# Web HMI Implementation Requirements (HTML / CSS / JavaScript) Derived from MIL-STD-1472H

> **Origin:** every requirement of [`MIL-STD-1472H-web-HMI-requirements.md`](MIL-STD-1472H-web-HMI-requirements.md) (extract of MIL-STD-1472H, 15 September 2020) is reported with its **original paragraph number** and followed by the implementation requirements derived from it.
> **Purpose:** translate human-factors requirements into requirements that can be verified on the **HTML, CSS and JavaScript entities** of a touch-screen web application.
> **Language:** English (HTML/CSS/JS technical terms and class, id, attribute and function names are kept as written in code).

## 1. How to read this document

- Every original requirement is presented as `#### <paragraph no.> – <title>` followed by a **summary** of the requirement (the full normative wording is in the extract) and a list of implementation requirements.
- Every implementation requirement has the identifier **`IMPL-<paragraph no.>-<nn>`**, a **technology** tag and an **area** tag:
  - Technology: `[HTML]` structure/semantics/ARIA attributes; `[CSS]` presentation, layout, states, animations; `[JS]` behavior, state, logic, events, browser APIs. A requirement involving several technologies carries several tags (e.g., `[HTML][JS]`).
  - Area: `[Header]`, `[Main]`, `[Footer]` or `[Global]` (cross-cutting: document, theme, common services).
- The words **shall / should / may** correspond to *shall / should / may* of the original requirement from which they derive; an original "should" requirement generates "should" implementation requirements unless stated otherwise.
- Numeric values (thresholds, times, sizes) are those of the standard; where the standard delegates the choice to the program, a configurable **design value** in `config.js` is indicated (see §2.5).
- Every original requirement has at least one implementation requirement; when a requirement is already satisfied by a common one, the cross-reference is explicit (e.g., "see `IMPL-5.1.1.8-01`").

## 2. Reference architecture of the web application

### 2.1 General constraints

| Constraint | Description |
|---|---|
| Application type | Web application (single page, HTML5 + CSS3 + JavaScript ES2020, **no mandatory framework dependency**; the use of a framework shall not alter the entities described below). |
| Primary input device | **Touch screen** (finger touch), with mouse/keyboard/pen supported as secondary inputs through Pointer Events. |
| Minimum resolution | **800 × 600 px** (CSS px, 4:3), in both landscape and portrait (600 × 800) orientation. No primary function shall require horizontal scrolling at 800 × 600. |
| Scalability | The layout scales **fluidly** from 800 × 600 to higher resolutions (e.g., 1024 × 768, 1280 × 800, 1920 × 1080, 2560 × 1440 and beyond) without loss of functions, legibility or minimum touch-target size. |
| Meta viewport | `<meta name="viewport" content="width=device-width, initial-scale=1">`; user zoom shall not be disabled. |

### 2.2 Application areas

The application consists of **three areas** arranged in a vertical grid that fills the whole viewport (`100dvh`). Only the `main panel` may scroll (and only vertically); header and footer always remain visible.

```
+-----------------------------------------------------------+
| HEADER      <header id="app-header">    height --header-h  |
+-----------------------------------------------------------+
| MAIN PANEL  <main id="main-panel">      1fr, inner scroll  |
|   #nav-region      (menu / navigation)                    |
|   #content-region  (views, forms, charts, tables)         |
|   #aux-region      (details / contextual help)            |
+-----------------------------------------------------------+
| FOOTER      <footer id="app-footer">    height --footer-h  |
+-----------------------------------------------------------+
```

| Area | HTML element | Expected content |
|---|---|---|
| **Header** | `<header id="app-header">` | `#app-title` (application name/current view, `<h1>`), `#mode-indicator` (current operating mode), `#control-station-indicator` (control station), `#user-menu` (user, role, logout), `#connection-indicator` (connection status), `#clock` (date/time), `#alert-summary` (counter of active/unacknowledged alarms) and quick controls (`#btn-home`, `#btn-back`, `#btn-help`, `#btn-display-settings`). |
| **Main panel** | `<main id="main-panel">` | `#nav-region` (`<nav>` with main menu and breadcrumb), `#content-region` (application views: forms, tables, charts, maps, dynamic displays, dialogs), `#aux-region` (side/contextual panels: details, inline help, message log), `#dialog-host` (host of modal `<dialog>` elements), `#toast-region` (transient messages), `#alert-region` (persistent alarm/warning banners, `role="alert"`). |
| **Footer** | `<footer id="app-footer">` | `#status-line` (system status and process feedback, `role="status"`), `#message-line` (last message/warning/error), `#progress-indicator`, `#function-keys` (contextual actions/touch shortcuts), `#session-timer` (session/inactivity time), `#app-version`. |

### 2.3 Scalable layout (CSS)

- Main layout: `body { display: grid; grid-template-rows: var(--header-h) minmax(0, 1fr) var(--footer-h); height: 100dvh; margin: 0; }`.
- Fluid units: `rem`, `%`, `fr`, `clamp()`, `minmax()`; the base font size is `html { font-size: clamp(16px, 0.9vw + 11px, 24px); }` so that at 800 × 600 it is ≥ 16 px and grows with the resolution.
- Reference breakpoints (min-width, in CSS px): **S** 800 (base, mobile-first), **M** 1024, **L** 1280, **XL** 1920, **XXL** 2560. Below 800 px width (not mandatory) the layout degrades to a single column keeping the same areas.
- Orientation: `@media (orientation: landscape|portrait)`; the `#nav-region`/`#content-region`/`#aux-region` layout has three columns in landscape ≥ M, two columns (nav + content, aux as a pop-up panel) at 800 px, and a single column in portrait.
- No text or control shall be reduced with `transform: scale()` or `zoom` to fit the resolution; scaling is obtained with `rem` and `clamp()`.

### 2.4 Common design tokens (CSS custom properties on `:root`)

| Token | Meaning / starting value |
|---|---|
| `--px-per-mm` | CSS pixels per physical millimeter; default `3.78` (96 dpi reference), adjustable from `config.js` according to the actual screen density. |
| `--touch-target-min` | `calc(15 * var(--px-per-mm))` – minimum actuation area (A) of controls (*Touch* table of 5.1.3.1.17: 15 × 15 mm minimum, 38 × 38 mm maximum, 16 × 16 mm preferred for keyboards). |
| `--touch-target-max` | `calc(38 * var(--px-per-mm))`. |
| `--touch-gap-min` | `calc(3 * var(--px-per-mm))` (minimum separation 3 mm; 5 mm with the *first contact* strategy); `--touch-gap-max`: 6 mm. |
| `--header-h`, `--footer-h` | `clamp(3.5rem, 8vh, 5rem)` and `clamp(2.5rem, 6vh, 4rem)`. |
| `--font-family-ui` | `Arial, Verdana, Helvetica, sans-serif` (standard sans-serif fonts). |
| `--fs-body`, `--fs-small`, `--fs-title` | Text sizes derived from the base font size (rem). |
| `--color-*` | Semantic palette: `--color-bg`, `--color-fg`, `--color-critical` (red), `--color-alarm` (orange), `--color-warning` (yellow), `--color-normal` (green), `--color-advisory` (cyan/blue), `--color-inactive` (gray), `--color-focus`, `--color-selected`. Every semantic color is always accompanied by an icon/shape/text (redundant coding). |
| `--contrast-*` | Themes `theme-light`, `theme-dark`, `theme-high-contrast` applied with `data-theme` on `<html>`; text/background contrast ratio ≥ 6:1 (target ≥ 10:1). |
| `--z-*` | Stacking levels: `--z-header`, `--z-dialog`, `--z-alert`, `--z-toast`. |

### 2.5 Common JavaScript modules and configuration

The following JS entities are *common services* referenced by the implementation requirements (ES modules `type="module"`; central immutable state with `CustomEvent` events):

| Module | Responsibility |
|---|---|
| `config.js` | Configurable design values (response times, session/inactivity timeouts, thresholds, `pxPerMm`, language, roles, color-coding tables). |
| `state.js` | Application state (operating mode, current view, user/role, control station, connection, alarms). Single source of truth for the displays. |
| `feedback.js` | `acknowledge(el)`, `setStatus(msg, level)`, `showProgress()`, `beep(type)`: visual/audio/haptic (`navigator.vibrate`) feedback within the Table V times. |
| `notify.js` | Message/warning/alarm queue with priority, acknowledgment, history (`#alert-region`, `#toast-region`, `#message-line`). |
| `dialog.js` | Management of modal/non-modal dialogs on `<dialog>`, confirmations, cancellation, closure. |
| `validate.js` | Validation of input data (type, format, range), error messages and correction. |
| `auth.js` | Login/logout, roles, passwords, account lockout, inactivity timeout. |
| `nav.js` | Navigation, history, back/home, breadcrumb, shortcuts. |
| `prefs.js` | User preferences (theme, text size, brightness/contrast, audio, orientation) in `localStorage`. |
| `i18n.js` | Localized texts, units of measure and date/time/number formats. |
| `net.js` | Communication with the back end (`fetch`/WebSocket), connection status, retries, data transmission. |
| `log.js` | Logging of user actions and security events (forwarded to the server). |

### 2.6 Cross-cutting conventions

- **Accessibility**: semantic HTML, WAI-ARIA (`role`, `aria-live`, `aria-label`, `aria-describedby`, `aria-invalid`, `aria-current`), logical tab order, `:focus-visible` always visible.
- **Input**: Pointer Events (`pointerdown/up/cancel`) for touch/mouse/pen; *last contact* actuation strategy (action on `pointerup` on the target) unless otherwise stated; `touch-action: manipulation` on controls; no hover-only functions; no multi-finger gesture as the only means.
- **Response times (Table V, Appendix A of the extract)**: touch/click response ≤ 0.1 s; character echo ≤ 0.2 s; error ≤ 0.2 s; local update ≤ 0.5 s; page change ≤ 1.0 s; update from host ≤ 2.0 s; update from file ≤ 10 s. Beyond 1 s a progress indicator is shown.
- **Visual state of controls**: classes/attributes `:hover` (mouse only), `:focus-visible`, `:active`, `[aria-pressed]`, `[aria-selected]`, `[aria-current]`, `[disabled]`/`[aria-disabled]`, `.is-selected`, `.is-busy`.
- **Acceptance tests**: every implementation requirement can be verified by *inspection* of the DOM/CSS (I), *automated test* (T, e.g., Playwright/Jest) or manual *demonstration* (D) on 800 × 600, 1024 × 768 and 1920 × 1080 viewports.

## 3. Implementation requirements per original requirement

Sections 3.1–3.16 mirror sections 1–16 of the requirements extract. The document covers all **866** original requirements of the extract and derives **957** implementation requirements (`IMPL-…`).

### 3.1 General design principles

Cross-cutting criteria applicable to any HMI, including a web HMI; for requirements of a more procedural/organizational nature, verifiable architectural constraints and review criteria are derived.

#### 4.5.2 – Application
*Summary (shall/should/may):* The criteria of the standard shall be applied to the design of hardware, software, systems, equipment and facilities with a human interface or interaction, including operation, maintenance, training and support.

- **IMPL-4.5.2-01** `[HTML][JS]` `[Global]` All application views (operation, maintenance, configuration, help/training) shall reuse the same areas (`#app-header`/`#main-panel`/`#app-footer`) and the same common modules (`state.js`, `config.js`) from §2, without alternative UI patterns.
- **IMPL-4.5.2-02** `[JS]` `[Global]` Review criterion/acceptance test (I): a code review checklist that verifies, for every new view, conformance with sections 2.1-2.6 before merging.

#### 4.6.1 – Fail-safe design
*Summary:* A fail-safe design shall be guaranteed in areas where a failure could cause damage to equipment, injury to personnel, or accidental actuation of critical equipment.

- **IMPL-4.6.1-01** `[JS]` `[Main]` `validate.js` shall require explicit confirmation via `dialog.js` (modal) before performing destructive/irreversible actions on critical functions, with the "cancel" option preselected.
- **IMPL-4.6.1-02** `[JS]` `[Global]` `net.js` shall handle communication errors by bringing the application state to a known safe mode (read-only fallback via `state.js`) and report it in `#alert-region`, instead of performing indeterminate actions.

#### 4.6.2 – Remote and local control
*Summary:* For critical control/monitoring functions, normal and backup functions shall exist, switchable at any time according to a defined protocol; the station holding control shall be indicated to both the normal and the backup station.

- **IMPL-4.6.2-01** `[HTML][JS]` `[Header]` `#control-station-indicator` shall show in real time which station (normal/backup) holds control, updated by `state.js` via `CustomEvent` on control handover.
- **IMPL-4.6.2-02** `[JS]` `[Global]` `state.js`/`nav.js` shall expose a `handoverControl()` function that allows the backup station to take control at any time, logged by `log.js`.
- **IMPL-4.6.2-03** `[HTML][CSS]` `[Header]` The "in control"/"standby" state in `#control-station-indicator` shall be redundant (text + icon + semantic color `--color-*`), with no perceptual ambiguity.

#### 4.7.1 – Equipment simplicity
*Summary:* The equipment shall represent the simplest design consistent with the functional requirements and expected operating conditions.

- **IMPL-4.7.1-01** `[HTML][JS]` `[Global]` Architectural criterion/acceptance test (I): every new view/component shall reuse the entities already defined (§2.2, §2.5) and avoid unnecessary framework dependencies (see §2.1).
- **IMPL-4.7.1-02** `[HTML]` `[Main]` Forms in `#content-region` shall expose only the controls needed for the current task, with advanced options grouped in collapsible sections (`<details>`) not visible by default.

#### 4.9.1 – System and personnel safety factors
*Summary:* The design shall reflect system and personnel safety factors, minimizing human error under alert conditions, operational stress, or emergency.

- **IMPL-4.9.1-01** `[JS]` `[Global]` `notify.js` shall give higher priority and persistence (banner `#alert-region` without auto-dismiss) to critical/alarm-level messages than to informational ones during emergency conditions.
- **IMPL-4.9.1-02** `[JS]` `[Main]` `validate.js`/`dialog.js` shall require two-step confirmation for critical commands even under high-load conditions (multiple active alarms), to minimize human error.

#### 4.13.1 – Automation of functions
*Summary:* Functions shall be automated only to achieve greater overall system effectiveness, efficiency, reliability, simplicity, economy and safety, not merely to rely on human performance alone.

- **IMPL-4.13.1-01** `[JS]` `[Global]` Design/review criterion (I): every automation introduced in `state.js` shall be documented with its rationale (effectiveness/efficiency/safety), verifiable in review.
- **IMPL-4.13.1-02** `[HTML][JS]` `[Main]` Automated functions shall expose an explicit activation/deactivation control visible in `#content-region` (`[aria-pressed]`), with no implicit activations.

#### 4.13.2 – Human involvement
*Summary:* Regardless of the level of automation, the design shall ensure that the user retains command, remains involved in ongoing operations, and is kept informed to maintain situational awareness.

- **IMPL-4.13.2-01** `[HTML][JS]` `[Main]` Every automated function shall publish its current status/actions via `state.js` to `#aux-region` (the "automated activities" panel), with `aria-live="polite"`.
- **IMPL-4.13.2-02** `[JS]` `[Global]` Automated functions shall expose an override/stop command that is always reachable by keyboard and touch (`--touch-target-min`), handled by `feedback.js` with immediate confirmation.

#### 4.13.3 – Automated function characteristics
*Summary:* Automated functions shall minimize surprises, offer an adequate range of options, monitor user actions to minimize/resist/tolerate errors, and be cancellable by the user.

- **IMPL-4.13.3-01** `[JS]` `[Global]` `notify.js` shall notify in advance (configurable lead time in `config.js`) every relevant automatic state change, avoiding silent transitions.
- **IMPL-4.13.3-02** `[JS]` `[Main]` `validate.js` shall intercept user actions that conflict with an active automation and request confirmation before proceeding.
- **IMPL-4.13.3-03** `[HTML][JS]` `[Main]` Every automated function shall expose a "Cancel/Override" button (≥ `--touch-target-min`) always active during execution.

#### 4.13.4 – Indication of operating mode
*Summary:* A clear indication of the currently selected operating mode shall be provided at all times.

- **IMPL-4.13.4-01** `[HTML][JS]` `[Header]` `#mode-indicator` shall always show (not on request) the current operating mode, updated by `state.js` via the `modechange` event.
- **IMPL-4.13.4-02** `[CSS]` `[Header]` `#mode-indicator` shall encode the mode with text + semantic color (`--color-*`) + icon, never color alone, ensuring double coding.

#### 4.14 – Functional use of color
*Summary:* Where not in conflict with specified color codes, colors used for functional purposes shall be accessible to users with color vision deficiencies.

- **IMPL-4.14-01** `[CSS]` `[Global]` Every functional use of color (`--color-critical`/`--color-alarm`/`--color-warning`/`--color-normal`/`--color-advisory`/`--color-inactive`) shall be accompanied by an icon/shape/text (double coding), as per §2.4.
- **IMPL-4.14-02** `[JS][CSS]` `[Global]` `prefs.js` shall offer a "color blindness" mode with an alternative palette suitable for deuteranopia/protanopia, selectable and persisted in `localStorage`.

#### 4.16 – System integration
*Summary:* The design shall be compatible with all systems the user shall use, maintain or support.

- **IMPL-4.16-01** `[JS]` `[Global]` `net.js` shall expose a documented integration interface based on web standards (`fetch`/WebSocket, JSON), without non-standard proprietary dependencies.
- **IMPL-4.16-02** `[JS]` `[Global]` Acceptance test (D): demonstration of interoperability (login, data exchange) with the reference external systems listed in `config.js` before release.

#### 5.17.1.1 – Functional interface
*Summary:* Software programs and equipment interfaces shall provide a functional interface between the system for which they are designed and its users.

- **IMPL-5.17.1.1-01** `[HTML][JS]` `[Main]` `#content-region` shall expose, for each provided system function, a dedicated view/form connected to `state.js`, with no system function lacking a UI control/display.

#### 5.17.1.2 – Compatible with personnel
*Summary:* Interfaces shall be compatible with the work performed and with the cognitive processes of personnel.

- **IMPL-5.17.1.2-01** `[HTML][JS]` `[Main]` The navigation flow (`nav.js`, breadcrumb in `#nav-region`) shall mirror the sequence of the user's operational tasks (task-based IA), verified through testing with users/domain experts (D).
- **IMPL-5.17.1.2-02** `[JS]` `[Global]` `i18n.js` shall use terminology and units of measure from the user's operational domain, configurable by role/profile.

#### 5.17.1.3 – Human performance
*Summary:* The interface should minimize conditions that may degrade human performance or contribute to human error.

- **IMPL-5.17.1.3-01** `[JS]` `[Main]` `validate.js` should flag in real time (inline, not only on submit) inputs that may lead to error, with corrective messages (see `IMPL-5.1.2.1.6.11-01`).
- **IMPL-5.17.1.3-02** `[CSS][HTML]` `[Global]` Controls with a high risk of error (destructive actions) should be visually distinguished (color, separate position) from routine controls.

#### 5.17.1.4 – Display content
*Summary:* Interfaces provide a functional interface between system and users; they should optimize compatibility with personnel and minimize conditions that degrade performance or contribute to error.

- **IMPL-5.17.1.4-01** `[HTML][JS]` `[Main]` See `IMPL-5.17.1.1-01` and `IMPL-5.17.1.3-01`; in addition, `#content-region` shall show only data relevant to the current task (progressive disclosure), configurable by `state.js` based on the user's role.

### 3.2 Feedback, system status and response time

The reference values are those in Table V (Appendix A, reported in §2.6): touch/click response ≤ 0.1 s; character echo ≤ 0.2 s; error ≤ 0.2 s; local update ≤ 0.5 s; page change ≤ 1.0 s; update from host ≤ 2.0 s; update from file ≤ 10 s; beyond 1 s a progress indicator shall be shown.

#### 5.1.1.8 – Feedback
*Summary:* There shall be no perceptible delay between a change in a controlled/monitored system condition and its indication on a display.

- **IMPL-5.1.1.8-01** `[JS]` `[Global]` `state.js` shall propagate every monitored state change to the affected displays within the "local update" threshold (≤ 0.5 s, Table V) via synchronous `CustomEvent`s.
- **IMPL-5.1.1.8-02** `[JS]` `[Header]` `net.js` shall propagate updates received from the host within ≤ 2.0 s (Table V) and report any longer delay as an anomaly in `#connection-indicator`.

#### 5.1.1.8.1 – Unavoidable time lag
*Summary:* If a delay between control actuation and the final system state is unavoidable, the system shall provide immediate feedback on the process and direction of the parameter change.

- **IMPL-5.1.1.8.1-01** `[JS][HTML]` `[Main]` `feedback.js` shall immediately show (≤ 0.1 s, Table V) an "in progress" indicator with the direction/trend of the parameter (e.g., arrow, bar) whenever a command has a non-instantaneous effect.

#### 5.1.1.8.2 – Feedback indication
*Summary:* Feedback shall indicate without ambiguity, uncertainty or error that the control is correctly actuated, that the desired response is obtained, and when it is complete.

- **IMPL-5.1.1.8.2-01** `[JS]` `[Global]` `feedback.js:acknowledge(el)` shall apply, in sequence, distinct and unambiguous states (`.is-busy` → `.is-success`/`.is-error` → final state), each with text + icon, with no ambiguous intermediate states.

#### 5.1.1.8.3 – Critical control functions
*Summary:* Critical control functions, such as keyboard entry, shall provide feedback before submission to ensure that the entry is free of errors and corresponds to what is intended.

- **IMPL-5.1.1.8.3-01** `[JS][HTML]` `[Main]` `dialog.js` shall present a confirmation with a summary of the entered value/command before submission, for every control marked `data-critical="true"`.

#### 5.1.2.1.1 – Relationship
*Summary:* The relationships between a control and its associated display, and vice versa, shall be immediately evident and unambiguous to the user.

- **IMPL-5.1.2.1.1-01** `[HTML][CSS]` `[Main]` Every control and its associated display shall share a container (`<fieldset>`/`<section>` with `aria-labelledby`) and visual proximity in `#content-region`.

#### 5.1.2.1.3 – Design
*Summary:* Control/display relationships shall be made evident through proximity, grouping similarity, coding, framing, labeling and similar techniques.

- **IMPL-5.1.2.1.3-01** `[CSS][HTML]` `[Main]` Functionally related groups shall use a common border/`<fieldset>`, explicit `<label>`s, and the same color-coding class (`--color-*`, §2.4).

#### 5.1.2.1.4 – Complexity and precision
*Summary:* The complexity and precision required to manipulate controls and monitor displays shall be consistent with the precision required by the system.

- **IMPL-5.1.2.1.4-01** `[HTML][JS]` `[Main]` `validate.js` shall define, for every numeric field, `step`/`min`/`max` values consistent with the operational precision required by the domain, without excess decimal digits.

#### 5.1.2.1.5 – User ability
*Summary:* The complexity/precision of controls and displays shall not exceed the user's ability to discriminate details or manipulate controls under the expected dynamic conditions.

- **IMPL-5.1.2.1.5-01** `[CSS]` `[Global]` Touch targets shall respect `--touch-target-min` (15×15 mm) even in higher information-density views, without reducing them below the threshold.
- **IMPL-5.1.2.1.5-02** `[JS]` `[Main]` `prefs.js` shall allow a "reduced precision" profile (enlarged text/controls) without breaking the layout (see §2.3).

#### 5.1.2.1.6.1 – Use
*Summary:* Feedback presenting status, confirmation and input-verification information shall be provided throughout the interaction with the system.

- **IMPL-5.1.2.1.6.1-01** `[JS][HTML]` `[Footer]` `#status-line` (`role="status"`) shall report, via `feedback.js:setStatus(msg, level)`, the status/confirmation of every relevant user action.

#### 5.1.2.1.6.2 – System status
*Summary:* Users shall always be provided with information on system status regarding operating modes and availability, either automatically or on request.

- **IMPL-5.1.2.1.6.2-01** `[HTML][JS]` `[Header]` `#mode-indicator` and `#connection-indicator` shall continuously show operating mode and availability; `#btn-help` shall allow the detail to be recalled on demand in `#aux-region`.

#### 5.1.2.1.6.3 – Computer response
*Summary:* Every user input shall produce a perceptible and consistent response from the computer; if there is no intentional visual feedback for an invalid input, an alternative form (e.g., sound) shall be produced to ensure its recognition.

- **IMPL-5.1.2.1.6.3-01** `[JS]` `[Global]` `feedback.js` shall generate a perceptible response (visual and/or `beep('error')`) within ≤ 0.1 s of every interaction (Table V).
- **IMPL-5.1.2.1.6.3-02** `[JS]` `[Global]` When an invalid input does not produce visual feedback, `feedback.js:beep('invalid')` shall act as an alternative channel, respecting the audio preferences in `prefs.js`.

#### 5.1.2.1.6.4 – System response time
*Summary:* Maximum response times for real-time systems (e.g., fire control, command and control) shall not exceed the values in Table V; for teleoperations, acceptable times should be specified by the acquisition program.

- **IMPL-5.1.2.1.6.4-01** `[JS]` `[Global]` `config.js` shall define the maximum times of Table V (0.1/0.2/0.2/0.5/1.0/2.0/10 s) as thresholds verifiable by automated test (T).
- **IMPL-5.1.2.1.6.4-02** `[JS]` `[Global]` For teleoperation views, `net.js` shall expose a threshold configurable separately in `config.js` (design value), distinct from the standard ones in Table V.

#### 5.1.2.1.6.4.1 – Non-real-time systems
*Summary:* Non-real-time systems may allow relaxed response times.

- **IMPL-5.1.2.1.6.4.1-01** `[JS]` `[Global]` `config.js` shall allow individual views/functions to be marked as `realtime: false`, applying relaxed response thresholds (design value) relative to Table V.

#### 5.1.2.1.6.4.2 – Existing or predecessor systems
*Summary:* Response times for real-time and non-real-time systems shall not exceed the response time of the existing or equivalent predecessor system.

- **IMPL-5.1.2.1.6.4.2-01** `[JS]` `[Global]` `config.js` shall record, per function, the response time of the predecessor system (if known) as the limit beyond which the automated performance test (T) fails.

#### 5.1.2.1.6.4.3 – Response time greater than 1 second
*Summary:* If the computer's response time will exceed 1 second, the user shall receive a message indicating that the system is processing (for remote automated systems, see 5.12).

- **IMPL-5.1.2.1.6.4.3-01** `[JS][HTML]` `[Footer]` `net.js`/`feedback.js` shall show in `#status-line`/`#progress-indicator` the message "processing in progress" as soon as an operation exceeds 1.0 s without completing.

#### 5.1.2.1.6.5 – Task performance time
*Summary:* The time to accurately complete a standard time-sensitive action/sequence (including system response time) shall not exceed that for the same actions on the existing or equivalent predecessor system.

- **IMPL-5.1.2.1.6.5-01** `[JS]` `[Global]` Acceptance test (T): for the critical tasks listed in `config.js`, an automated Playwright test shall measure the end-to-end time and compare it with the predecessor system's limit recorded in `config.js`.

#### 5.1.2.1.6.6 – Time-consuming processes
*Summary:* The system shall provide a warning message when a command that will require a long time or intensive resource usage is invoked.

- **IMPL-5.1.2.1.6.6-01** `[JS][HTML]` `[Main]` `dialog.js` shall show an advance warning (estimated time, resources) before starting operations marked `data-long-running="true"` in `config.js`.

#### 5.1.2.1.6.7 – Aborting time-consuming processes
*Summary:* Users shall be provided with an option to abort time-consuming processes.

- **IMPL-5.1.2.1.6.7-01** `[HTML][JS]` `[Footer]` `#progress-indicator` shall include a "Cancel" button (≥ `--touch-target-min`) connected to an `abort()` function exposed by `net.js`/`state.js`.

#### 5.1.2.1.6.8 – Stand-by
*Summary:* When system operation requires the user to wait, an appropriate message or icon shall be displayed until interaction is possible again.

- **IMPL-5.1.2.1.6.8-01** `[JS][HTML]` `[Footer]` `feedback.js:showProgress()` shall activate a "please wait" message/icon in `#status-line`/`#progress-indicator` and set `aria-busy="true"` on the blocked controls.

#### 5.1.2.1.6.8.1 – Delays in excess of 1 second
*Summary:* When the delay exceeds 1 second, the user shall be informed.

- **IMPL-5.1.2.1.6.8.1-01** `[JS]` `[Footer]` See `IMPL-5.1.2.1.6.4.3-01`: the same 1.0 s threshold in `config.js` shall trigger the message in `#status-line`.

#### 5.1.2.1.6.8.2 – Delays in excess of 10 seconds
*Summary:* For delays exceeding 10 seconds, a countdown display shall show the remaining delay time.

- **IMPL-5.1.2.1.6.8.2-01** `[JS][HTML]` `[Footer]` Beyond the 10 s threshold (Table V, "update from file") defined in `config.js`, `#progress-indicator` shall show a countdown (mm:ss) updated every second by `feedback.js`.

#### 5.1.2.1.6.9 – Startup
*Summary:* When the system is not immediately available after startup, it shall provide feedback on the average response time or known unavailability periods, and the keyboard/pointer shall be disabled until startup is complete.

- **IMPL-5.1.2.1.6.9-01** `[JS][HTML]` `[Main]` During startup, the application shall show a loading overlay with the estimated average time (from `config.js`) and set `inert`/`disabled` on interactive controls until `state.js` signals `app:ready`.

#### 5.1.2.1.6.10 – Input confirmation
*Summary:* Confirmation of inputs shall not cause the removal of displayed data.

- **IMPL-5.1.2.1.6.10-01** `[JS]` `[Main]` `dialog.js:confirm()` shall not clear or hide the values of the underlying form; entered data remains visible in `#content-region` after confirmation.

#### 5.1.2.1.6.11 – User input rejection
*Summary:* If the system rejects a user input, feedback shall be provided indicating the reason for rejection and the required corrective action.

- **IMPL-5.1.2.1.6.11-01** `[JS][HTML]` `[Main]` `validate.js` shall set `aria-invalid="true"` and an explicit message linked via `aria-describedby` indicating the reason for rejection and the required correction.

#### 5.1.2.1.6.12 – Highlighted option selection
*Summary:* Any message or data selected as an option or input to the system shall be highlighted to indicate recognition by the system.

- **IMPL-5.1.2.1.6.12-01** `[CSS][HTML]` `[Main]` Selected elements shall receive the class `.is-selected` and/or `[aria-selected="true"]` with a distinctive style (`--color-selected`) as soon as they are recognized by the system.

#### 5.1.2.1.6.13 – Process outcome
*Summary:* When a control process or sequence is completed or interrupted by the system, a positive indication of the outcome and of the requirements for the user's next action shall be presented.

- **IMPL-5.1.2.1.6.13-01** `[JS][HTML]` `[Footer]` `notify.js` shall send to `#message-line`/`#toast-region` an outcome message (success/interruption) with any required next action.

#### 5.1.2.1.6.14 – Feedback message content
*Summary:* Users shall not be required to translate feedback messages using a reference system or codebooks.

- **IMPL-5.1.2.1.6.14-01** `[JS]` `[Global]` `i18n.js` shall provide feedback message text in full natural language (no unresolved code/abbreviation), retrievable via translation key.

#### 5.1.2.1.6.15 – Abbreviations
*Summary:* Abbreviations should be avoided in feedback messages unless they are well known to all potential users.

- **IMPL-5.1.2.1.6.15-01** `[JS]` `[Global]` Review criterion (I): the text dictionary of `i18n.js` shall be checked in review for the absence of non-standard abbreviations in feedback messages.

#### 5.1.2.3.1 – Lack of ambiguity
*Summary:* Display indicators shall clearly and unambiguously direct and guide the appropriate control response.

- **IMPL-5.1.2.3.1-01** `[HTML][CSS]` `[Main]` Every indicator that requires an action (alarm, invalid field) shall include an explicit text label of the required action, not only an icon/color.

#### 5.1.2.3.2 – Response to control movements
*Summary:* A display's response to control movements shall be consistent, predictable and compatible with the user's expectations.

- **IMPL-5.1.2.3.2-01** `[JS]` `[Main]` Controls with an associated display (slider, stepper) shall update the display monotonically and consistently with the direction of the gesture (`pointermove`), with no reversals or jumps.

#### 5.1.2.3.3 – Display response time
*Summary:* There shall be no perceptible delay between a change in a controlled/monitored real-time condition and its indication on the display (see 5.1.2.1.6.4).

- **IMPL-5.1.2.3.3-01** `[JS]` `[Global]` See `IMPL-5.1.1.8-01`; for real-time views, `state.js` shall update the associated display within the "local update" threshold of ≤ 0.5 s in Table V.

#### 5.1.2.3.4 – Time lag
*Summary:* The delay between the system's response to a control input and the display's presentation of that response shall be minimized, consistent with safe and effective operation.

- **IMPL-5.1.2.3.4-01** `[JS]` `[Global]` Acceptance test (T): `net.js` shall measure and log (`log.js`) the command→display latency for critical functions, with an alarm threshold configurable in `config.js`.

#### 5.17.9.1 – Feedback
*Summary:* Feedback presenting status, confirmation and input verification shall be provided during interaction with the system (see 5.1.2.1.6.1).

- **IMPL-5.17.9.1-01** `[JS][HTML]` `[Footer]` See `IMPL-5.1.2.1.6.1-01`; `#status-line` shall remain the single source also for cybersecurity/administration interfaces (login, role management).

#### 5.17.9.2 – System status
*Summary:* Information on system status regarding operating modes and availability shall be accessible to users, either automatically or on request.

- **IMPL-5.17.9.2-01** `[HTML][CSS]` `[Header]` `#app-header` (with `#mode-indicator`, `#connection-indicator`) shall remain visible even when a modal dialog is open in `#dialog-host`, since the grid layout (§2.2) keeps header and footer always present.

#### 5.17.9.3 – Computer responses
*Summary:* Every user input shall produce a perceptible and consistent response from the computer (see 5.1.2.1.6.3).

- **IMPL-5.17.9.3-01** `[JS]` `[Global]` See `IMPL-5.1.2.1.6.3-01`.

#### 5.17.9.4 – Alternative form
*Summary:* If feedback is not presented visually, an alternative form of feedback (e.g., sound) shall be provided.

- **IMPL-5.17.9.4-01** `[JS]` `[Global]` See `IMPL-5.1.2.1.6.3-02`; `feedback.js:beep(type)` shall also cover cases of non-visual feedback in cybersecurity screens (e.g., account lockout).

#### 5.17.9.5 – System response time
*Summary:* Maximum response times for critical systems (fire control, command and control) shall not exceed the values in Table V.

- **IMPL-5.17.9.5-01** `[JS]` `[Global]` See `IMPL-5.1.2.1.6.4-01`.

#### 5.17.9.5.1 – Non-critical systems
*Summary:* Non-critical systems may allow relaxed response times.

- **IMPL-5.17.9.5.1-01** `[JS]` `[Global]` See `IMPL-5.1.2.1.6.4.1-01`.

#### 5.17.9.5.2 – Existing or predecessor systems
*Summary:* Response times for critical and non-critical systems shall not exceed those of existing or equivalent predecessor systems.

- **IMPL-5.17.9.5.2-01** `[JS]` `[Global]` See `IMPL-5.1.2.1.6.4.2-01`.

#### 5.17.9.5.3 – System processing indication
*Summary:* When the computer's response times will exceed 1 second, the system shall provide a message indicating that it is processing (for remote automated systems, see 5.12).

- **IMPL-5.17.9.5.3-01** `[JS][HTML]` `[Footer]` See `IMPL-5.1.2.1.6.4.3-01`.

#### 5.17.9.6 – Task performance time
*Summary:* The time to accurately complete a time-sensitive action/sequence (including response times) shall not exceed that of existing or equivalent predecessor systems.

- **IMPL-5.17.9.6-01** `[JS]` `[Global]` See `IMPL-5.1.2.1.6.5-01`.

#### 5.17.9.7 – Time-consuming processes
*Summary:* The system shall provide warning information when a command that will require a long time or intensive resource usage is invoked.

- **IMPL-5.17.9.7-01** `[JS][HTML]` `[Main]` See `IMPL-5.1.2.1.6.6-01`.

#### 5.17.9.8 – Aborting time-consuming processes
*Summary:* The system shall provide users with an option to abort time-consuming processes.

- **IMPL-5.17.9.8-01** `[HTML][JS]` `[Footer]` See `IMPL-5.1.2.1.6.7-01`.

#### 5.17.9.9 – System processing
*Summary:* When system processing requires the user to wait before acting further, an appropriate message or icon shall be displayed until interaction is possible again.

- **IMPL-5.17.9.9-01** `[JS][HTML]` `[Footer]` See `IMPL-5.1.2.1.6.8-01`.

#### 5.17.9.9.1 – Status message
*Summary:* When the expected delay exceeds 1 second, the system shall provide a status message.

- **IMPL-5.17.9.9.1-01** `[JS]` `[Footer]` See `IMPL-5.1.2.1.6.8.1-01`.

#### 5.17.9.9.2 – Progress indicator
*Summary:* For delays exceeding 10 seconds, the display shall provide a progress indicator.

- **IMPL-5.17.9.9.2-01** `[JS][HTML]` `[Footer]` See `IMPL-5.1.2.1.6.8.2-01`; `#progress-indicator` shall also show a percentage progress bar when the total amount of work is known.

#### 5.17.9.10 – User input rejection
*Summary:* If the system rejects a user input, feedback shall be provided indicating the reason for rejection and the required corrective action.

- **IMPL-5.17.9.10-01** `[JS][HTML]` `[Main]` See `IMPL-5.1.2.1.6.11-01`.

#### 5.17.9.11 – Highlighted option selection
*Summary:* Any element selected as an option or input to the system shall be highlighted to indicate its recognition by the system.

- **IMPL-5.17.9.11-01** `[CSS][HTML]` `[Main]` See `IMPL-5.1.2.1.6.12-01`.

#### 5.17.9.12 – Process outcome
*Summary:* When a control process or a sequence of related actions is completed or interrupted by the system, the system shall provide an indication of the outcome to the user.

- **IMPL-5.17.9.12-01** `[JS][HTML]` `[Footer]` See `IMPL-5.1.2.1.6.13-01`.

#### 5.17.9.13 – Subsequent actions
*Summary:* Following an interrupted action, the system shall provide every requirement for the user's next action.

- **IMPL-5.17.9.13-01** `[JS][HTML]` `[Footer]` `notify.js` shall include in the outcome message (see `IMPL-5.1.2.1.6.13-01`) an explicit list of the required next actions when the process has been interrupted, not only upon completion.

#### 5.17.9.14 – Feedback message content
*Summary:* Feedback messages shall be explicit and informative.

- **IMPL-5.17.9.14-01** `[JS]` `[Global]` `i18n.js` shall ensure that every feedback message includes cause and effect (what happened, why, what to do), not just a status code.

#### 5.17.9.14.1 – Understandable
*Summary:* Feedback shall not require a reference system or a codebook to be understood.

- **IMPL-5.17.9.14.1-01** `[JS]` `[Global]` See `IMPL-5.1.2.1.6.14-01`.

#### 5.17.9.14.2 – Non-standard abbreviations
*Summary:* Non-standard abbreviations should be avoided in feedback messages.

- **IMPL-5.17.9.14.2-01** `[JS]` `[Global]` See `IMPL-5.1.2.1.6.15-01`.

### 3.3 Cybersecurity: access control, authentication, logon/logoff, data protection

The following requirements concern the application's login, session and administration screens (SA = system administrator; ISSO = information systems security officer). They are implemented via `auth.js` (login/logout, roles, account lockout, inactivity), `state.js` (active user/role/session), `notify.js`/`log.js` (notifications and audit) and dedicated views in `#content-region`/`#dialog-host`.

#### 5.16 – Cybersecurity
*Summary (shall):* Cybersecurity considerations (DoDI 8510.01, CNSSI 1253, NIST SP 800-53) concern access control and user identification/authentication; the relevant human-interaction elements shall be identified, analyzed and implemented in the risk management process.

- **IMPL-5.16-01** `[JS]` `[Global]` `auth.js` and `log.js` shall implement the access control/identification/authentication functions as central services invoked by all views requiring authorization, without duplicating their logic in individual application modules.
- **IMPL-5.16-02** `[JS]` `[Global]` Security criteria (roles, lockout thresholds, session duration) shall be design values configurable in `config.js`, reviewable without modifying the application code.
- **IMPL-5.16-03** `[HTML]` `[Global]` Every view with sensitive data shall declare in its markup the `data-requires-role` attribute, verified by `auth.js` before rendering the content in `#content-region`.

#### 5.16.1.1 – User access
*Summary (shall):* Role-based access control shall be used to manage user access.

- **IMPL-5.16.1.1-01** `[JS]` `[Global]` `auth.js` shall maintain in `state.js` the set of roles/permissions of the authenticated user, and `nav.js` shall hide/disable (`[disabled]`/`[aria-disabled]`) menu items not authorized for the current role.
- **IMPL-5.16.1.1-02** `[HTML]` `[Global]` Every protected action (button, link, `#function-keys` entry) shall have a `data-role-required` attribute checked client-side and revalidated server-side before execution.

#### 5.16.1.2 – Access notifications
*Summary (should):* SA and ISSO should receive a notification when access authorizations are inconsistent with restrictions.

- **IMPL-5.16.1.2-01** `[JS]` `[Global]` `notify.js` should generate a priority-queued alert (`warning` level) to the SA/ISSO roles when `auth.js` detects an inconsistency between the assigned role and access restrictions, visible in `#alert-summary`.

#### 5.16.1.3.1 – Linked user profiles
*Summary (shall):* If a user has multiple roles in the system, their profile shall be linked to all roles and permissions.

- **IMPL-5.16.1.3.1-01** `[JS]` `[Global]` `state.js` shall represent the user profile as an object with a `roles[]` array linked to a single `userId`, consulted by `auth.js` to resolve the effective permissions.
- **IMPL-5.16.1.3.1-02** `[HTML]` `[Header]` `#user-menu` shall list all the roles associated with the active profile, not only the one in use in the current session.

#### 5.16.1.3.2 – User role login
*Summary (should):* The user should be informed under which role they are logged in.

- **IMPL-5.16.1.3.2-01** `[HTML][CSS]` `[Header]` `#user-menu` should permanently and legibly display the current role (e.g., a text badge next to the username), updated by `state.js` on every role change.

#### 5.16.1.4 – Visible active accounts
*Summary (shall):* For the entire duration of the session, the user shall be able to see which system account is active.

- **IMPL-5.16.1.4-01** `[HTML][JS]` `[Header]` `#user-menu` shall persistently display the identifier of the active account, resynchronized by `state.js` on every session change; no interaction shall be necessary to view it.

#### 5.16.1.5 – Access to roles and permissions
*Summary (shall):* SA and ISSO shall have access to the system's role and permission matrices.

- **IMPL-5.16.1.5-01** `[HTML][JS]` `[Main]` An administration view in `#content-region` (accessible only to SA/ISSO via `data-role-required`) shall present the roles×permissions matrix as a browsable table, populated by `auth.js`.

#### 5.16.1.6 – Separation of duties
*Summary (should):* SA and ISSO should have the roles and associated duties needed to maintain awareness and segregation of organizational duties.

- **IMPL-5.16.1.6-01** `[HTML]` `[Main]` The administrative view referred to in IMPL-5.16.1.5-01 should include, for each role, the list of associated duties/functions, to highlight any overlaps of responsibility.

#### 5.16.1.7 – Automated restrictions
*Summary (shall):* Invalid logins shall trigger automatic access restrictions according to the system's cybersecurity plan.

- **IMPL-5.16.1.7-01** `[JS]` `[Global]` `auth.js` shall count failed login attempts per account (threshold configurable in `config.js`) and automatically apply account lockout when the threshold is exceeded, logging the event with `log.js`.

#### 5.16.1.8 – Automated notifications
*Summary (should):* The SA should receive an automatic notification when a user's access has been restricted by the system.

- **IMPL-5.16.1.8-01** `[JS]` `[Global]` When `auth.js` locks an account (see IMPL-5.16.1.7-01), `notify.js` should send an automatic notification to the SA roles, visible in `#alert-summary`/`#toast-region`.

#### 5.16.1.9 – Monitoring logs
*Summary (shall):* The SA shall be able to determine who is connected to the system in real time or in the past (via system logs).

- **IMPL-5.16.1.9-01** `[HTML][JS]` `[Main]` An administrative view shall show the list of currently connected users (from `state.js`/`net.js`) and shall offer a search/filter over the historical access logs produced by `log.js`.

#### 5.16.1.10 – Unlock an account
*Summary (shall/should):* The SA shall be able to unlock an account; they should be able to do so remotely.

- **IMPL-5.16.1.10-01** `[HTML][JS]` `[Main]` The administrative view shall include, for every locked account, a `.btn-unlock-account` button (`min-block-size: var(--touch-target-min)`) that invokes `auth.js` to revoke the lock via `net.js`, usable from a remote workstation.

#### 5.16.1.11 – Create user profiles
*Summary (shall):* The SA shall be able to create user profiles.

- **IMPL-5.16.1.11-01** `[HTML][JS]` `[Main]` The administrative view shall include a profile-creation form (name, roles, initial credentials) validated by `validate.js` and submitted via `net.js`.

#### 5.16.1.12 – Access user profiles
*Summary (shall):* The SA shall be able to access user profiles.

- **IMPL-5.16.1.12-01** `[HTML][JS]` `[Main]` The administrative view shall offer a list/search of user profiles with a browsable detail view (personal data, roles, account status) in `#aux-region`.

#### 5.16.1.13 – Edit user profiles
*Summary (should):* SA and ISSO should be able to edit user profiles.

- **IMPL-5.16.1.13-01** `[HTML][JS]` `[Main]` The profile detail (IMPL-5.16.1.12-01) should include an edit form (roles, status, data) with `validate.js` validation and confirmation via `dialog.js`.

#### 5.16.1.14 – Account setup feedback
*Summary (shall):* The SA shall be informed when the account creation process is completed.

- **IMPL-5.16.1.14-01** `[JS]` `[Global]` At the end of profile creation (IMPL-5.16.1.11-01), `feedback.js`/`notify.js` shall show explicit confirmation (`#toast-region`, `role="status"`) to the SA within the times of Table V (local update ≤ 0.5 s).

#### 5.16.1.15 – Disabled account feedback
*Summary (shall):* The SA shall receive feedback when an account has been disabled.

- **IMPL-5.16.1.15-01** `[JS]` `[Global]` `notify.js` shall show confirmation to the SA when an account is disabled (same channel as IMPL-5.16.1.14-01), with the state persisted by `state.js`.

#### 5.16.1.16 – Purged account feedback
*Summary (shall):* The SA shall receive feedback when an account has been permanently deleted.

- **IMPL-5.16.1.16-01** `[JS]` `[Global]` `notify.js` shall show explicit confirmation to the SA on completion of the permanent account deletion operation, with a prior double confirmation via `dialog.js` (irreversible action).

#### 5.16.2.1 – User identification
*Summary (shall):* User identification procedures shall be as simple as possible, consistent with common data-protection practices.

- **IMPL-5.16.2.1-01** `[HTML][JS]` `[Main]` The login form shall be limited to the minimum necessary fields (identifier, authentication factor), with clear labels, standard HTML autocomplete (`autocomplete="username"`) and no superfluous steps.

#### 5.16.2.2 – Notification message or banner
*Summary (shall):* Where required by policy, the system shall support the display of security and privacy banners/notices until the user explicitly acknowledges them.

- **IMPL-5.16.2.2-01** `[HTML][JS]` `[Global]` A `<dialog id="security-banner">` managed by `dialog.js` shall be shown before/during login, blocking until the user activates an explicit `#btn-acknowledge-banner` button; the text is configurable via `i18n.js`/`config.js`.

#### 5.16.2.3 – Multifactor authentication
*Summary (shall):* Multifactor authentication for network and local access shall be implemented, with at least two factors among: a. a physical object (e.g., CAC/token); b. something the user knows (password/PIN); c. a biometric datum.

- **IMPL-5.16.2.3-01** `[HTML][JS]` `[Main]` The login flow managed by `auth.js` shall require, in sequence, at least two factors among: token/CAC reading (dedicated browser API or hardware integration), password/PIN (`<input type="password">`) and, where available, biometrics (WebAuthn), with visible progress state.
- **IMPL-5.16.2.3-02** `[JS]` `[Global]` `config.js` shall define which combinations of factors (a/b/c) are enabled for the current deployment, verified by `auth.js` before authentication is considered complete.

#### 5.16.2.4 – Viable multifactor authentication options
*Summary (shall):* The user shall be informed of the multifactor authentication options available to access the system.

- **IMPL-5.16.2.4-01** `[HTML]` `[Main]` The login screen shall legibly list the available authentication methods (e.g., "CAC + PIN", "password + biometrics") before the user chooses how to proceed.

#### 5.16.2.5 – Status feedback
*Summary (shall):* The user shall receive feedback on the status (accepted/rejected) of authentication inputs.

- **IMPL-5.16.2.5-01** `[JS]` `[Main]` After each authentication factor submission, `feedback.js` shall show the immediate outcome (accepted/rejected) via `#status-line`/`#message-line` and `aria-live="polite"`, within ≤ 0.2 s (error, Table V).

#### 5.16.2.6 – Password not echoed on display
*Summary (should):* If the username/password factor is used, the password should not be displayed in clear text (e.g., an asterisk for each character).

- **IMPL-5.16.2.6-01** `[HTML]` `[Main]` The password field shall use `<input type="password">` (native masking with dot/asterisk); an optional "show password" control (`aria-pressed`) may be offered to the user, disabled by default.

#### 5.16.2.7 – Error message
*Summary (shall):* An error message shall be provided when a selected user/device identifier is not valid, with instructions for selecting a new one.

- **IMPL-5.16.2.7-01** `[JS]` `[Main]` `validate.js` shall report the invalid identifier via `#message-line` and `[aria-invalid="true"]` on the field, with instructional text on how to correct it, generated by `i18n.js`.

#### 5.16.2.8 – User and device identifier notification
*Summary (shall):* The user shall be notified when the assignment of the user/device identifier has been completed successfully.

- **IMPL-5.16.2.8-01** `[JS]` `[Main]` `feedback.js` shall confirm via `#status-line` the successful assignment of the user/device identifier, within ≤ 0.5 s (local update, Table V).

#### 5.16.2.9 – User status
*Summary (shall):* The status of a requested action shall be displayed.

- **IMPL-5.16.2.9-01** `[JS]` `[Global]` Every action initiated by the user (login, SA request, etc.) shall have a visible status (in progress/completed/failed) managed by `feedback.js`/`#status-line`, with `#progress-indicator` beyond 1 s.

#### 5.16.2.10 – Reason for failure feedback
*Summary (shall):* A specific reason shall be displayed in the event of a failed action.

- **IMPL-5.16.2.10-01** `[JS]` `[Main]` `validate.js`/`notify.js` shall show a specific (non-generic) message in `#message-line` about the reason for the failure, avoiding texts such as "generic error".

#### 5.16.2.11 – Corrective action suggestion
*Summary (shall):* A corrective action shall be suggested in the event of access denial.

- **IMPL-5.16.2.11-01** `[JS]` `[Main]` The access-denial message generated by `auth.js`/`notify.js` shall include an explicit operational suggestion (e.g., "contact the administrator", "re-enter PIN").

#### 5.16.2.12 – Maximum number of concurrent sessions
*Summary (should):* The maximum number of concurrent sessions per account should be enforced, with access denied beyond the threshold.

- **IMPL-5.16.2.12-01** `[JS]` `[Global]` `auth.js` should check with `net.js` the number of active sessions per account against the threshold in `config.js` and deny access with an explicit message if exceeded.

#### 5.16.2.13 – Concurrent session control
*Summary (should):* The SA and ISSO should have information on all active concurrent sessions for each account.

- **IMPL-5.16.2.13-01** `[HTML][JS]` `[Main]` The administrative view (IMPL-5.16.1.9-01) should list, for each account, the active concurrent sessions (device, time, status).

#### 5.16.2.14 – Multiple concurrent sessions
*Summary (shall):* If more than one session per account is permitted, the user shall be informed of all of their own active sessions.

- **IMPL-5.16.2.14-01** `[HTML][JS]` `[Header]` `#user-menu` shall include an entry listing the active sessions of the current user, updated by `state.js`/`net.js`.

#### 5.16.3.1 – Logon procedures
*Summary (shall):* Logon shall be a separate procedure to be completed before the user can select operational options.

- **IMPL-5.16.3.1-01** `[HTML][JS]` `[Main]` `nav.js` shall prevent access to `#nav-region`/`#content-region` until `auth.js` signals that authentication is complete; the login view is the only one reachable before login.

#### 5.16.3.2 – Automatic logon display
*Summary (shall):* Logon requests shall be displayed automatically without special actions beyond powering on the terminal.

- **IMPL-5.16.3.2-01** `[JS]` `[Global]` On application startup (`DOMContentLoaded`), `auth.js` shall automatically display the login screen/form without requiring any preliminary user interaction.

#### 5.16.3.3 – Logon attempts notification
*Summary (shall):* The ability to view the account logon history shall be available to the SA and ISSO.

- **IMPL-5.16.3.3-01** `[HTML][JS]` `[Main]` The administrative view shall offer a searchable/filterable table of the logon history per account, fed by `log.js`.

#### 5.16.3.4 – Logon metadata
*Summary (shall):* Name, account, role, date and time of logon attempts shall be recorded.

- **IMPL-5.16.3.4-01** `[JS]` `[Global]` `log.js` shall record, for every logon attempt: user name, account, role, date and time, forwarding the record to the back end via `net.js`.

#### 5.16.3.5 – Invalid logon attempts
*Summary (shall):* If the system imposes a limit on invalid login attempts, the user shall be informed of the number of attempts remaining before lockout.

- **IMPL-5.16.3.5-01** `[JS]` `[Main]` `auth.js` shall show in `#message-line` the number of attempts remaining after each failed login, updated on every attempt, until lockout (see IMPL-5.16.1.7-01).

#### 5.16.3.6 – Logon reset request
*Summary (shall):* If the user is locked out, they shall be able to request that the SA reset the number of logon attempts.

- **IMPL-5.16.3.6-01** `[HTML][JS]` `[Main]` The account lockout screen shall present a `#btn-request-reset` button/link that sends (via `net.js`) an unlock request to the SA, with a submission confirmation shown to the user.

#### 5.16.4.1 – Automatic logoff
*Summary (shall):* Where required by policy, after a predefined period of inactivity the user shall be automatically logged off without loss of data.

- **IMPL-5.16.4.1-01** `[JS]` `[Global]` `auth.js`, via `#session-timer` and a timeout configurable in `config.js`, shall perform automatic logout after inactivity, first saving any unconfirmed state (drafts) via `state.js`/`net.js` to avoid data loss.
- **IMPL-5.16.4.1-02** `[HTML][CSS]` `[Footer]` `#session-timer` shall visually show the time remaining before automatic logout, with a warning state (`--color-warning`) in the final configurable seconds.

#### 5.16.4.2 – Logoff procedures
*Summary (shall):* On a logoff/exit/shutdown request, the system shall: a. check for pending transactions and warn of possible data loss; b. request confirmation before performing the logoff.

- **IMPL-5.16.4.2-01** `[JS]` `[Global]` `auth.js` shall query `state.js` for unsaved transactions/changes before logoff and, if present, display an explicit warning of possible data loss (point a).
- **IMPL-5.16.4.2-02** `[HTML][JS]` `[Global]` A `<dialog id="confirm-logoff">` managed by `dialog.js` shall require explicit user confirmation before performing logoff/exit/shutdown (point b).

#### 5.16.5.1 – Displayed security classification
*Summary (shall):* When the displayed data is classified, a prominent indication of the classification level shall be provided.

- **IMPL-5.16.5.1-01** `[HTML][CSS]` `[Header]` An indicator `#classification-banner` (always visible, high contrast, distinct/dedicated color `--color-critical`) shall show the classification level of the current data, updated by `state.js`.

#### 5.16.5.2 – Warnings
*Summary (should):* The system logic should generate messages and alarm signals to warn of intrusion attempts by unauthorized users.

- **IMPL-5.16.5.2-01** `[JS]` `[Global]` `notify.js`, on notification from `net.js`/`auth.js` of anomalous access attempts, should generate a high-priority alert (visual `#alert-summary` + audible `feedback.js.beep('alarm')`).

#### 5.16.6.1 – Simulated data
*Summary (shall):* When simulated data and functions are provided (e.g., for training), real data shall be protected.

- **IMPL-5.16.6.1-01** `[JS]` `[Global]` `state.js` shall manage an explicit `simulation` mode, isolating access to real data (blocked on the `net.js` side) whenever that mode is active.

#### 5.16.6.2 – Real system use
*Summary (shall):* Actual use of the system shall be clearly distinguished from all simulated operations.

- **IMPL-5.16.6.2-01** `[HTML][CSS]` `[Header]` `#mode-indicator` shall permanently and visually distinctly show (color/label/icon) whether the session is in real or simulated mode.

#### 5.16.6.3 – Clear indication
*Summary (shall):* In applications where real or simulated data may be shown, a clear indication of simulated data shall be included.

- **IMPL-5.16.6.3-01** `[HTML][CSS]` `[Main]` Every view in `#content-region` that shows data shall apply an `.is-simulated` class (distinctive border/label "SIMULATED DATA") when `state.js` signals simulation mode, in addition to IMPL-5.16.6.2-01.

#### 5.16.7.1 – Password criteria
*Summary (shall):* The criteria for an acceptable password (e.g., minimum number and types of characters) shall be shown to the user while generating the password.

- **IMPL-5.16.7.1-01** `[HTML]` `[Main]` The password setup/change form shall persistently display (not only on error) the list of required criteria, defined in `config.js` and validated by `validate.js`.

#### 5.16.7.2 – Dynamic feedback
*Summary (shall):* The user shall receive dynamic feedback on which password complexity criteria have not been met while generating the password.

- **IMPL-5.16.7.2-01** `[JS]` `[Main]` `validate.js` shall update in real time (`input` event, echo ≤ 0.2 s Table V) the status of each criterion listed in IMPL-5.16.7.1-01, with a met/not-met indicator (`aria-live="polite"`).

#### 5.16.7.5 – Changing passwords
*Summary (shall):* Where passwords shall be changed periodically per policy, the system shall support enforcement of the policy; within the mandatory interval the user shall be able to change the password whenever desired.

- **IMPL-5.16.7.5-01** `[JS]` `[Main]` `auth.js` shall force a redirect to the password-change form when `config.js` signals expiry of the current password, and shall allow voluntary change at any time via `#user-menu`.

#### 5.16.7.6 – Password managers
*Summary (should):* When appropriate to system operations, the system should support the use of password managers for entering credentials.

- **IMPL-5.16.7.6-01** `[HTML]` `[Main]` Login/credential fields shall use standard `autocomplete` attributes (`username`, `current-password`, `new-password`) and shall not block paste/autofill, for compatibility with browser password managers.

#### 5.19.1.9.2 – Cybersecurity (PED)
*Summary (should):* During use, portable electronic devices (PEDs) should have security measures to prevent tampering and to meet DoD cybersecurity requirements and §5.16.

- **IMPL-5.19.1.9.2-01** `[JS]` `[Global]` When the application runs on a mobile/tablet device (PED), `auth.js` should apply the same access-control/authentication measures defined in this section (see IMPL-5.16.*), with no exceptions for the form factor.

### 3.4 Controls, pointing and touch input

Requirements applicable to on-screen controls and touchscreen use (requirements relating to mechanical hardware, physical keyboard/mouse/trackball/joystick/light pen are excluded, not relevant to a web HMI). These are implemented with the `--touch-target-min/max`, `--touch-gap-min/max` tokens (§2.4), Pointer Events, `feedback.js`, `dialog.js`, `nav.js`.

#### 5.1.1.3.1 – Grouping
*Summary (shall):* Controls actuated in a task-driven sequence or used together shall be grouped together with their associated displays.

- **IMPL-5.1.1.3.1-01** `[HTML][CSS]` `[Main]` Controls belonging to the same operational sequence shall be enclosed in a common semantic container (e.g., `<fieldset class="control-group">`) positioned adjacent to the associated displays/values in `#content-region`.

#### 5.1.1.3.1.1 – Multiple steps selected by a single control
*Summary (shall):* When multiple steps of a sequence are selected by a single control, the steps shall be ordered according to their order of occurrence to minimize movement and prevent unnecessary cycling.

- **IMPL-5.1.1.3.1.1-01** `[JS]` `[Main]` A multi-step control (e.g., `<select>`/stepper) managed by `state.js` shall list the options in actual chronological/operational order, without the possibility of skipping or repeating unnecessary steps.

#### 5.1.1.3.1.2 – Cycling through ON/OFF position
*Summary (shall):* Unnecessary cycling through the control's ON/OFF position shall be avoided.

- **IMPL-5.1.1.3.1.2-01** `[JS]` `[Main]` The stepper/cyclic control logic in `state.js` shall not pass through an intermediate ON/OFF state when switching between two operational values unrelated to that state.

#### 5.1.1.3.2 – Sequential operation
*Summary (shall):* Where sequential operations follow a fixed pattern, controls shall be arranged to facilitate the operation (e.g., left-to-right/top-to-bottom pattern).

- **IMPL-5.1.1.3.2-01** `[CSS]` `[Main]` Controls with fixed sequential use shall be arranged with `display: grid`/`flex` following the natural reading order (left→right, top→bottom), consistent with DOM order for tab order.

#### 5.1.1.3.4 – Consistency
*Summary (shall):* The arrangement of functionally similar/identical primary controls shall be consistent across the panels of the entire system; moving a control to the right/left shall correspond to movement of the displayed element in the same direction.

- **IMPL-5.1.1.3.4-01** `[CSS]` `[Global]` Common utility classes (e.g., `.control-slider`, `.control-toggle`) shall define the same arrangement and direction across all views of the application, documented as a reusable pattern.
- **IMPL-5.1.1.3.4-02** `[JS]` `[Main]` For controls that manipulate a displayed element (slider, cursor), moving the control to the right/left shall produce movement of the associated element in the same direction, managed by `state.js`.

#### 5.1.1.4.1 – Consistent application
*Summary (shall):* Where a coding scheme is used to differentiate controls, the code's application shall be uniform throughout the system.

- **IMPL-5.1.1.4.1-01** `[CSS]` `[Global]` The codes (color, icon, shape) used to differentiate control types shall be defined only once in the `--color-*` tokens (§2.4) and applied uniformly through shared classes, with no per-view local variants.

#### 5.1.1.7.1 – Location and design
*Summary (shall):* Controls shall be designed and located so as not to be subject to accidental/inadvertent activation, particularly critical controls whose incorrect activation could cause damage, injury, performance degradation or shutdown of mission-critical equipment.

- **IMPL-5.1.1.7.1-01** `[HTML][CSS]` `[Main]` Critical controls (`.control-critical` class) shall be positioned isolated from frequently used controls, with an increased `--touch-gap-min` (e.g., double the minimum) and distinctive background/border to reduce accidental activation.
- **IMPL-5.1.1.7.1-02** `[JS]` `[Main]` Activating a `.control-critical` control shall require confirmation via `dialog.js` before executing the action (see also IMPL-5.1.3.1.7-01).

#### 5.1.1.7.3 – Rapid operation
*Summary (shall):* Any method of protection against inadvertent activation shall not prevent actuation within the required time.

- **IMPL-5.1.1.7.3-01** `[JS]` `[Main]` The confirmation required for critical controls (IMPL-5.1.1.7.1-02) shall be executable with a single additional tap (e.g., a "Confirm" button in the dialog), without multiple steps that would delay beyond the required operational time (configured in `config.js`).

#### 5.1.1.9.2 – Consistency of use
*Summary (shall):* A control used for a critical or emergency function shall be dedicated exclusively to that function.

- **IMPL-5.1.1.9.2-01** `[HTML][JS]` `[Main]` Controls with the `.control-emergency` class (e.g., stop, alarm) shall not be reused for other functions in any view; `nav.js`/`state.js` shall keep their identity and behavior constant wherever they appear.

#### 5.1.2.2.1 – Functional grouping
*Summary (shall):* Functionally related controls and displays shall be located close together and organized into functional groups (e.g., power, status, test).

- **IMPL-5.1.2.2.1-01** `[HTML][CSS]` `[Main]` `#content-region` shall organize related controls and displays into adjacent `<section class="control-group" aria-label="...">` containers, grouped by function (e.g., power, status, test).

#### 5.1.2.2.1.1 – Sequence
*Summary (shall):* Functional groups of controls/displays shall be located to ensure a left-to-right (preferred) or top-to-bottom order of use, or both.

- **IMPL-5.1.2.2.1.1-01** `[CSS]` `[Main]` The arrangement of `.control-group` elements in `#content-region` shall follow a grid that respects left→right order as the primary criterion, top→bottom as secondary, via `grid-auto-flow: column` or equivalent.

#### 5.1.2.2.1.3 – Access
*Summary (shall):* Consistent with the integrity of grouping by function/sequence, the most-used and most important groups shall be located in the most easily accessible areas.

- **IMPL-5.1.2.2.1.3-01** `[CSS]` `[Main]` `.control-group` elements marked `data-priority="high"` shall be positioned in areas reachable without scrolling (above the fold) and closest to the thumb/center of the touch screen.

#### 5.1.2.2.1.5 – Consistency
*Summary (shall):* The position of recurring functional groups and individual elements shall be consistent from panel to panel.

- **IMPL-5.1.2.2.1.5-01** `[CSS]` `[Global]` A common grid layout (shared classes/tokens) shall position recurring functional groups (e.g., confirm/cancel actions, status) at the same relative coordinates across all views of `#content-region`.

#### 5.1.2.2.1.5.2 – Display commonality
*Summary (shall):* When multiple displays and multiple formats are used, nomenclature and symbology shall be common across all displays, where appropriate.

- **IMPL-5.1.2.2.1.5.2-01** `[JS]` `[Global]` `i18n.js` shall provide a single source of labels/symbols/icons reused by all views, avoiding synonyms or different symbols for the same concept.

#### 5.1.2.2.1.5.3 – Text or readout fields
*Summary (shall):* Text/readout fields common to all displays (e.g., system alerts) shall be located in a standard position across all panels and formats.

- **IMPL-5.1.2.2.1.5.3-01** `[HTML][CSS]` `[Footer]` Common fields such as system alerts shall remain anchored to `#status-line`/`#message-line` in the footer, in a fixed position regardless of the current view in `#content-region`.

#### 5.1.2.2.1.5.4 – Commonality and consistency across systems
*Summary (shall):* Where possible, display consistency and commonality criteria shall also be applied to other systems used by the user.

- **IMPL-5.1.2.2.1.5.4-01** `[JS]` `[Global]` Process requirement: the nomenclature/symbology/positioning conventions (IMPL-5.1.2.2.1.5.2-01, -5.3-01) shall be documented as a shared design system, reusable/verifiable also in other applications of the same system family.

#### 5.1.2.2.3 – Arrangement within groups
*Summary (shall):* Controls and displays within functional groups shall be located according to the operational sequence and/or function.

- **IMPL-5.1.2.2.3-01** `[CSS]` `[Main]` The DOM and visual order of elements within each `.control-group` shall reflect the operational sequence or functional relationship between the controls, not an arbitrary order.

#### 5.1.2.2.3.1 – No specific sequence
*Summary (shall):* If the controls/displays of a group do not follow a specific operational sequence, they shall be arranged by importance or frequency of use, with the most important/used in the most accessible positions.

- **IMPL-5.1.2.2.3.1-01** `[CSS]` `[Main]` In the absence of a fixed sequence, the `data-priority`/`data-frequency` attribute on the element determines the CSS `order`/position in the grid, favoring the most accessible positions for the higher values.

#### 5.1.2.2.3.11 – Emergency use
*Summary (shall):* Emergency displays and controls shall be located where they can be seen and reached without delay (e.g., within a 30° cone relative to the normal line of sight).

- **IMPL-5.1.2.2.3.11-01** `[HTML][CSS]` `[Header][Footer]` Emergency controls/indicators (`#alert-summary`, `.control-emergency`) shall remain always visible in the header/footer (never in hidden popup panels), reachable without additional navigation, as the software equivalent of the "30° cone".

#### 5.1.3.1.1.1 – High frequency use
*Summary (should):* The touchscreen should not be the sole means of input if the interface requires frequent entry of large amounts of data.

- **IMPL-5.1.3.1.1.1-01** `[JS]` `[Global]` For views with massive/recurring data entry (e.g., extensive forms, editable tables), the application should also support input from a physical keyboard/scanner via Pointer Events and `keydown` handling, not only touch on a virtual keyboard.

#### 5.1.3.1.1.2 – Moving or vibration environments
*Summary (should):* The touchscreen should not be the sole means of input if system movement/vibration could degrade user performance.

- **IMPL-5.1.3.1.1.2-01** `[JS]` `[Global]` `prefs.js` should offer an "unstable environment" mode that increases `--touch-target-min` toward the maximum and enables voice/haptic confirmations (`navigator.vibrate`) via `feedback.js` as redundancy to touch.

#### 5.1.3.1.1.3 – Mission-critical and safety-critical applications
*Summary (shall):* The touchscreen shall not be the sole input device for mission-critical or safety-critical interfaces.

- **IMPL-5.1.3.1.1.3-01** `[JS]` `[Global]` Views marked `data-criticality="mission"`/`"safety"` shall guarantee full operability from the keyboard as well (focus, `Enter`/`Space`, shortcuts) via `nav.js`, without relying exclusively on touch.

#### 5.1.3.1.3 – Positive indication
*Summary (shall):* Positive indication of touchscreen activation shall be provided to signal the system's response to the control action.

- **IMPL-5.1.3.1.3-01** `[CSS][JS]` `[Global]` Every control shall apply immediate visual feedback on `pointerdown`/`pointerup` (`:active` state, `feedback.js.acknowledge(el)`) and optionally audible/haptic feedback, within ≤ 0.1 s (touch response, Table V).

#### 5.1.3.1.4 – Display response time (latency)
*Summary (should):* Display response time should not exceed 100 ms; a shorter time may be required by operational considerations.

- **IMPL-5.1.3.1.4-01** `[JS]` `[Global]` `feedback.js` should ensure that visual feedback of activation (IMPL-5.1.3.1.3-01) is rendered within 100 ms of the `pointerup` event, measurable with `performance.now()` in automated tests.

#### 5.1.3.1.5 – Impact on visual display
*Summary (shall):* The characteristics of touch-interactive devices shall not degrade the quality of the visual display in a way that compromises user performance.

- **IMPL-5.1.3.1.5-01** `[CSS]` `[Global]` Touch feedback overlays (e.g., ripples, highlights) shall use opacities/colors that do not reduce text/background contrast below the defined `--contrast-*` threshold (≥ 6:1).

#### 5.1.3.1.6 – Spatial resolution
*Summary (shall):* Touch-interactive devices shall provide spatial resolution sufficient for the intended tasks.

- **IMPL-5.1.3.1.6-01** `[CSS]` `[Global]` The size and spacing of touch targets shall comply with `--touch-target-min`/`--touch-gap-min` (see table in 3.4-5.1.3.1.17) so that effective touch resolution is adequate to the task.

#### 5.1.3.1.7 – Critical tasks
*Summary (shall):* Where a touchscreen control is used for a critical task, the system response shall require an additional confirming action to ensure the activation was intentional.

- **IMPL-5.1.3.1.7-01** `[JS]` `[Main]` `.control-critical` controls shall open a confirmation `<dialog>` via `dialog.js` after the initial `pointerup`, executing the action only after a second explicit user confirmation.

#### 5.1.3.1.7.1 – Confirmatory action impractical
*Summary (shall):* Where the confirming action is impractical for critical tasks, multiple touch activations shall be incorporated to minimize the possibility of accidental activation.

- **IMPL-5.1.3.1.7.1-01** `[JS]` `[Main]` As an alternative to IMPL-5.1.3.1.7-01 (when a dialog is impractical), the critical control shall require a sequence of 2 distinct taps within a time window configurable in `config.js`, managed by `feedback.js`.

#### 5.1.3.1.8 – Repeat function delay
*Summary (shall):* An initial delay of 500-750 ms shall be provided when a repeat delay is supplied.

- **IMPL-5.1.3.1.8-01** `[JS]` `[Global]` For press-and-hold/repeat controls (e.g., +/- steppers), `feedback.js` shall apply an initial `setTimeout` of 500-750 ms (design value in `config.js`) before starting automatic repetition on sustained `pointerdown`.

#### 5.1.3.1.9 – Sensitivity
*Summary (shall):* Contact of the fingertip with the touchscreen shall be able to activate the control or input function.

- **IMPL-5.1.3.1.9-01** `[CSS][JS]` `[Global]` All controls shall have `touch-action: manipulation` and an actuation area ≥ `--touch-target-min`, ensuring reliable activation with a single fingertip contact without requiring prolonged pressure or tools.

#### 5.1.3.1.14 – Sustained interaction
*Summary (shall):* Touchscreens shall not be used for frequent actions sustained over time (e.g., typing on a virtual keyboard, continuous target selection); this does not apply to small handheld devices.

- **IMPL-5.1.3.1.14-01** `[JS]` `[Global]` For prolonged text-entry tasks, `prefs.js` should favor a physical keyboard/voice dictation over the virtual touch keyboard; the interface shall not require continuous touch typing as the only path (except on small handheld devices, `data-form-factor="handheld"`).

#### 5.1.3.1.15 – Intermittent interaction
*Summary (may):* Touchscreens may be used to accomplish intermittent actions such as coarse cursor navigation and selection of communication panels.

- **IMPL-5.1.3.1.15-01** `[JS]` `[Main]` Intermittent interactions (menu item selection, panel navigation) may be implemented entirely via touch (Pointer Events) without requiring alternatives, being consistent with the intended intermittent use.

#### 5.1.3.1.16 – Target shape and color
*Summary (shall):* If color coding is used, it shall be redundant with another form of coding.

- **IMPL-5.1.3.1.16-01** `[CSS][HTML]` `[Global]` Every color-coded state/target (`--color-critical`, `--color-alarm`, etc.) shall be accompanied by a distinctive icon, shape or text (double coding, see §2.4), not by color alone.

#### 5.19.3.3 – Data input (PED)
*Summary (should):* Data input on a PED should occur via a virtual or physical keyboard/keypad; unless there is a specific need, handwriting recognition should be avoided.

- **IMPL-5.19.3.3-01** `[HTML][JS]` `[Main]` Data-entry fields shall use standard controls (`<input>`, the operating system's virtual keyboard or an on-screen keypad defined by the app) and shall not require handwriting recognition as the sole input method.

#### 5.19.3.4 – Direct input pointers
*Summary (shall):* Direct input pointers (e.g., touchscreen, light pen) shall be used in preference to indirect input pointers (e.g., trackball, mouse).

- **IMPL-5.19.3.4-01** `[JS]` `[Global]` Pointer Events shall favor direct touch as the primary mode of interaction; mouse/trackball remain supported as secondary inputs (see §2.2 General constraints), not replacements for direct touch.

#### 5.1.3.1.17 – Dimensions, resistance, and separation
*Summary (shall):* The dimensions and separation of touchscreen response areas shall conform to the following table (resistance/force is a hardware characteristic, omitted; physical dimensions are translated into CSS px via `--px-per-mm`).

| Category | A (actuation area) | S (separation) | Equivalent CSS token |
|---|---|---|---|
| Alphanumeric/numeric keyboard – minimum | – | 0 | `--touch-gap-min-kb: 0` |
| Alphanumeric/numeric keyboard – preferred | 16 × 16 mm | 2 mm | `--touch-target-kb-preferred: calc(16 * var(--px-per-mm))`; `--touch-gap-kb-preferred: calc(2 * var(--px-per-mm))` |
| Alphanumeric/numeric keyboard – maximum | – | 6 mm | `--touch-gap-max: calc(6 * var(--px-per-mm))` |
| Other applications – minimum | 15 × 15 mm | 3 mm | `--touch-target-min: calc(15 * var(--px-per-mm))`; `--touch-gap-min: calc(3 * var(--px-per-mm))` |
| Other applications – maximum | 38 × 38 mm | 6 mm | `--touch-target-max: calc(38 * var(--px-per-mm))`; `--touch-gap-max: calc(6 * var(--px-per-mm))` |

Notes: (1) with standard fire-retardant gloves, add 5 mm to each dimension of the actuation area; (2) with a "first contact" strategy the minimum separation is 5 mm, with a "last contact" strategy (adopted by default, §2.6) it can drop to 3 mm (not for alphanumeric keyboards).

- **IMPL-5.1.3.1.17-01** `[CSS]` `[Global]` All non-keyboard controls shall comply with `min-inline-size`/`min-block-size: var(--touch-target-min)` (15×15 mm) and shall not exceed `var(--touch-target-max)` (38×38 mm) except where layout requires it, as already defined in §2.4.
- **IMPL-5.1.3.1.17-02** `[CSS]` `[Main]` On-screen keyboard keys (`.key` components) shall use the preferred size `--touch-target-kb-preferred` (16×16 mm) with gap `--touch-gap-kb-preferred` (2 mm, minimum 0, maximum `--touch-gap-max` 6 mm).
- **IMPL-5.1.3.1.17-03** `[CSS]` `[Global]` Spacing between adjacent non-keyboard controls shall comply with `gap: var(--touch-gap-min)` (3 mm, default last-contact strategy), raisable to 5 mm if `config.js` sets the "first contact" strategy.
- **IMPL-5.1.3.1.17-04** `[JS]` `[Global]` `prefs.js` shall expose a "gloves mode" option that adds 5 mm (`calc(var(--touch-target-min) + 5 * var(--px-per-mm))`) to the minimum target dimensions, for users with fire-retardant gloves.

### 3.5 Interactive control and dialog design (general)

General criteria for interactive dialogs: control actions, response times, menu hierarchy, feedback, numeric systems and graphic interaction. Criteria duplicated in 5.17 (menus, forms, command language, question-and-answer, query) are addressed in the dedicated sections.

#### 5.1.3.12.1.1 – Control actions
*Summary (shall/should/may):* Control actions shall be minimized, consistent, make minimal memory demands of the user, and be sufficiently flexible to adapt to different user needs.

- **IMPL-5.1.3.12.1.1-01** `[HTML][CSS]` `[Global]` Define a single, reusable set of control components (`.btn`, `.toggle`, `.field`) shared by all views in `#content-region`, avoiding ad hoc per-view variants.
- **IMPL-5.1.3.12.1.1-02** `[JS]` `[Global]` `prefs.js` shall offer an `interactionMode` (`novice`/`expert`) that reduces the steps required for the same action, without changing its meaning.
- **IMPL-5.1.3.12.1.1-03** `[JS]` `[Global]` `config.js` shall centralize the action-to-control mapping, so that every recurring action (confirm, cancel, next) always uses the same control throughout the app.

#### 5.1.3.12.1.2 – Dialog types
*Summary:* Dialog types shall be compatible with anticipated task requirements and user skills.

- **IMPL-5.1.3.12.1.2-01** `[JS]` `[Global]` `config.js` shall define, per view, the dialog type (`form`, `menu`, `command`); `nav.js` instantiates the matching component in `#content-region`.
- **IMPL-5.1.3.12.1.2-02** `[JS]` `[Global]` `state.js` shall expose `state.user.skillLevel`/`role`; `nav.js` chooses menu-guided dialogs for novice users and direct commands for expert users.

#### 5.1.3.12.1.3 – Response time compatibility with user
*Summary:* System response times shall be consistent with operational requirements and appropriate to the dialog type and the user's training.

- **IMPL-5.1.3.12.1.3-01** `[JS]` `[Global]` `config.js` shall expose `responseTimes` (Table V, §2.6) used by `feedback.js`/`net.js` for timeouts and progress thresholds consistent with the dialog type.
- **IMPL-5.1.3.12.1.3-02** `[JS]` `[Global]` `log.js` shall record actual response times, for verification of compliance (test T) with operational requirements.

#### 5.1.3.12.1.3.1 – Compatibility with system response times
*Summary:* Response times required of the user shall be compatible with the response times required of the system.

- **IMPL-5.1.3.12.1.3.1-01** `[JS]` `[Global]` `config.js` shall derive user input timeouts from the same `responseTimes` values, avoiding values mismatched between the UI field and the back end.

#### 5.1.3.12.1.3.2 – Consistent with user tasking
*Summary:* Response times required of the user shall fall within the limits imposed by the total workload expected in the operational environment (see 5.1.2.1.6.4).

- **IMPL-5.1.3.12.1.3.2-01** `[JS]` `[Global]` `config.js` shall make the maximum response time per action configurable based on `workloadProfile`, used by `validate.js` for form timeouts.

#### 5.1.3.12.1.3.3 – Response time-induced input device lockout
*Summary:* If processing requires delaying concurrent inputs and no buffer is available, input device lockout shall occur until the system is ready to accept the next transaction.

- **IMPL-5.1.3.12.1.3.3-01** `[JS]` `[Global]` `net.js`/`state.js` shall set `state.busy = true` and disable (`disabled`/`aria-disabled="true"`) the interactive controls during processing, until a response is received.
- **IMPL-5.1.3.12.1.3.3-02** `[CSS]` `[Global]` The `.is-busy` class shall show `cursor: wait` and a disabled style on the locked-out controls.

#### 5.1.3.12.1.3.4 – Lockout alert
*Summary:* An alert shall be displayed to indicate to the user that lockout has occurred.

- **IMPL-5.1.3.12.1.3.4-01** `[JS][HTML]` `[Footer]` `feedback.js` shall call `setStatus("Processing…", "busy")`, shown in `#status-line` (`role="status"`) whenever `.is-busy` is active.

#### 5.1.3.12.1.3.5 – Input device restoration
*Summary:* When the system is ready to continue following a lockout, a signal indicating this shall be presented (e.g., the cursor returns to its normal shape).

- **IMPL-5.1.3.12.1.3.5-01** `[CSS][JS]` `[Global]` At the end of processing, `state.js` shall remove `.is-busy`/`cursor: wait` and `feedback.js` restores the normal state of controls and cursor.

#### 5.1.3.12.1.3.6 – Interrupt to end input device lockout
*Summary:* In the event of input device lockout, the user shall be able to cancel the transaction causing the extended lockout, with an UNDO-type function that stops processing without RESETting the system.

- **IMPL-5.1.3.12.1.3.6-01** `[HTML][JS]` `[Footer]` A `#btn-cancel-request` control in `#function-keys` shall remain active during `.is-busy` and invoke `net.js:abortRequest()` (`AbortController`) without resetting `state.js`.

#### 5.1.3.12.1.4 – Simplicity
*Summary:* Control-display relationships shall be simple and explicit.

- **IMPL-5.1.3.12.1.4-01** `[HTML][CSS]` `[Main]` Every control in `#content-region` shall be positioned near the display it modifies and associated via `aria-controls`/`for`, without implicit indirection.

#### 5.1.3.12.1.5 – Control actions
*Summary:* Control actions shall be simple and direct; potentially destructive actions shall require extended attention (e.g., "Are you sure?").

- **IMPL-5.1.3.12.1.5-01** `[JS][HTML]` `[Global]` `dialog.js` shall open a confirmation `<dialog>` (`role="alertdialog"`) for every action marked `data-destructive="true"` before execution.
- **IMPL-5.1.3.12.1.5-02** `[JS]` `[Global]` Non-destructive actions shall execute directly on `pointerup` (*last contact* strategy), without superfluous confirmations.

#### 5.1.3.12.1.6 – Accidental actuation
*Summary:* A mechanism shall be provided to prevent accidental actuation of potentially destructive control actions (e.g., accidental erasure, memory dump).

- **IMPL-5.1.3.12.1.6-01** `[CSS]` `[Global]` Destructive controls shall comply with `--touch-gap-min`/`--touch-gap-max` from adjacent controls and shall not be the target of initial focus.
- **IMPL-5.1.3.12.1.6-02** `[JS]` `[Global]` See `IMPL-5.1.3.12.1.5-01`: `dialog.js` shall require reinforced confirmation (e.g., press-and-hold for a design value in `config.js`) for irreversible actions.

#### 5.1.3.12.1.7 – Compatibility with user skill
*Summary:* Controls shall be compatible with the lowest anticipated skill levels; experienced users shall have options that shorten the steps required for inexperienced users.

- **IMPL-5.1.3.12.1.7-01** `[JS]` `[Global]` `prefs.js` shall store `interactionMode` (`novice`|`expert`) in `localStorage`; `nav.js` enables shortcuts/direct commands only in expert mode.
- **IMPL-5.1.3.12.1.7-02** `[HTML]` `[Main]` The full menu-guided path shall always remain available in `#nav-region` even when expert shortcuts are active.

#### 5.1.3.12.1.8 – Availability of information
*Summary:* Information necessary to select or enter a specific control action shall be available to the user when selection is appropriate.

- **IMPL-5.1.3.12.1.8-01** `[HTML][JS]` `[Main]` Every control shall expose `aria-describedby` pointing to inline help text (`#aux-region`) visible when the control becomes selectable.

#### 5.1.3.12.1.9 – Concurrent display
*Summary:* Concurrent displays shall meet the criteria of 5.1.3.12.1.9.1–.9.3 (alternatives visible before selection, current value, positive feedback).

- **IMPL-5.1.3.12.1.9-01** `[JS]` `[Global]` See `IMPL-5.1.3.12.1.9.1-01`, `IMPL-5.1.3.12.1.9.2-01`, `IMPL-5.1.3.12.1.9.3-01` for the implementation detail of the three criteria.

#### 5.1.3.12.1.9.1 – Control actions (alternatives displayed before selection)
*Summary:* Control actions chosen from a discrete set of alternatives shall have those alternatives displayed before selection.

- **IMPL-5.1.3.12.1.9.1-01** `[HTML]` `[Main]` Discrete-choice controls (`<select>`, `role="radiogroup"`, menus) shall list all options in the markup before any selection interaction.

#### 5.1.3.12.1.9.2 – Current value
*Summary:* The current value of any parameter or variable with which the user is interacting shall be displayed.

- **IMPL-5.1.3.12.1.9.2-01** `[HTML][JS]` `[Main]` Every parameter control shall show the current value (e.g. `<output>`, `aria-valuenow`) updated by `state.js` in real time.

#### 5.1.3.12.1.9.3 – User control inputs
*Summary:* User control inputs shall produce a displayed positive feedback response indicating execution of the requested action.

- **IMPL-5.1.3.12.1.9.3-01** `[JS]` `[Global]` `feedback.js:acknowledge(el)` shall apply `.is-acknowledged` and, if required, a beep/vibration within the times of Table V for every control input.

#### 5.1.3.12.1.10 – Hierarchical process
*Summary:* The number of hierarchical levels used to control a process or sequence shall be minimized; no more than four levels should be used.

- **IMPL-5.1.3.12.1.10-01** `[JS]` `[Global]` `nav.js` shall respect `config.js:maxHierarchyLevels = 4` as a structural constraint of `#nav-region`, verifiable by inspection (I) of the navigation tree.

#### 5.1.3.12.1.10.1 – Display and input formats
*Summary:* Display and input formats should be similar within hierarchical levels.

- **IMPL-5.1.3.12.1.10.1-01** `[CSS][HTML]` `[Main]` Views at the same hierarchical level shall share the same layout template/classes in `#content-region`, with no format variation between peer-level views.

#### 5.1.3.12.1.10.2 – Current position
*Summary:* The system shall always indicate the current position within the sequence.

- **IMPL-5.1.3.12.1.10.2-01** `[HTML][JS]` `[Main]` `#nav-region` shall include a breadcrumb (`nav[aria-label="breadcrumb"]`) updated by `nav.js` with `aria-current="page"` on the last element at every view change.

#### 5.1.3.12.1.11 – User memorization
*Summary:* The system shall not require the user to learn mnemonics, codes, special or long sequences, or special instructions.

- **IMPL-5.1.3.12.1.11-01** `[HTML][JS]` `[Global]` Commands shall be accessible via visible controls with text (buttons/menus) in `#content-region`; any keyboard shortcuts remain an optional alternative documented in the help (see §3.7).

#### 5.1.3.12.1.12 – Dialog type
*Summary:* The choice of dialog type (form filling, menus, command language) for interactive control shall be compatible with user characteristics and task requirements.

- **IMPL-5.1.3.12.1.12-01** `[JS]` `[Global]` See `IMPL-5.1.3.12.1.2-01`/`-02`: same dialog-type selection logic in `config.js`/`nav.js` based on `state.user.role`/`skillLevel`.

#### 5.1.3.12.1.13 – Number system
*Summary:* Number systems shall meet the criteria of 5.1.3.12.1.13.1–.13.2 (decimal system, clarity of any alternate system).

- **IMPL-5.1.3.12.1.13-01** `[JS]` `[Global]` See `IMPL-5.1.3.12.1.13.1-01` and `IMPL-5.1.3.12.1.13.2-01`.

#### 5.1.3.12.1.13.1 – Decimal system
*Summary:* Numeric data displayed or required for control input shall be in decimal format rather than binary/octal/hexadecimal, unless operationally necessary.

- **IMPL-5.1.3.12.1.13.1-01** `[JS]` `[Global]` `i18n.js` shall format all numeric values in base 10 by default (`Intl.NumberFormat`); non-decimal fields require explicit opt-in in `config.js`.

#### 5.1.3.12.1.13.2 – Alternate number system
*Summary:* When numeric data is not in decimal format for operational necessity, the alternate number system used shall be clear to the user.

- **IMPL-5.1.3.12.1.13.2-01** `[HTML]` `[Main]` Fields with a non-decimal base shall show an explicit label/suffix (e.g. `0x`, "HEX" badge) next to the value, defined in `i18n.js`.

#### 5.1.3.12.1.14 – Data manipulation
*Summary:* The system shall allow the user to manipulate data without specialized knowledge of internal storage and retrieval mechanisms.

- **IMPL-5.1.3.12.1.14-01** `[JS]` `[Global]` Data operations in `#content-region` shall go through `state.js`/`net.js` application APIs as domain actions (e.g. "save", "filter"), without exposing the internal data structure.

#### 5.1.3.12.1.15 – Computer processing constraints
*Summary:* The transaction selection sequence shall be dictated by user choices and not by internal processing constraints.

- **IMPL-5.1.3.12.1.15-01** `[JS]` `[Global]` `nav.js` shall not force a navigation order based on internal loading state; controls remain selectable in the order chosen by the user, with waits handled by `net.js` in the background.

#### 5.1.3.12.1.16 – Feedback for correct input
*Summary:* Feedback responses to correct input shall consist of state/value changes of the controlled display elements, presented in an expected and logically natural form.

- **IMPL-5.1.3.12.1.16-01** `[JS]` `[Global]` `feedback.js:acknowledge(el)` shall directly update the state/value of the controlled display (not a separate generic message), in a form consistent with the action performed.

#### 5.1.3.12.1.19 – Control input data display
*Summary:* The presence and location of data entered by the user shall be clearly indicated; the data shall not mislead the user regarding nomenclature, units of measure, sequence of steps, or timing.

- **IMPL-5.1.3.12.1.19-01** `[HTML]` `[Main]` Every field shall show a label, unit of measure (`aria-describedby`) and value aligned unambiguously; `i18n.js` ensures terminological consistency.
- **IMPL-5.1.3.12.1.19-02** `[CSS]` `[Main]` The "value entered by the user" state shall be distinguishable (e.g. `.field--dirty`) from the default/system-computed value.

#### 5.1.3.12.2.2.3 – Menu selection by pointing
*Summary:* If menu selection is performed by pointing, two distinct actions shall be provided: the first designates the option, a second separate action confirms the selection for processing.

- **IMPL-5.1.3.12.2.2.3-01** `[JS][HTML]` `[Main]` Menu items shall distinguish designation (`pointerdown`/highlight, `aria-selected="true"`) from a separate confirmation event (`pointerup` on the "Confirm" action) before execution.

#### 5.1.3.12.2.2.5 – Stack menu selections
*Summary:* Users shall be provided the capability to stack menu selections, making several selections without each menu being displayed individually.

- **IMPL-5.1.3.12.2.2.5-01** `[JS]` `[Global]` `nav.js` shall support a `commandStack` queue that accepts several selections in rapid sequence (e.g. text command or typed path) without showing each intermediate menu.

#### 5.1.3.12.2.9 – Direction function call
*Summary:* If several levels of hierarchical menus are provided, a direct call capability shall be provided so that the experienced user does not have to step through multiple menu levels.

- **IMPL-5.1.3.12.2.9-01** `[JS]` `[Global]` `nav.js` shall expose direct commands/internal routes (`#/goto/<viewId>`) or a command palette to reach any view without passing through intermediate menus, in expert mode (see `IMPL-5.1.3.12.1.7-01`).

#### 5.1.3.12.2.14 – Return to next higher level
*Summary:* In hierarchical menus, the user shall be able to return to the next higher level with a single action up to the initial menu/display; no more than three levels should be used.

- **IMPL-5.1.3.12.2.14-01** `[HTML][JS]` `[Header]` `#btn-back` in the header shall invoke `nav.js:goUp()` moving up one level per single action; `config.js:maxMenuLevels = 3` constrains the depth of the `#nav-region` menus.

#### 5.1.3.12.2.15 – Return to top level
*Summary:* A function shall be provided to directly recall the initial, top-level menu/display without stepping through the hierarchy.

- **IMPL-5.1.3.12.2.15-01** `[HTML][JS]` `[Header]` `#btn-home` in the header shall invoke `nav.js:goHome()` to return directly to the top-level view in a single step.

#### 5.1.3.12.3.8.2 – Automatic movement
*Summary:* The cursor shall move automatically to the next field when the end of the current field is reached.

- **IMPL-5.1.3.12.3.8.2-01** `[JS]` `[Main]` Form fields shall handle the `input` event to move focus to the next field when `maxlength` is reached, keeping keyboard/tab navigation consistent.

#### 5.1.3.12.3.14 – User input delayed
*Summary:* If user input will be delayed, the user shall be able to designate the field to indicate that the missing item is delayed, not forgotten.

- **IMPL-5.1.3.12.3.14-01** `[HTML][JS]` `[Main]` Fields shall offer a `data-deferred` control (e.g. "To be completed later" checkbox) that applies `.field--deferred` and temporarily excludes the field from `validate.js` mandatory validation.

#### 5.1.3.12.5.5 – Abbreviations
*Summary:* The user shall be allowed to enter the full name or an abbreviation for any command of more than five characters.

- **IMPL-5.1.3.12.5.5-01** `[JS]` `[Main]` The command interpreter shall resolve unambiguous abbreviations via `nav.js:resolveCommand(input)` for every command with a name > 5 characters.

#### 5.1.3.12.6.3 – Multiple questions
*Summary:* The user shall not be required to answer several questions at once.

- **IMPL-5.1.3.12.6.3-01** `[HTML][JS]` `[Main]` Question-and-answer dialogs (`dialog.js`) shall present one question at a time, not several simultaneous mandatory fields in a single modal prompt.

#### 5.1.3.12.8 – Graphic interaction
*Summary:* Graphic interaction design shall meet the criteria of 5.1.3.12.8.1–.8.3 (use, iconic menus, supplementary textual labels).

- **IMPL-5.1.3.12.8-01** `[JS]` `[Global]` See `IMPL-5.1.3.12.8.1-01`, `IMPL-5.1.3.12.8.2-01`, `IMPL-5.1.3.12.8.3-01`.

#### 5.1.3.12.8.1 – Use
*Summary:* Graphic interaction as a dialog may be considered for casual users, as a graphic aid supplementing other types of interactive control.

- **IMPL-5.1.3.12.8.1-01** `[HTML]` `[Main]` `#content-region` may offer supplementary graphic controls (icons, maps) in addition to, never as an exclusive replacement of, the standard text/menu controls.

#### 5.1.3.12.8.2 – Iconic menus
*Summary:* With users of different linguistic backgrounds, graphic menus with icons for control options may be used; for use by foreign military personnel, icon design shall be consistent with applicable cultural and ethnic variables to ensure comprehension and avoid potential offense.

- **IMPL-5.1.3.12.8.2-01** `[JS]` `[Global]` `i18n.js` shall allow the selection of localized/culturally validated icon sets based on `state.user.locale`, configurable in `config.js`.

#### 5.1.3.12.8.3 – Supplementary textual labels
*Summary:* Where icons represent control actions in menus, textual labels shall be displayed, or made available for display, with each icon.

- **IMPL-5.1.3.12.8.3-01** `[HTML]` `[Main]` Every menu icon shall have adjacent visible text or an `aria-label`/`title` accessible on keyboard focus too, not only on mouse hover.

### 3.6 Dialog types: command language, question-and-answer, query, menus, forms

Requirements on command languages, question-and-answer dialogs, queries, menus (drop-down, iconic, hierarchical) and data-entry forms, including conventions for managing fixed and variable function keys.

#### 5.1.3.11.1.6 – Entry of data sets
*Summary (shall):* When repetitive entry of data sets is required, validation of each set shall be completed before a new transaction can begin.

- **IMPL-5.1.3.11.1.6-01** `[JS]` `[Main]` `validate.js` shall perform full validation of the current data set on form submit in `#content-region` and prevent `form.requestSubmit()`/submission of the next transaction until all fields are valid (`aria-invalid="false"`).
- **IMPL-5.1.3.11.1.6-02** `[HTML][CSS]` `[Main]` The form shall use `<fieldset>` for each repetitive set with a visual `.is-busy`/`[aria-disabled]` state when moving to the next set until validation is complete.

#### 5.1.3.11.1.8 – Abbreviations, acronyms, mnemonics, and codes
*Summary (shall):* When abbreviations/acronyms/mnemonics/codes are used to shorten data entry, they shall be distinctive and have an intuitive relationship with natural language or domain terminology.

- **IMPL-5.1.3.11.1.8-01** `[JS]` `[Global]` `config.js` shall define the table of allowed codes/abbreviations per field, mapped to the expanded label used by `i18n.js` for hints.
- **IMPL-5.1.3.11.1.8-02** `[HTML]` `[Main]` `<input>` fields accepting codes shall have `title` and `aria-describedby` pointing to the expanded meaning of the code/mnemonic.

#### 5.1.3.11.1.8.1 – Length
*Summary (shall):* An abbreviation shall be no longer than necessary to ensure a clear and unambiguous meaning.

- **IMPL-5.1.3.11.1.8.1-01** `[JS]` `[Main]` `validate.js` shall verify in `config.js` that every configured abbreviation does not exceed the minimum necessary length (design value, default 4 characters) and flag ambiguous duplicates during review.

#### 5.1.3.11.1.8.2 – Reference
*Summary (shall):* An easily accessible reference of abbreviation meanings shall be made available to the operator.

- **IMPL-5.1.3.11.1.8.2-01** `[HTML][JS]` `[Footer][Main]` A help panel (`#aux-region` or dialog from `#btn-help`) listing all active abbreviations/codes and their meanings shall exist, queryable from `i18n.js`.

#### 5.1.3.11.1.8.3 – Consistency
*Summary (shall):* Abbreviations, acronyms, mnemonics, and codes shall be consistent within and between systems/applications used by the same user population.

- **IMPL-5.1.3.11.1.8.3-01** `[JS]` `[Global]` The code table in `config.js` is centralized and shared by all modules (`validate.js`, `i18n.js`, `nav.js`); no module shall define alternative local codes for the same concept.

#### 5.1.3.11.1.11 – Change of data
*Summary (shall):* When a user requests a change (or deletion) of a data item not currently displayed, the option to display the old value before confirming the change shall be presented.

- **IMPL-5.1.3.11.1.11-01** `[JS][HTML]` `[Main]` `dialog.js` shall open a confirmation `<dialog>` showing the previous value (`data-old-value`) next to the new value before performing the update or deletion.

#### 5.1.3.11.1.12 – Single method of data entry
*Summary (shall):* Data entry methods and displays shall not require the user to shift between entry methods.

- **IMPL-5.1.3.11.1.12-01** `[HTML][CSS]` `[Main]` Every form in `#content-region` shall use a single, consistent entry paradigm (e.g. only an on-screen touch keyboard, or only native controls) for all fields of the same form; do not alternate free `<input type="text">` and dedicated numeric keypads within the same task.

#### 5.1.3.11.1.13 – Data entry display
*Summary (shall):* Where data entry on an electronic display is permitted only in prescribed areas, a clear visual definition of the entry fields shall be provided.

- **IMPL-5.1.3.11.1.13-01** `[CSS]` `[Main]` Editable fields shall have a distinctive border/background (`border: 1px solid var(--color-fg)`, `background: var(--color-bg)` with dedicated contrast) compared to read-only areas, applied via the `.field-editable` class.

#### 5.1.3.11.3.3 – Length
*Summary (shall):* The length of individual data items shall be no more than necessary to accomplish the task.

- **IMPL-5.1.3.11.3.3-01** `[HTML][JS]` `[Main]` Every `<input>`/`<textarea>` shall set `maxlength` to the minimum value necessary defined in `config.js` for the field, verified by `validate.js`.

#### 5.1.3.11.3.4 – Justification
*Summary (shall):* When entering tabular data the user shall not be required to right- or left-justify entries; the system shall automatically justify columns relative to the decimal point, left margin, or right margin depending on the data type.

- **IMPL-5.1.3.11.3.4-01** `[CSS]` `[Main]` Numeric table cells shall use `text-align: right` aligned to the decimal point (`font-variant-numeric: tabular-nums`); text cells `text-align: left`, applied automatically based on column type, without user intervention.
- **IMPL-5.1.3.11.3.4-02** `[JS]` `[Main]` `validate.js` shall format the entered value (padding/alignment) on focus loss, without requiring the user to type justification spaces.

#### 5.1.3.11.3.6 – Minimization of keying
*Summary (shall):* The amount of keying required shall be no more than necessary to accomplish the task.

- **IMPL-5.1.3.11.3.6-01** `[JS][HTML]` `[Main]` Forms shall use `<select>`, `autocomplete`, values prefilled from `state.js`, and reasonable defaults to minimize manual keying, especially on touchscreens.

#### 5.1.3.11.3.7 – Minimization of shift keying
*Summary (shall):* The use of keyboard shift functions shall be minimized during data entry transactions.

- **IMPL-5.1.3.11.3.7-01** `[JS]` `[Main]` The virtual touch keyboard managed by `feedback.js`/input component shall set the case (upper/lower) and the most likely character set for the field (e.g. numeric, uppercase for codes), avoiding repeated layout/shift changes.

#### 5.1.3.11.3.8 – Data change
*Summary (shall):* In keyed data entry, means shall be provided to allow users to change previous entries by overtyping existing text or inserting new text at any position in the field.

- **IMPL-5.1.3.11.3.8-01** `[HTML]` `[Main]` `<input>`/`<textarea>` fields shall natively support both overtype mode (Ins key) and text insertion at any cursor position, without disabling cursor selection/positioning.

#### 5.1.3.11.4.2 – Standardization
*Summary (shall):* Fixed function keys shall be common throughout the system.

- **IMPL-5.1.3.11.4.2-01** `[HTML][CSS]` `[Footer]` `#function-keys` shall use the same set of `<button class="fn-key" data-fn="...">` with consistent labels/positions across all views of the application, defined once in `nav.js`.

#### 5.1.3.11.4.3 – Functional consistency
*Summary (shall):* Once a function has been assigned to a key, it shall not be reassigned to a different function for a given user.

- **IMPL-5.1.3.11.4.3-01** `[JS]` `[Footer]` `state.js`/`config.js` shall keep the key→function association constant for the user session; `nav.js` shall not dynamically remap the same `data-fn` to different commands.

#### 5.1.3.11.4.4 – Availability
*Summary (shall):* Fixed function keys shall be selected to control continuously available functions (lockout of fixed function keys shall be minimized).

- **IMPL-5.1.3.11.4.4-01** `[JS]` `[Footer]` `nav.js` shall keep enabled (no `[disabled]`) the `#function-keys` keys associated with always-available functions (home, help, back) in every application state.

#### 5.1.3.11.4.4.1 – Function keys not currently used
*Summary (shall):* At any step in a transaction sequence, function keys not used for current input shall be temporarily disabled under computer control.

- **IMPL-5.1.3.11.4.4.1-01** `[JS][HTML]` `[Footer]` `nav.js` shall set `[disabled]`/`[aria-disabled="true"]` on `.fn-key` elements not relevant to the current step of the transaction in `#content-region`, re-enabling them automatically at step change.

#### 5.1.3.11.4.5 – Non-active keys
*Summary (shall):* Non-active fixed function keys shall be replaced by a blank key or displayed grayed-out.

- **IMPL-5.1.3.11.4.5-01** `[CSS]` `[Footer]` The `.fn-key[disabled]` class shall apply a "grayed-out" style (`opacity`, `color: var(--color-inactive)`) distinct from the active state, without removing the label.

#### 5.1.3.11.4.6 – Grouping
*Summary (shall):* Fixed function keys shall be logically grouped and placed in distinctive locations on the keyboard.

- **IMPL-5.1.3.11.4.6-01** `[HTML][CSS]` `[Footer]` `#function-keys` shall group `.fn-key` elements into semantic sub-containers (e.g. `<div class="fn-group" data-group="nav|edit|system">`) with visual separation (`gap`, border) by functional category.

#### 5.1.3.11.4.7 – Actuation
*Summary (shall):* Except when used to toggle between two opposing states, a fixed function key shall require only a single actuation to accomplish its function.

- **IMPL-5.1.3.11.4.7-01** `[JS]` `[Footer]` The `pointerup` handler for `.fn-key` in `nav.js` shall execute the action with a single actuation; only keys with `[aria-pressed]` (toggle) may alternate state on each actuation.

#### 5.1.3.11.4.8 – Feedback
*Summary (shall):* When activation of a fixed function key does not produce an immediately observable natural response, the user shall receive an indication of system acknowledgment.

- **IMPL-5.1.3.11.4.8-01** `[JS]` `[Footer]` `feedback.js.acknowledge(el)` shall be invoked on every `.fn-key` actuation lacking an immediate visual effect, showing feedback (brief highlight, `beep()`) within the times of Table V (≤0.1 s), see `IMPL-5.1.3.11.1.6-01`.

#### 5.1.3.11.4.9 – Fixed function key labels
*Summary (shall):* Fixed function key assignments shall always be displayed.

- **IMPL-5.1.3.11.4.9-01** `[HTML]` `[Footer]` Every `.fn-key` shall always contain a visible text label (not just an icon) in `#function-keys`, never empty.

#### 5.1.3.11.4.9.1 – Direct marking
*Summary (shall):* Fixed function key assignments shall be displayed by direct marking.

- **IMPL-5.1.3.11.4.9.1-01** `[HTML][CSS]` `[Footer]` The `.fn-key` label shall be rendered as text/icon directly on the key (content of the `<button>`), not via a tooltip or separate legend.

#### 5.1.3.11.4.9.2 – Abbreviations
*Summary (shall):* Where abbreviations are necessary for fixed function keys, standard abbreviations shall be used.

- **IMPL-5.1.3.11.4.9.2-01** `[JS]` `[Footer]` Abbreviated `.fn-key` labels shall come from the standard table in `i18n.js`/`config.js` (see `IMPL-5.1.3.11.1.8-01`), not from free per-view text.

#### 5.1.3.11.4.10 – Prolonged function key pressing
*Summary (shall):* Except for the DELETE key, prolonged pressing of function keys shall not repeat the function.

- **IMPL-5.1.3.11.4.10-01** `[JS]` `[Footer]` The `.fn-key` event handler shall respond only to `pointerup` (single actuation), without `pointerdown`/repeated-interval listeners, except for the `data-fn="delete"` key, which may use controlled repeat in `config.js`.

#### 5.1.3.11.5.2 – Critical action
*Summary (shall):* A single entry on a variable function key shall not produce a critical action.

- **IMPL-5.1.3.11.5.2-01** `[JS]` `[Footer]` Variable function keys (`.vfn-key`, defined by `state.js` per view) associated with critical actions (`data-critical="true"`) shall always require a confirmation step handled by `dialog.js`, never executing the action on the first actuation.

#### 5.1.3.11.5.3 – Critical action confirmation
*Summary (shall):* Critical actions shall require confirmation when initiated with a variable function key.

- **IMPL-5.1.3.11.5.3-01** `[JS][HTML]` `[Footer][Main]` `dialog.js` shall open a confirmation `<dialog>` ("Confirm"/"Cancel") before executing any action with `data-critical="true"` originated from a variable function key, see `IMPL-5.1.3.11.5.2-01`.

#### 5.1.3.11.5.4 – Status display
*Summary (shall):* When the effect of a function key varies, the status of the key shall be displayed.

- **IMPL-5.1.3.11.5.4-01** `[HTML][CSS]` `[Footer]` `.vfn-key` elements whose effect changes with context shall show the current label/icon updated by `state.js` and an `[aria-pressed]`/`[data-state]` attribute reflected in CSS.

#### 5.1.3.11.5.5 – Reprogrammable or inactive default functions
*Summary (shall):* When keys with labeled default functions are reprogrammed or deactivated, a visual warning shall alert the user that the standard function is not currently accessible via that key.

- **IMPL-5.1.3.11.5.5-01** `[CSS][JS]` `[Footer]` When `nav.js` reassigns or deactivates a `.vfn-key` relative to the standard function, it shall apply an `.is-remapped` class/visual badge (`--color-warning`) and update the `aria-label` accordingly.

#### 5.1.3.11.5.8 – Easy return to base-level functions
*Summary (shall):* Where the functions assigned to a set of function keys change as a result of user selection, an easy means shall be provided to return to the initial base-level functions.

- **IMPL-5.1.3.11.5.8-01** `[HTML][JS]` `[Footer]` `#function-keys` shall include a persistent `data-fn="base-level"` key (e.g. "Base") that invokes `nav.js` to restore the initial assignment of the `.vfn-key` elements in a single tap.

#### 5.17.2.1.1 – Use
*Summary (may):* Command language may be used for tasks involving a wide range of user input and where familiarity with command language can leverage the flexibility and speed of this control technique.

- **IMPL-5.17.2.1.1-01** `[JS]` `[Main]` An optional command-line mode shall exist (`command-palette`, component in `#content-region`/`#aux-region`) managed by a dedicated module (e.g. `cmd.js`/`nav.js` extension), enabled by the expert user in `prefs.js`, without replacing the equivalent graphical controls.

#### 5.17.2.1.2 – User viewpoint
*Summary (shall):* Command language commands shall be logically related to what the user expects the command to initiate.

- **IMPL-5.17.2.1.2-01** `[JS]` `[Global]` Command names defined in `config.js` shall correspond semantically to the resulting action (e.g. `open`, `close`, `ack`) and follow a verb-object naming convention verifiable in review.

#### 5.17.2.1.3 – Naming distinctiveness
*Summary (shall):* Command names shall be distinct from one another.

- **IMPL-5.17.2.1.3-01** `[JS]` `[Global]` `validate.js`/the build tool shall verify that the command table in `config.js` contains no duplicate or ambiguous names (common prefixes resolvable only by truncation).

#### 5.17.2.1.4 – Punctuation and special characters
*Summary (shall):* The command language dialog shall minimize the use of punctuation and special characters.

- **IMPL-5.17.2.1.4-01** `[JS]` `[Main]` The command parser shall accept syntax based on simple space-separated words, avoiding symbols such as `;`, `|`, `&&` except for minimal indispensable operators defined in `config.js`.

#### 5.17.2.1.5 – Standardization
*Summary (shall):* All commands and corresponding abbreviations shall be identical across systems and platforms.

- **IMPL-5.17.2.1.5-01** `[JS]` `[Global]` The command/abbreviation table shall reside in a shared `config.js` and shall not be duplicated or redefined per platform/view, see `IMPL-5.1.3.11.1.8.3-01`.

#### 5.17.2.1.6 – Standard location
*Summary (shall):* Commands shall be entered and displayed in a standard location on the software user interface.

- **IMPL-5.17.2.1.6-01** `[HTML][CSS]` `[Main]` The command bar/field shall have a fixed position (e.g. at the top of `#content-region` or in `#aux-region`), identical across all views where it is available.

#### 5.17.2.1.7 – Command prompts
*Summary (shall):* The user shall be provided a means, such as prompts or tooltips, to determine required parameters or available options for command entry.

- **IMPL-5.17.2.1.7-01** `[HTML][JS]` `[Main]` The command field shall show an inline hint (`<datalist>`/autocomplete popup updated by `cmd.js`) with expected parameters and available options as the user types.

#### 5.17.2.1.8 – Complexity
*Summary (shall):* The command language shall accommodate user skill levels from beginner to expert.

- **IMPL-5.17.2.1.8-01** `[JS]` `[Global]` `prefs.js` shall expose an "expert/guided mode" preference that shows/hides extended hints and allows abbreviated command forms only in expert mode.

#### 5.17.2.1.9 – User definition of macro commands
*Summary (shall):* The command dialog shall not allow a user to name macros with existing command names ("reserved keywords").

- **IMPL-5.17.2.1.9-01** `[JS]` `[Main]` `validate.js` shall reject (error in `#message-line`) creation of a macro whose name matches an entry in the reserved command table in `config.js`.

#### 5.17.2.1.10 – Standard techniques for command editing
*Summary (shall):* Command and data entries shall be edited using the same techniques.

- **IMPL-5.17.2.1.10-01** `[HTML][JS]` `[Main]` The command entry field shall be a standard `<input>` with the same editing modes (overtype/insert, selection) as other data fields, see `IMPL-5.1.3.11.3.8-01`.

#### 5.17.2.1.11 – Destructive commands
*Summary (shall):* If a command entry has destructive consequences, the system shall display the consequences of the command so the user may review and cancel or confirm before execution.

- **IMPL-5.17.2.1.11-01** `[JS][HTML]` `[Main]` Before executing a command marked `data-destructive="true"`, `dialog.js` shall show a summary of the consequences in a `<dialog>` with "Confirm"/"Cancel" actions, see `IMPL-5.1.3.11.5.3-01`.

#### 5.17.2.2.1 – Use
*Summary (may):* Question-and-answer dialogs may be used for routine data entry tasks where the data is known, their order can be constrained, and users will have little or no training.

- **IMPL-5.17.2.2.1-01** `[HTML][JS]` `[Main]` For guided tasks aimed at untrained users, a "question-and-answer" wizard component shall be available (sequence of `<fieldset>`/steps in `#content-region` managed by `nav.js`) as an alternative to the free-form form.

#### 5.17.2.2.2 – Questions displayed separately
*Summary (shall):* For multiple questions in a dialog box, each question shall be listed separately.

- **IMPL-5.17.2.2.2-01** `[HTML][CSS]` `[Main]` Each wizard question shall be a distinct element (`<label>`/block) with visual separation (minimum margin `--touch-gap-min`) from the others, never concatenated into a single block of text.

#### 5.17.2.2.3 – Distinct response area
*Summary (shall):* When responses are required for a question, each question shall have a separate and distinct response area.

- **IMPL-5.17.2.2.3-01** `[HTML][CSS]` `[Main]` Each question shall associate a single response field via `<label for>`/`aria-labelledby`, with a dedicated touch area ≥ `--touch-target-min`, not shared with other questions.

#### 5.17.2.2.4 – Recapitulating prior answers
*Summary (shall):* When a series of system-posed questions are interrelated, answers to previous questions shall be displayed if they provide context helpful to answering the current question.

- **IMPL-5.17.2.2.4-01** `[JS][HTML]` `[Main]` The wizard shall show a read-only recap of relevant previous answers (from `state.js`) above/next to the current question when interrelated.

#### 5.17.2.2.5 – Source document capability
*Summary (shall):* When questions prompt entry of data from a source document, the question sequence shall match the data sequence in the source document.

- **IMPL-5.17.2.2.5-01** `[JS]` `[Main]` The order of wizard steps configured in `config.js` shall be configurable to mirror the sequence of the task's reference source document, documented in the form configuration.

#### 5.17.2.3.1 – Natural organization of data
*Summary (should):* Query languages should reflect a data structure/organization perceived by users as natural, allowing retrieval with a single query even if the data resides in different files.

- **IMPL-5.17.2.3.1-01** `[JS]` `[Main]` The query interface (`net.js`) shall expose a unified data model per topic (e.g. "alarms", "sessions") regardless of the underlying sources/endpoints, allowing a single user query per topic.

#### 5.17.2.3.2 – Coherent representation of data organization
*Summary (shall):* A single representation of the data organization for query formulation shall be established (the user need not know whether different queries access different databases via different routes).

- **IMPL-5.17.2.3.2-01** `[JS]` `[Global]` `net.js` shall encapsulate routing to multiple endpoints/databases behind a single query API consumed by the interface, without exposing source details in the UI.

#### 5.17.2.3.3 – Task-oriented wording
*Summary (shall):* Users shall be able to specify the data requested in a query without having to specify where it is stored.

- **IMPL-5.17.2.3.3-01** `[HTML][JS]` `[Main]` The query form shall expose only semantic fields (e.g. "date range", "event type") and never physical table/database selectors.

#### 5.17.2.3.4 – Logic to link queries
*Summary (should):* The query language should include logic elements allowing users to link (e.g. "and", "or") sequential queries into a single entry.

- **IMPL-5.17.2.3.4-01** `[HTML][JS]` `[Main]` The query builder shall offer controls to combine conditions with "and"/"or" operators (e.g. `<select>` between filters), translated by `i18n.js` into natural language.

#### 5.17.2.3.5 – Confirming large-scale data retrieval
*Summary (shall):* If a user's query request will retrieve a large amount of data, the user shall be provided options to continue, cancel, or refine the query during processing.

- **IMPL-5.17.2.3.5-01** `[JS][HTML]` `[Main]` When `net.js` estimates a result beyond a threshold (`config.js`), `dialog.js` shall present a `<dialog>` with "Continue"/"Cancel"/"Refine query" actions before proceeding.

#### 5.17.2.3.6 – Estimate of remaining time
*Summary (shall):* When processing takes a long time, the user shall be provided with a status indication that includes an estimate of the remaining proportion in terms of time or amount.

- **IMPL-5.17.2.3.6-01** `[JS][HTML]` `[Footer]` `#progress-indicator` shall show the estimated remaining percentage/time updated by `net.js`/`feedback.js.showProgress()` for every query exceeding 1 s (see Table V in §2.6).

#### 5.17.3.1.1 – Drop-down menu bars
*Summary (shall):* Drop-down menus contain a list of contextual commands presented in a menu bar along the top edge of a window. The menu bar shall contain a set of category labels (e.g. "Edit") from which drop-down menus are accessed.

- **IMPL-5.17.3.1.1-01** `[HTML]` `[Header][Main]` The main menu bar shall be implemented as `<nav>`/`<ul role="menubar">` with `role="menuitem"` entries per category (e.g. "File", "Edit"), placed in `#nav-region` or `#app-header`.
- **IMPL-5.17.3.1.1-02** `[JS]` `[Main]` `nav.js` shall open the drop-down menu associated with the selected category (`aria-expanded="true"`), showing the list of contextual commands for that category.

#### 5.17.3.1.1.1 – Use
*Summary (should):* Drop-down menus and the associated menu bars should be used when there are more than three commands and categories.

- **IMPL-5.17.3.1.1.1-01** `[JS]` `[Global]` The choice between direct controls (buttons) and drop-down menus shall be driven by a threshold in `config.js` (default: >3 commands/categories → drop-down menu).

#### 5.17.3.1.1.2 – Primary windows
*Summary (should):* Drop-down menus and menu bars should be used when commands appear in primary windows (i.e. popup dialogs).

- **IMPL-5.17.3.1.1.2-01** `[HTML]` `[Main]` Commands presented in primary views of `#content-region` (not in secondary `<dialog>` elements) shall prefer drop-down menu organization over scattered controls.

#### 5.17.3.1.2 – Submenus
*Summary (shall):* Submenus shall consist of a group of options related to a higher-level toolbar menu item.

- **IMPL-5.17.3.1.2-01** `[HTML][JS]` `[Main]` Every submenu shall be a nested `<ul role="menu">`, whose items are semantically related to the parent menu item, managed by `nav.js`.

#### 5.17.3.1.2.1 – Additional menu options
*Summary (should):* Submenus should allow additional menu options to be viewed without increasing the length of the overall menu.

- **IMPL-5.17.3.1.2.1-01** `[CSS][JS]` `[Main]` When a menu exceeds the visible-item threshold (`config.js`), `nav.js` shall group the excess items into a submenu rather than lengthening the main list.

#### 5.17.3.1.2.2 – Shorten length
*Summary (may):* Submenus may be used to shorten the length of a long menu when a higher-level item can describe several related functionalities.

- **IMPL-5.17.3.1.2.2-01** `[JS]` `[Global]` The menu structure in `config.js` shall allow declaring a "container" item that groups related functionalities into a submenu, reducing the length of the main menu.

#### 5.17.3.1.2.3 – Frequently needed functions
*Summary (should):* Submenus should not be used for functions that are frequently needed and accessed.

- **IMPL-5.17.3.1.2.3-01** `[JS]` `[Global]` High-frequency-use items (`data-frequent` flag in `config.js`) shall remain at the top level of the menu/toolbar, not nested in submenus, see `IMPL-5.17.3.2.9-01`.

#### 5.17.3.1.2.4 – Indication
*Summary (should):* An indicator (e.g., a right-pointing triangle) should be used to signal the presence of a submenu on a higher-level menu item.

- **IMPL-5.17.3.1.2.4-01** `[HTML][CSS]` `[Main]` Menu items with submenus shall include a graphical indicator (`::after` with glyph `▶`/SVG icon) next to the label, managed by a shared CSS token.

#### 5.17.3.1.2.5 – Presentation of options
*Summary (shall):* When the higher-level option is selected, the options shall be presented without further user input.

- **IMPL-5.17.3.1.2.5-01** `[JS]` `[Main]` `nav.js` shall automatically open the submenu associated with the first tap/activation of the parent item (`pointerup`), without requiring a second opening gesture.

#### 5.17.3.1.2.6 – Number of levels
*Summary (should):* Submenus should be limited to three levels (Main>Sub1>Sub2) to ensure discoverability of nested items.

- **IMPL-5.17.3.1.2.6-01** `[JS]` `[Global]` The navigation structure in `config.js`/`nav.js` shall validate that menu depth does not exceed 3 levels; deeper structures shall be flagged during review.

#### 5.17.3.1.3 – Menu bar
*Summary (should):* Users should interact with menu bars by left-clicking the menu category to open the drop-down command list.

- **IMPL-5.17.3.1.3-01** `[HTML][JS]` `[Header][Main]` Menu bar categories (`#nav-region` or `#app-header`) shall respond to `pointerup` (left click/tap) to open the associated drop-down menu, consistent across mouse and touch.

#### 5.17.3.1.3.1 – Toolbar menu
*Summary (may):* Toolbars may be used to graphically present command actions for easy and efficient access.

- **IMPL-5.17.3.1.3.1-01** `[HTML][CSS]` `[Main]` An optional toolbar (`<div role="toolbar">` in `#nav-region`/`#content-region`) may present command icons for the most frequent actions, in addition to the drop-down menu.

#### 5.17.3.1.3.2 – Use in primary windows
*Summary (should):* Toolbars should be used in primary windows to provide a small number of icons as immediate commands.

- **IMPL-5.17.3.1.3.2-01** `[HTML][CSS]` `[Main]` In the main view (primary `#content-region`, not in a dialog) the toolbar shall be limited to a small number of icons (project value in `config.js`, default ≤7) for immediate commands.

#### 5.17.3.1.3.3 – Use in secondary windows
*Summary (should):* Toolbars should not be used in secondary windows, with text-only commands, crowded icons, and non-immediate commands.

- **IMPL-5.17.3.1.3.3-01** `[HTML]` `[Main]` Secondary `<dialog>` elements managed by `dialog.js` shall not include icon toolbars; actions shall be presented as explicit text buttons.

#### 5.17.3.1.3.4 – Toolbar options
*Summary (should):* Fixed toolbar options should be limited to the commands used most frequently by a large percentage of users, or a means for custom configuration shall be provided.

- **IMPL-5.17.3.1.3.4-01** `[JS]` `[Main]` `prefs.js` shall allow the user to customize toolbar entries from an allowed subset defined in `config.js`, keeping only the most frequent commands by default.

#### 5.17.3.1.3.5 – Toolbar labels
*Summary (shall):* Toolbars with icons shall provide labels via a tooltip (hover to show the label).

- **IMPL-5.17.3.1.3.5-01** `[HTML]` `[Main]` Every toolbar icon button shall have a native `title`/tooltip and an equivalent `aria-label`, since hover is not available on touch (see `IMPL-5.17.3.1.3.6-01`).

#### 5.17.3.1.3.6 – Non-standard icons
*Summary (should/shall):* Non-standard icons should be avoided but, if present, shall provide labels via hover tooltip and should include fixed text labels.

- **IMPL-5.17.3.1.3.6-01** `[HTML][CSS]` `[Main]` For icons not belonging to the standard set in `config.js`, the toolbar shall also show a fixed text label below/next to the icon, not just the tooltip (touch requirement, see rule 5 of the brief).

#### 5.17.3.1.3.7 – Selection
*Summary (should):* Toolbar menus should be selected with a left click for devices such as mice and trackballs.

- **IMPL-5.17.3.1.3.7-01** `[JS]` `[Main]` Toolbar buttons shall handle selection via a unified `pointerup` event (left click on mouse, tap on touch), with no dependency on the right-click button.

#### 5.17.3.1.4 – Ribbon menus
*Summary (may):* Ribbon menus separate task-dedicated commands within a window; a list of task-related commands appears under an active tab; they may be used with multiple menu categories with items under each.

- **IMPL-5.17.3.1.4-01** `[HTML][CSS][JS]` `[Main]` If adopted, the ribbon shall be built with `<div role="tablist">`/`<button role="tab">` in `#nav-region` and `role="tabpanel"` panels in `#content-region`, managed by `nav.js` with `aria-selected`.

#### 5.17.3.1.5 – Right-click and context menus
*Summary (may):* Right-click menus may provide contextually relevant options and actions pertaining only to the selected item or pane.

- **IMPL-5.17.3.1.5-01** `[JS][HTML]` `[Main]` An optional context menu (`contextmenu`/long `pointerdown` for touch) may be offered in `#content-region` for actions specific to the selected element, managed by `nav.js`.

#### 5.17.3.1.5.1 – Method of accomplishment
*Summary (shall):* Right-click menus shall not provide the only method for accomplishing a command.

- **IMPL-5.17.3.1.5.1-01** `[JS]` `[Main]` Every action available in the context menu shall also be reachable via the main menu/toolbar/function key, verified during code review.

#### 5.17.3.1.5.2 – Accessibility
*Summary (shall):* Right-click and context menus shall not include options that are not accessible elsewhere by an apparent means.

- **IMPL-5.17.3.1.5.2-01** `[JS]` `[Main]` `nav.js` shall not register commands exclusive to the context menu; every entry shall have an alternative path documented in the drop-down menu or toolbar.

#### 5.17.3.1.5.3 – Availability
*Summary (should):* Right-click menus should be available to initiate basic, frequently used commands.

- **IMPL-5.17.3.1.5.3-01** `[JS]` `[Main]` The context menu, when present, shall be limited to the frequently used commands for the selected element (defined per element type in `config.js`).

#### 5.17.3.1.5.4 – Not substitute
*Summary (shall):* Right-click menus shall not be used as a substitute for primary menus (e.g., drop-down menus).

- **IMPL-5.17.3.1.5.4-01** `[JS]` `[Global]` `nav.js` shall keep the drop-down menu/navigation bar as the primary path for all commands; the context menu remains an optional accelerator, never the only architectural entry point.

#### 5.17.3.2.1 – Interactive menu use
*Summary (shall):* Interactive menu selection shall be used for tasks involving little or no arbitrary data entry, users with relatively little training, or a command set too large to memorize.

- **IMPL-5.17.3.2.1-01** `[HTML][JS]` `[Main]` `#nav-region` shall offer menu selection (not just text commands) as the primary path for all tasks with a large command set or inexperienced users, managed by `nav.js`.

#### 5.17.3.2.2 – Selection method
*Summary (shall):* Device selection shall comply with the criteria in 5.17.3.2.2.1–5.17.3.2.2.4.

- **IMPL-5.17.3.2.2-01** `[JS]` `[Global]` Menu selection handlers shall be implemented with unified Pointer Events, covering the requirements of the following subsections.

#### 5.17.3.2.2.1 – Mouse
*Summary (shall):* A mouse or other pointing device (including touch technology) shall be an available method for menu selection.

- **IMPL-5.17.3.2.2.1-01** `[JS]` `[Global]` All menu items shall handle generic `pointerdown/up`, working with both mouse and touch, without separate exclusive `click`/`touchstart` handlers.

#### 5.17.3.2.2.2 – Pointing
*Summary (shall):* If menu selection is accomplished by pointing, a selection action shall initiate the command (left mouse click or tap for touch input).

- **IMPL-5.17.3.2.2.2-01** `[JS]` `[Main]` Menu item selection shall be triggered on `pointerup` over the target (*last contact* strategy, see §2.6), not on simple hover/traversal.

#### 5.17.3.2.2.3 – Submenus
*Summary (shall):* Each submenu shall have a title that clearly identifies the purpose of that menu.

- **IMPL-5.17.3.2.2.3-01** `[HTML]` `[Main]` Each submenu `<ul role="menu">` shall have a heading/`aria-label` describing its purpose, not merely inheriting the parent item's name.

#### 5.17.3.2.2.4 – Multiple pages
*Summary (shall):* Menus shall not consist of multiple pages of options.

- **IMPL-5.17.3.2.2.4-01** `[CSS][JS]` `[Main]` Menus shall use internal vertical scrolling (`overflow-y: auto` on `max-block-size`) instead of pagination; `nav.js` shall not implement "next page" controls in menus.

#### 5.17.3.2.3 – Active option presentation
*Summary (shall):* Unavailable menu options shall either not be presented or be greyed out to make clear they are unavailable.

- **IMPL-5.17.3.2.3-01** `[HTML][CSS]` `[Main]` Unavailable menu items shall have `[disabled]`/`[aria-disabled="true"]` with greyed-out styling (`.menu-item[aria-disabled]`), or be omitted from the DOM, never shown as active but non-functional.

#### 5.17.3.2.4 – Format consistency
*Summary (shall):* Menus shall be presented in a consistent format throughout the system and across related systems the user is expected to operate.

- **IMPL-5.17.3.2.4-01** `[CSS]` `[Global]` All menus shall use the same CSS tokens (spacing, typography, states) defined in §2.4, with no local variants per view.

#### 5.17.3.2.5 – Accessible
*Summary (shall):* Menus shall be readily accessible at all times.

- **IMPL-5.17.3.2.5-01** `[HTML][JS]` `[Header][Main]` The main menu access point (`#nav-region` or toggle in `#app-header`) shall remain available and not hidden by any application view or blocking modal dialog for longer than the minimum necessary time.

#### 5.17.3.2.6 – Option sequence
*Summary (shall):* Menu selections shall be listed in a logical order, such as alphabetically or by frequency of use.

- **IMPL-5.17.3.2.6-01** `[JS]` `[Global]` The order of menu items in `config.js` shall be explicitly alphabetical or by frequency of use (declared in configuration), not random.

#### 5.17.3.2.7 – Simple menus
*Summary (may/should):* If selections fit on one page in no more than two columns, a single menu may be used; if options exceed two columns, a hierarchical menu structure should be employed.

- **IMPL-5.17.3.2.7-01** `[CSS][JS]` `[Main]` `nav.js` shall automatically switch from a flat menu to a hierarchical structure (submenus) when the item count exceeds the threshold of two displayable columns at `--touch-target-min`, calculated at runtime based on the breakpoint.

#### 5.17.3.2.8 – Option presentation
*Summary (should):* Textual menu options should be presented on separate lines to ensure items are discriminable and selectable.

- **IMPL-5.17.3.2.8-01** `[CSS]` `[Main]` Menu items shall be single-row list elements (`<li>`) (`display: block`/`flex`), never inline text side by side on the same line.

#### 5.17.3.2.9 – Direct function call
*Summary (shall):* A direct function call capability shall be provided for frequently used options.

- **IMPL-5.17.3.2.9-01** `[JS][HTML]` `[Footer]` The most frequent menu options shall be duplicated as a function key in `#function-keys` or a direct shortcut, to avoid full menu navigation.

#### 5.17.3.2.10 – Shortcut commands
*Summary (should):* Shortcut commands (e.g., "control C" for copy) should be provided for frequently used actions.

- **IMPL-5.17.3.2.10-01** `[JS]` `[Global]` `nav.js` shall register standard keyboard shortcuts (global `keydown`) for the most frequent actions, as an alternative secondary keyboard input.

#### 5.17.3.2.11 – Option coding
*Summary (shall):* When selections can be made via keyboard shortcuts, the shortcuts associated with each alternative option shall be included next to the menu option.

- **IMPL-5.17.3.2.11-01** `[HTML][CSS]` `[Main]` Every menu item with a shortcut shall show the key combination next to the label (e.g., `<span class="menu-shortcut">Ctrl+C</span>`), right-aligned within the item.

#### 5.17.3.2.12 – Keyboard shortcuts
*Summary (shall):* If menu selections are made by keyboard entry of alphanumeric codes, options shall be coded with letters associated with the option name or standard shortcuts, not arbitrary codes.

- **IMPL-5.17.3.2.12-01** `[JS]` `[Global]` Keyboard mnemonics (`accesskey`/handling in `nav.js`) shall derive from the initial letter or a significant letter of the option name, never from arbitrary numeric codes.

#### 5.17.3.2.13 – Duplication
*Summary (shall):* Keyed codes shall not duplicate other user function codes.

- **IMPL-5.17.3.2.13-01** `[JS]` `[Global]` `validate.js`/the build tool shall verify the uniqueness of keyboard bindings in `config.js` across menus, function keys, and global shortcuts.

#### 5.17.3.2.14 – Position in structure
*Summary (shall):* When menu selection traverses multiple levels, all levels shall remain visible until the selection is made.

- **IMPL-5.17.3.2.14-01** `[CSS][JS]` `[Main]` Hierarchical navigation shall simultaneously show (breadcrumb or cascading columns) all menu levels traversed up to the final selection, without replacing the previous level's view.

#### 5.17.3.2.15 – Menu hierarchy
*Summary (shall):* A menu tree showing the hierarchy shall be included in the user manual or online Help section.

- **IMPL-5.17.3.2.15-01** `[HTML][JS]` `[Main]` The online Help section (opened from `#btn-help`) shall include a tree view (nested `<ul>`) mirroring the entire `#nav-region` hierarchy, generated from `nav.js`/`config.js`.

#### 5.17.3.2.16 – Tooltips
*Summary (should):* A tooltip should be displayed over an item when the user hovers the pointer without clicking.

- **IMPL-5.17.3.2.16-01** `[HTML][JS]` `[Main]` Menu items shall have a `title`/tooltip on hover for mouse input; for touch, in the absence of hover, equivalent information shall always be visible as text (see rule 5 of the brief, no hover-only functions).

#### 5.17.3.2.17 – Icons
*Summary (may):* Menus may use icons to represent control options.

- **IMPL-5.17.3.2.17-01** `[HTML][CSS]` `[Main]` Menu items may include an SVG icon/`<img>` next to the text label (never an icon alone, see `IMPL-5.1.3.12.8.3` in section 5 of the document).

#### 5.17.3.2.17.1 – Currently being used
*Summary (should):* When possible, icons should be consistent with those currently used in other systems, familiar to the user, and used consistently within the application.

- **IMPL-5.17.3.2.17.1-01** `[CSS][JS]` `[Global]` The icon set shall be defined once (SVG sprite/icon library in `config.js`) and reused identically across all menus, with no local variants.

#### 5.17.3.2.17.2 – Graphic menu use
*Summary (may):* Graphic menus may use icons to represent control options and be consistent within an application.

- **IMPL-5.17.3.2.17.2-01** `[CSS]` `[Global]` Graphic icon menus (e.g., quick navigation dashboards) shall share the same icon set/style as in `IMPL-5.17.3.2.17.1-01`.

#### 5.17.4.1 – Data entry
*Summary (shall):* Data entry functions shall be designed to establish transaction consistency, minimize input actions, minimize memory load on the user, ensure compatibility between data entry and display, and give the user control over data entry.

- **IMPL-5.17.4.1-01** `[JS][HTML]` `[Main]` All forms shall share common patterns (same `<input>` components, same `validate.js` error handling, same label-field order) and the form layout shall mirror the corresponding data display (same field order), under explicit user control (no autosubmit).

#### 5.17.4.2 – User pacing
*Summary (shall):* The pace of data entry shall be controlled by the user, not by the system.

- **IMPL-5.17.4.2-01** `[JS]` `[Main]` No form shall automatically advance to the next field or submit data on a timeout; advancement occurs only on an explicit user action (see `IMPL-5.17.4.7-01`).

#### 5.17.4.3 – Positive feedback
*Summary (shall):* The system shall provide feedback to the user regarding the acceptance or rejection of an entered datum.

- **IMPL-5.17.4.3-01** `[JS]` `[Main]` `feedback.js.setStatus()`/`validate.js` shall show the outcome (accepted/rejected) in `#message-line` or inline on the field for every completed entry.

#### 5.17.4.4 – Response time
*Summary (shall):* Feedback response times shall comply with 5.17.9.5.

- **IMPL-5.17.4.4-01** `[JS]` `[Global]` `feedback.js` shall comply with the times in Table V (§2.6): character echo ≤0.2 s, error ≤0.2 s, local update ≤0.5 s.

#### 5.17.4.5 – Processing delay
*Summary (shall):* Where system overload or other conditions cause a processing delay, the system shall acknowledge the data entry and indicate the delay to the user.

- **IMPL-5.17.4.5-01** `[JS][HTML]` `[Footer]` In case of a delay beyond 1 s, `net.js` shall acknowledge receipt of the input (`#status-line`) and show `#progress-indicator` with an indication of the delay, see `IMPL-5.17.2.3.6-01`.

#### 5.17.4.6 – Completion time
*Summary (should):* The system should provide an indication of the completion time or the fraction of the process completed.

- **IMPL-5.17.4.6-01** `[JS]` `[Footer]` `#progress-indicator` shall expose, when available, the percentage completed and/or estimated remaining time for long data entry processes.

#### 5.17.4.7 – Explicit action
*Summary (shall):* Each data entry shall require an explicit completion action, such as pressing an ENTER key or another key (e.g., TAB).

- **IMPL-5.17.4.7-01** `[HTML][JS]` `[Main]` Every field shall confirm its value only on a `change`/`keydown` (Enter/Tab) event or an explicit "Confirm" button, never on continuous `input` without a completion action.

#### 5.17.4.8 – Validation
*Summary (shall):* Data entries shall be validated by the system for correct format, legal value, and range of values before processing.

- **IMPL-5.17.4.8-01** `[JS]` `[Main]` `validate.js` shall check format (regex/type), allowed value, and numeric range of every field before forwarding data to `net.js`, setting `aria-invalid` and a message on failure.

#### 5.17.4.9 – Software-available data
*Summary (shall):* The user shall not be required to enter data already available to the software.

- **IMPL-5.17.4.9-01** `[JS]` `[Main]` Forms shall automatically pre-fill fields whose value is already known from `state.js`/`net.js` (e.g., current user, station, date), making them at most editable but never empty.

#### 5.17.4.10 – Input units
*Summary (shall):* Data shall be entered in units familiar to the user.

- **IMPL-5.17.4.10-01** `[JS][HTML]` `[Main]` `i18n.js`/`prefs.js` shall determine the unit of measure displayed/accepted for each numeric field based on user/locale preference, with transparent internal conversion.

#### 5.17.5.2 – Distinctive cursor
*Summary (shall):* The cursor shall have different visual attributes that distinguish its various uses, such as selecting an object or editing text.

- **IMPL-5.17.5.2-01** `[CSS]` `[Main]` `cursor: text|pointer|crosshair|move` shall be set according to the mode (text editing, object selection, dragging) via dedicated state classes; touch equivalent via highlighting of the active target.

#### 5.17.5.3 – Distinctive cursor position
*Summary (shall):* The current cursor position on graphic/image-processing displays requiring precise positioning shall be indicated by a distinctive symbol marking a point (e.g., a cross or crosshair whose intersection marks a position with reasonable precision).

- **IMPL-5.17.5.3-01** `[CSS][JS]` `[Main]` In graphic/map views requiring precise positioning, an SVG cross/crosshair cursor synchronized to the pointer position shall be rendered, managed by the graphic component in `#content-region`.

#### 5.17.5.4 – Obscuration
*Summary (shall):* The cursor shall not obscure displayed entities.

- **IMPL-5.17.5.4-01** `[CSS]` `[Main]` The graphic cursor/crosshair shall have an offset or transparency such that it does not cover the underlying point/element it indicates (e.g., empty center of the crosshair).

#### 5.17.5.6 – Cursor movement using arrow keys
*Summary (shall):* If the cursor is moved by pressing a key, releasing the key shall cause the movement to stop.

- **IMPL-5.17.5.6-01** `[JS]` `[Main]` The `keydown`/`keyup` handler for graphic cursor movement shall stop the movement on `keyup`, without continuing past the key release.

#### 5.17.5.7 – Precise positioning
*Summary (shall):* Where data entry requires exact placement of graphic elements, users shall be given the ability to expand the critical display area (e.g., zoom and pan) to make positioning easier and more precise.

- **IMPL-5.17.5.7-01** `[JS][HTML]` `[Main]` Graphic components with precise positioning shall offer zoom/pan controls (touch-friendly buttons ≥ `--touch-target-min` and pinch-to-zoom gestures) via a dedicated module.

#### 5.17.5.8 – Home position
*Summary (shall):* The cursor's home position shall be consistent across similar types of displays.

- **IMPL-5.17.5.8-01** `[JS]` `[Global]` The graphic editing component shall define a consistent cursor home position (e.g., center or origin) in `config.js`, applied uniformly across all views of the same type.

#### 5.17.5.9 – Explicit actuation
*Summary (shall):* An explicit action separate from cursor position (e.g., pressing a button or toggling) shall be required for a control selection.

- **IMPL-5.17.5.9-01** `[JS]` `[Main]` Selecting a control shall require a `pointerup`/`click` event distinct from mere cursor positioning (`pointermove`/hover).

#### 5.17.5.10 – Incremental cursor positioning
*Summary (shall):* Where cursor positioning is incremented in discrete steps, the step size shall be consistent horizontally (left and right) and vertically (up and down).

- **IMPL-5.17.5.10-01** `[JS]` `[Main]` The graphic cursor movement step via keyboard/buttons shall be a single value configured in `config.js`, identical in all four directions.

#### 5.17.5.11.1 – Position designation
*Summary (shall):* When position designation is required in a task emphasizing keyboard data entry, cursor control shall be available from the keyboard, preferably with the arrow keys.

- **IMPL-5.17.5.11.1-01** `[JS]` `[Main]` For keyboard-oriented cursor positioning tasks, the arrow keys shall be the primary movement method, handled by a dedicated `keydown` listener in addition to the pointer.

#### 5.17.5.11.2 – Means of control
*Summary (should):* If keys other than arrow keys are used for movement (as with an abbreviated keyboard), the display should clearly indicate the means of control.

- **IMPL-5.17.5.11.2-01** `[HTML][CSS]` `[Main]` If alternative keys to the arrows are used for cursor movement, the interface shall show a visible indication (legend/tooltip) of the active keys, especially on a reduced touch keyboard.

#### 5.17.5.12.1 – User expectations
*Summary (shall):* The response of a cursor to user input shall be consistent and compatible with the user's expectations.

- **IMPL-5.17.5.12.1-01** `[JS]` `[Global]` Cursor behavior (speed, acceleration, direction) shall be implemented uniformly by a single shared module, with no undocumented variations between views.

#### 5.17.5.12.2 – Move in corresponding direction
*Summary (shall):* For cursor control by key action, the cursor shall move in the direction corresponding to the arrow key pressed (left→left, right→right, up→up, down→down).

- **IMPL-5.17.5.12.2-01** `[JS]` `[Main]` The arrow key handler shall map `ArrowLeft/Right/Up/Down` exactly to the corresponding movement direction of the graphic cursor, with no inversions.

#### 5.17.5.13 – Explicit delete action
*Summary (shall):* Data deletion or cancellation shall require an explicit action, such as pressing a DELETE key.

- **IMPL-5.17.5.13-01** `[JS][HTML]` `[Main]` Deleting a value/element shall require an explicit activation (Delete key/dedicated "Delete" button), never a side effect of another action.

#### 5.17.5.14 – Permanent deletion
*Summary (shall):* Permanent deletion (in the absence of an "undo" function) of more than one character shall not be allowed without an affirmative response to a validation query.

- **IMPL-5.17.5.14-01** `[JS][HTML]` `[Main]` When `undo` is not available and the deletion involves more than one character/element, `dialog.js` shall request explicit confirmation before performing the deletion, see `IMPL-5.1.3.11.5.3-01`.

#### 5.17.6.1 – Use
*Summary (may):* Form-filling interactive control may be used where some flexibility in data entry is needed.

- **IMPL-5.17.6.1-01** `[HTML]` `[Main]` Tasks with structured data and relative order flexibility shall use a `<form>` in `#content-region` as the interaction pattern, as an alternative to the question-and-answer wizard (see `IMPL-5.17.2.2.1-01`).

#### 5.17.6.2 – Grouping
*Summary (shall):* Displayed forms shall be organized to group related items together.

- **IMPL-5.17.6.2-01** `[HTML]` `[Main]` Related form elements shall be grouped into `<fieldset>` with a descriptive `<legend>`, not listed as an undifferentiated flat list.

#### 5.17.6.3 – Format and content consistency
*Summary (shall):* The format and content of displayed forms shall duplicate in every major parameter the paper form they are intended to represent.

- **IMPL-5.17.6.3-01** `[HTML][CSS]` `[Main]` When the digital form represents a reference paper form, the order, grouping, and labels of the fields shall mirror its structure, documented in the form's configuration.

#### 5.17.6.4 – Advancing through form fields
*Summary (shall):* Using TAB or ENTER to advance through fields, the system shall require a response for every data entry field; advancing past a field left blank when no entry is desired shall require an explicit action (TAB or ENTER keystrokes).

- **IMPL-5.17.6.4-01** `[JS]` `[Main]` `validate.js` shall require explicit confirmation (Tab/Enter) to leave an optional field blank; no silent automatic advancement that skips unfilled fields.

#### 5.17.6.5 – Separation
*Summary (shall):* Fields or groups of fields shall be separated by spaces, lines, or other delineation cues.

- **IMPL-5.17.6.5-01** `[CSS]` `[Main]` `<fieldset>`/field groups shall have minimum visual separation (`gap`/`border-block` ≥ `--touch-gap-min`) between them.

#### 5.17.6.6 – Distinctiveness of fields
*Summary (shall):* Required fields shall be distinguished from optional fields.

- **IMPL-5.17.6.6-01** `[HTML][CSS]` `[Main]` Required fields shall have `required`/`aria-required="true"` and a consistent visual indicator (e.g., asterisk, `.field-required` class) distinct from optional fields.

#### 5.17.6.7 – Field labels
*Summary (shall):* Field labels shall be distinctively presented so they can be distinguished from the data entry space.

- **IMPL-5.17.6.7-01** `[HTML][CSS]` `[Main]` `<label>` elements shall have distinct typographic style/color (e.g., `font-weight`, `color: var(--color-fg)`) relative to the associated entry control.

#### 5.17.6.8 – Additional cueing
*Summary (shall):* Labels for data entry fields shall incorporate additional cueing when the data format could logically take more than one form (e.g., "DATE (MM/DD/YYYY): _ _ / _ _ / _ _ _ _").

- **IMPL-5.17.6.8-01** `[HTML]` `[Main]` `<label>` elements for fields with ambiguous format shall include the expected pattern in parentheses (e.g., "Date (DD/MM/YYYY)") and/or a corresponding `placeholder`, verified by `validate.js`.

#### 5.17.6.9 – Informative labels
*Summary (shall):* Descriptive wording shall be employed when labeling data fields.

- **IMPL-5.17.6.9-01** `[HTML]` `[Main]` Field labels shall use complete descriptive words managed by `i18n.js`, avoiding unexplained acronyms.

#### 5.17.6.10 – Arbitrary codes
*Summary (shall):* Use of arbitrary codes shall be avoided.

- **IMPL-5.17.6.10-01** `[JS]` `[Main]` Selectable values in forms (e.g., `<select>`) shall use readable text labels as visible text, with any internal codes only as an unexposed `value`, never arbitrary codes shown to the user.

#### 5.17.6.11 – Top-aligned labels
*Summary (shall):* Top-aligned labels shall be used when labels are long or the form requires horizontal space to group related entry fields.

- **IMPL-5.17.6.11-01** `[CSS]` `[Main]` The form layout class `.form-row--stacked` (label above the field, `flex-direction: column`) shall be used for long labels or groups requiring horizontal space.

#### 5.17.6.12 – Vertical space limited
*Summary (shall):* Top-aligned labels shall not be used if vertical space is limited.

- **IMPL-5.17.6.12-01** `[CSS]` `[Main]` At breakpoint S (800×600) with a reduced vertical viewport, the form layout shall automatically switch from `.form-row--stacked` to `.form-row--inline` (label left/right) via a media query, avoiding top alignment.

#### 5.17.6.13 – Right-aligned labels
*Summary (shall/may):* Right-aligned labels shall be used for forms requiring quick scanning of labels and may be used when vertical space is limited.

- **IMPL-5.17.6.13-01** `[CSS]` `[Main]` The `.form-row--label-right` class (`text-align: right` on the label, same row as the field) shall be available for forms requiring quick scanning or with limited vertical space.

#### 5.17.6.14 – Left-aligned labels
*Summary (shall):* Left-aligned labels shall be used when a form requires users to carefully consider the labels.

- **IMPL-5.17.6.14-01** `[CSS]` `[Main]` The `.form-row--label-left` class (default, `text-align: left`) shall be used for forms requiring careful reading of labels (e.g., legal/confirmation forms).

#### 5.17.6.15 – Limited vertical space (left-aligned labels)
*Summary (shall):* Left-aligned labels shall not be used when they are so long that they do not allow data entry in the same vertical space.

- **IMPL-5.17.6.15-01** `[CSS]` `[JS]` `[Main]` When the label length (measured or configured) exceeds the threshold in `config.js`, the layout shall automatically switch to `.form-row--stacked` instead of `.form-row--label-left`.

#### 5.17.6.16 – Cursor default position
*Summary (shall):* When a form is displayed, a displayed cursor shall be positioned by default on the first data entry field.

- **IMPL-5.17.6.16-01** `[JS]` `[Main]` When the form is displayed, `nav.js`/the form component shall call `focus()` on the first editable field, setting the initial focus without requiring the user to tap.

#### 5.17.6.16.1 – Cursor advancement
*Summary (shall):* The cursor shall advance to the next data entry field via the TAB or ENTER key when the user has completed entry of the current field.

- **IMPL-5.17.6.16.1-01** `[JS]` `[Main]` The form's `keydown` handler shall intercept `Tab`/`Enter` to move focus to the next field in DOM/`tabindex` order, except for the exceptions in `IMPL-5.17.6.16.2-01`.

#### 5.17.6.16.2 – Exceptions
*Summary (shall):* Exceptions to using ENTER for advancement occur when some fields require or allow multiple input lines; in that case, only TAB shall be used to advance the cursor.

- **IMPL-5.17.6.16.2-01** `[JS]` `[Main]` For multi-line `<textarea>` fields, the `keydown` handler shall not intercept `Enter` for advancement (it remains a line break); only `Tab` shall move focus to the next field.

#### 5.17.6.17 – Entry length indication
*Summary (shall):* The maximum acceptable length for variable-length fields shall be displayed.

- **IMPL-5.17.6.17-01** `[HTML][JS]` `[Main]` Variable-length fields shall show an `n/max` counter (live-updated by `validate.js`, `aria-live="polite"`) next to the field, based on `maxlength`.

#### 5.17.6.18 – Maximum characters inputted
*Summary (shall):* An indication shall be provided to the user if the maximum number of characters has been entered.

- **IMPL-5.17.6.18-01** `[JS][CSS]` `[Main]` When `maxlength` is reached, the field shall be highlighted (`.field-at-limit` class) and the counter from `IMPL-5.17.6.17-01` shall flag it visually (e.g., `--color-warning` color).

#### 5.17.6.19 – Overwriting
*Summary (shall):* Characters other than those indicating the format, which shall be overwritten, shall not be entered by default into unfilled data fields.

- **IMPL-5.17.6.19-01** `[HTML][JS]` `[Main]` Fields with a format mask (e.g., date) shall show only format placeholder characters (`placeholder`/mask in `validate.js`), without pre-filling default values that were not requested.

#### 5.17.6.20 – Dimensional units
*Summary (shall):* When a consistent dimensional unit is intended as the entry unit for a given field, the unit shall be provided by the computer to the right of the field.

- **IMPL-5.17.6.20-01** `[HTML][CSS]` `[Main]` Numeric fields with a fixed unit shall show the unit as static text to the right of the field (e.g., `<span class="field-unit">mm</span>`), not editable by the user.

#### 5.17.6.21 – Variable dimensional units
*Summary (shall):* When the dimensional unit may vary for a given field, the user shall be informed and a separate field with options shall be provided to the right of the numeric field for selecting the unit from a drop-down list.

- **IMPL-5.17.6.21-01** `[HTML]` `[Main]` Numeric fields with variable units shall have a `<select class="field-unit-select">` with the available units placed next to, to the right of, the numeric field.

#### 5.17.6.22 – User omissions
*Summary (shall):* The user shall be informed when required data entries have not been made.

- **IMPL-5.17.6.22-01** `[JS][HTML]` `[Main]` On submit, `validate.js` shall list in `#message-line`/next to the fields all unfilled required fields, with `aria-invalid="true"` and focus on the first missing one.

#### 5.17.6.23 – Non-entry areas
*Summary (shall):* Non-entry (protected) areas of the display shall be displayed so as to be distinguishable from areas where data may be entered.

- **IMPL-5.17.6.23-01** `[CSS]` `[Main]` Protected/read-only areas shall use a distinct style (`.field-readonly`, neutral background, no editable-field border) compared to enterable fields, see `IMPL-5.1.3.11.1.13-01`.

#### 5.17.6.24 – Inaccessible by cursor
*Summary (shall):* Non-entry areas shall be inaccessible via the cursor.

- **IMPL-5.17.6.24-01** `[HTML]` `[Main]` Read-only elements shall have `readonly`/`disabled`/`tabindex="-1"` to be excluded from tab order and focus.

#### 5.17.6.25 – Flexible data entry
*Summary (shall):* When multiple data items are entered, the user shall be allowed to re-enter, change, or cancel any item before submitting.

- **IMPL-5.17.6.25-01** `[JS][HTML]` `[Main]` Before final submit, every form field shall remain freely editable/erasable; a "Discard changes" action shall be present for the single field or the entire form.

#### 5.17.6.26 – Logical order
*Summary (shall):* Where no source document is involved, forms shall be designed so that data items are ordered in a logical sequence of input.

- **IMPL-5.17.6.26-01** `[HTML]` `[Main]` In the absence of a reference source document, the order of fields in the DOM shall follow a logical, task-oriented sequence defined during form design and documented in the component.

#### 5.17.6.27 – Dialog boxes for control entry
*Summary (may):* Dialog boxes may be used as an aid for composing complex control entries or for selecting options (e.g., for a print request, a displayed form might help a user invoke the various format controls that are available).

- **IMPL-5.17.6.27-01** `[HTML][JS]` `[Main]` For complex configuration tasks (e.g., print/export options), `dialog.js` shall offer a dedicated `<dialog>` form that guides the user in composing the parameters, instead of a single inline entry.

### 3.7 Error management, user guidance, help and prompts

Prevention, detection, messaging and recovery from errors, together with help and prompting.

#### 5.1.2.1.7.8 – Diagnostic information
*Summary (shall/should/may):* Error messages shall explicitly provide as much diagnostic information and remedial direction as can be inferred reliably from the error condition.

- **IMPL-5.1.2.1.7.8-01** `[JS]` `[Main]` `notify.js`/`validate.js` shall generate error messages with a `detail` field containing the inferred cause and the suggested corrective action.
- **IMPL-5.1.2.1.7.8-02** `[HTML]` `[Main]` The error message shall be rendered with `role="alert"` associated with the field (`aria-describedby`) for the diagnostic content.

#### 5.1.2.1.7.11.2 – Computer-corrected commands
*Summary:* Computer-corrected commands, values, and spellings shall be displayed and highlighted for user confirmation.

- **IMPL-5.1.2.1.7.11.2-01** `[HTML][JS]` `[Main]` When `validate.js` applies an automatic correction, the corrected value shall be shown highlighted (`.field--autocorrected`) in a confirmation dialog (`dialog.js`) prior to final acceptance.

#### 5.17.10.1 – Error correction
*Summary:* An easy means for correcting erroneous user entries shall be provided.

- **IMPL-5.17.10.1-01** `[HTML][JS]` `[Main]` Every field in error shall expose direct inline correction (automatic focus + edit) without requiring the entire form to be cleared.

#### 5.17.10.2 – Partial correction
*Summary:* The system shall permit partial correction of entries.

- **IMPL-5.17.10.2-01** `[JS]` `[Main]` `validate.js` shall allow editing of the single erroneous field/character without invalidating or requiring re-entry of other already-correct fields of the form.

#### 5.17.10.3 – Early detection
*Summary:* A capability to detect and correct errors after keying in but before entering into the system shall be provided.

- **IMPL-5.17.10.3-01** `[JS]` `[Main]` `validate.js` shall validate on `input`/`blur` before submit and show the error immediately inline, allowing correction before final confirmation.

#### 5.17.10.4 – Logical data breaks
*Summary:* Error checking shall occur at logical data entry breaks (e.g., end of field) rather than per character, to minimize disruption.

- **IMPL-5.17.10.4-01** `[JS]` `[Main]` `validate.js` shall perform validation on `change`/`blur` (end of field), not on every `keydown`, except for character-format checks (e.g., digits only).

#### 5.17.10.5 – Internal software checks
*Summary:* Software shall provide checks of user entries for validity of item, sequence of entry, completeness, and range of values.

- **IMPL-5.17.10.5-01** `[JS]` `[Main]` `validate.js` shall implement configurable rules (`type`, `required`, `min`/`max`, `pattern`, `order`) per field, with specific messages for each type of violation.

#### 5.17.10.6 – Critical entries
*Summary:* The system shall require the user to acknowledge entries that have irreversible or potentially destructive consequences prior to their being implemented.

- **IMPL-5.17.10.6-01** `[JS][HTML]` `[Global]` See `IMPL-5.1.3.12.1.5-01`: `dialog.js` shall open an explicit confirmation (`role="alertdialog"`) for every submission marked `data-destructive="true"` before execution.

#### 5.17.10.7.1 – Describe error
*Summary:* Error messages shall describe the error simply and in application terms rather than system function terms (e.g., "too many characters" vs. "stack overflow").

- **IMPL-5.17.10.7.1-01** `[JS]` `[Main]` `i18n.js`/`notify.js` shall map internal error codes to textual messages in application language, avoiding internal technical terminology.

#### 5.17.10.7.2 – Instruct user
*Summary:* Error messages shall instruct the user how to recover from the error or escape from the error situation.

- **IMPL-5.17.10.7.2-01** `[JS][HTML]` `[Main]` Every message generated by `notify.js` shall include a suggested corrective action (text/link with automatic focus on the field to be corrected).

#### 5.17.10.7.3 – Constructive and neutral tone
*Summary:* Error messages shall be constructive and neutral in tone, avoiding phrases that suggest a judgment of the user's behavior.

- **IMPL-5.17.10.7.3-01** `[JS]` `[Global]` The error text dictionary in `i18n.js` is subject to a review criterion (I) that prohibits blaming or judgmental wording.

#### 5.17.10.7.4 – Consistent with user understanding
*Summary:* The error message shall be written in a manner consistent with the user's understanding and expected level of knowledge.

- **IMPL-5.17.10.7.4-01** `[JS]` `[Global]` `i18n.js` shall provide error-text variants by user level (`state.user.skillLevel`), with simplified wording for novice users.

#### 5.17.10.7.5 – Correction entry and confirmation
*Summary:* When the user enters a correction of an error, such correction shall be implemented by an explicit user action (e.g., the ENTER key).

- **IMPL-5.17.10.7.5-01** `[HTML][JS]` `[Main]` The correction form shall require explicit activation of a "Confirm"/submit control (`button[type=submit]` or Enter); no automatic application on value change alone.

#### 5.17.10.7.6 – Error correction acknowledgement
*Summary:* The system shall acknowledge all error corrections by the user, either by accepting the corrected entry or by providing a separate error message for an entry still in error.

- **IMPL-5.17.10.7.6-01** `[JS]` `[Main]` `validate.js` shall re-evaluate the field after every correction and call `feedback.js:acknowledge(el)` if valid, or update the error message if still invalid.

#### 5.17.10.7.7 – Errors in elements not normally visible
*Summary:* References to errors in elements not normally visible to users shall not be included, unless the user has explicitly requested detailed diagnostic information for programmer-level troubleshooting.

- **IMPL-5.17.10.7.7-01** `[JS]` `[Main]` `notify.js` shall show only application-level messages by default; technical details/stack traces appear only behind an explicit "Show technical details" action (`<details>`).

#### 5.17.10.8 – Error description
*Summary:* If a simple error message is presented, users shall be provided access to a more detailed description.

- **IMPL-5.17.10.8-01** `[HTML][JS]` `[Main]` Every message in `#message-line`/toast shall include a "Details" control that expands (`<details>`/dialog) the full description via `notify.js`.

#### 5.17.10.9 – Message timing
*Summary:* Error messages shall be provided within 0.2 second of the time in which an error is detected.

- **IMPL-5.17.10.9-01** `[JS]` `[Global]` `config.js` shall define `errorMessageMaxDelayMs = 200` (Table V); `validate.js`/`notify.js` display the message within that threshold, verifiable with test T.

#### 5.17.10.10 – Error recovery and process change (multi-level "undo")
*Summary:* The user shall be able to stop a control process at any point in a sequence and easily return to previous levels in multi-step processes, in order to nullify an error or effect a desired change.

- **IMPL-5.17.10.10-01** `[JS][HTML]` `[Footer]` `#function-keys` shall include a "Back/Undo step" command that calls `nav.js:undoStep()`/`state.js` to return to the previous step of the multi-step process at any time.

#### 5.17.10.11 – Spelling errors
*Summary:* Spelling and other common errors shall not produce valid system commands or initiate transactions.

- **IMPL-5.17.10.11-01** `[JS]` `[Main]` `nav.js:resolveCommand()` shall require an exact match or a registered unambiguous abbreviation (see `IMPL-5.1.3.12.5.5-01`); unrecognized input never maps by silent approximation.

#### 5.17.10.12 – Recognize common misspellings
*Summary:* The system shall recognize common misspellings of commands, inform users of unrecognized entries, provide a similar correct entry, and display the recommendation for revision and confirmation by the user.

- **IMPL-5.17.10.12-01** `[JS][HTML]` `[Main]` `nav.js` shall implement a distance comparison (e.g., Levenshtein) on unrecognized commands and propose the most similar command in a confirmation dialog (`dialog.js`) before execution.

#### 5.17.10.13 – Errors in stacked commands
*Summary:* To prompt for corrections of an error in stacked commands, the system shall display the sequence with the errors highlighted; where possible, a procedure shall be provided to correct the error and salvage the stack.

- **IMPL-5.17.10.13-01** `[HTML][CSS]` `[Main]` The "command queue" view (`nav.js:commandStack`, see `IMPL-5.1.3.12.2.2.5-01`) shall list the queued commands with those in error highlighted (`.cmd--error`).

#### 5.17.10.13.1 – Error correction procedure
*Summary:* When possible, a procedure shall be provided to correct the error and salvage the stack.

- **IMPL-5.17.10.13.1-01** `[JS]` `[Main]` `nav.js` shall expose a "Correct and keep queue" action that modifies only the erroneous command in the stack without removing the other queued commands.

#### 5.17.10.13.2 – Partial execution
*Summary:* If the error was not detected until after some of the commands in the stack have executed, the system shall inform the user which commands were successfully executed.

- **IMPL-5.17.10.13.2-01** `[JS][HTML]` `[Main]` `notify.js` shall show a summary of commands successfully executed before the error, distinct from those not executed/failed, in the same message/dialog.

#### 5.17.10.14 – Display of erroneous entries
*Summary:* An error message shall be displayed continuously until either the error is corrected or the user dismisses it.

- **IMPL-5.17.10.14-01** `[JS][HTML]` `[Main]` The error message shall not auto-hide via timeout; it remains visible until `validate.js` detects the correction or the user activates the close control (`aria-label="Close error"`).

#### 5.17.10.15 – Automatic error correction
*Summary:* Automatic correction should be provided if: a) the error has a unique solution; b) the correction is apparent to the user; c) the feature can be disabled at the user's request.

- **IMPL-5.17.10.15-01** `[JS]` `[Main]` `validate.js` shall apply auto-correction only for errors with a unique deterministic solution (e.g., superfluous spaces, capitalization), always shown highlighted for confirmation (see `IMPL-5.1.2.1.7.11.2-01`).
- **IMPL-5.17.10.15-02** `[JS]` `[Global]` `prefs.js` shall expose `autoCorrect: boolean`, disableable at the user's request, stored in `localStorage`.

#### 5.17.10.16 – Location of error messages
*Summary:* Error messages shall appear as near as possible to the user entry that caused the message, without obscuring control or display areas needed for the task.

- **IMPL-5.17.10.16-01** `[CSS][HTML]` `[Main]` The inline error message shall be positioned immediately below/beside the field (`.field-error`), without overlapping adjacent controls at the current breakpoint.

#### 5.17.10.17 – Obscure information
*Summary:* If an error message obscures task-relevant information, a means for moving the message shall be provided.

- **IMPL-5.17.10.17-01** `[JS][HTML]` `[Main]` Error messages in a non-modal overlay/dialog shall be draggable (Pointer Events) or repositionable with a dedicated control when they cover `#content-region`.

#### 5.17.10.18 – Multiple error messages
*Summary:* If a user repeats an entry error, the second message shall include a noticeable change, so that the user is certain the correction attempt has been processed.

- **IMPL-5.17.10.18-01** `[JS][CSS]` `[Main]` `notify.js` shall apply a noticeable variation (e.g., `.is-repeated`, additional text, brief animation) when the same error repeats on the same field.

#### 5.17.12 – Help
*Summary:* In addition to the requirements provided here, detailed guidance and best practices may be found in ANSI/HFES 200.3.

- **IMPL-5.17.12-01** `[JS]` `[Global]` Review criterion (I): the help module shall be internally documented with reference to the guidelines adopted by the project, without introducing external runtime dependencies.

#### 5.17.12.1 – Access
*Summary:* The system shall provide built-in guidance, allowing users to easily access built-in technical help.

- **IMPL-5.17.12.1-01** `[HTML][JS]` `[Header]` An always-visible `#btn-help` control in the header shall open built-in help (`dialog.js`/`#aux-region`) without requiring network-external resources.

#### 5.17.12.2 – Explicit error management aids
*Summary:* Help shall be available through explicit aids (labels, prompts, advisory messages) and implicit aids (cueing).

- **IMPL-5.17.12.2-01** `[HTML]` `[Main]` Every field shall have a label (`<label>`), a prompt (`placeholder`/help text), and advisory messages (`aria-describedby`) as explicit aids, in addition to implicit cueing (`required`, status icons).

#### 5.17.12.3 – Standard action to request help
*Summary:* A simple standard action shall always be available for the user to access help.

- **IMPL-5.17.12.3-01** `[JS][HTML]` `[Global]` `#btn-help` shall be present in a fixed position in the header in every view, with a standard keyboard shortcut (e.g., `F1`) handled by `nav.js`.

#### 5.17.12.4 – Built-in help
*Summary:* At a minimum, built-in help shall include definitions of allowable options, system capabilities, procedures, and ranges of values.

- **IMPL-5.17.12.4-01** `[JS][HTML]` `[Main]` The help content in `#aux-region`/dialog shall include, per view, "available options", "procedures" and "allowable ranges" sections, populated from `i18n.js`.

#### 5.17.12.5 – Context sensitive help
*Summary:* Context-sensitive help shall be specific to the context from which it was accessed; after initial access, the system shall allow the user to query the help documentation.

- **IMPL-5.17.12.5-01** `[JS]` `[Global]` `#btn-help` shall invoke `nav.js:openHelp(state.currentView)`, passing the id of the active view/field; the help window shall include a search field for subsequent queries.

#### 5.17.12.6 – Minimize cross-referencing
*Summary:* Individual built-in help displays should contain enough information to respond to the user's query, even at the cost of redundancy in the overall help system.

- **IMPL-5.17.12.6-01** `[JS]` `[Global]` Content-authoring criterion (I): every help entry in `i18n.js` shall be self-consistent, without requiring multiple cross-references to answer the primary question.

#### 5.17.12.7 – Definitions
*Summary:* The help section should contain a list of abbreviations and acronyms used in the main application.

- **IMPL-5.17.12.7-01** `[HTML][JS]` `[Main]` Help shall include a "Glossary/Acronyms" sub-view (`#help-glossary`) generated from a table of terms in `i18n.js`.

#### 5.17.12.8 – Multilevel help
*Summary:* If an initial help display provides only summary information, more detailed explanations shall be available via user query or drill-down.

- **IMPL-5.17.12.8-01** `[HTML][JS]` `[Main]` Help content shall be structured as an initial summary with expansion controls (`<details>`/"More information" link) that call `nav.js:openHelp(topic, level="detail")`.

#### 5.17.12.9 – Consistent help terminology
*Summary:* Online documentation, offline documentation, and help instructions shall use identical terminology to describe or define identical concepts or processes.

- **IMPL-5.17.12.9-01** `[JS]` `[Global]` All text (UI, error messages, help) shall draw on the same centralized terminology dictionary in `i18n.js`, without uncontrolled synonyms for the same concept.

#### 5.17.12.10 – System-initiated help
*Summary:* For systems expected to have novice or infrequent users, a system-initiated help function may be used; when provided, it shall have the characteristics of 5.17.12.10.1–.10.3.

- **IMPL-5.17.12.10-01** `[JS]` `[Global]` `prefs.js` shall expose `systemInitiatedHelp: boolean`, enabled for novice user profiles (`state.user.skillLevel === "novice"`), complying with the following subsections.

#### 5.17.12.10.1 – Specific
*Summary:* System-initiated help shall only provide help specific to the user's current task or context.

- **IMPL-5.17.12.10.1-01** `[JS]` `[Main]` The proactive suggestion (`notify.js:suggestHelp()`) shall use `state.currentView`/`state.focusedField` to show only content relevant to the active context, never generic help.

#### 5.17.12.10.2 – Non-intrusive
*Summary:* System-initiated help shall not interfere with or obscure any control or display area needed for the current task.

- **IMPL-5.17.12.10.2-01** `[CSS][HTML]` `[Main]` Proactive help suggestions shall appear as non-modal toasts in `#toast-region`/`#aux-region`, never as an overlay covering `#content-region`.

#### 5.17.12.10.3 – Easy to enable and disable
*Summary:* A relatively novice user shall be able to disable and enable the system-initiated help function with minimal effort.

- **IMPL-5.17.12.10.3-01** `[HTML][JS]` `[Main]` An accessible toggle control (e.g., in the preferences panel) shall enable/disable `prefs.js:systemInitiatedHelp` with a single interaction.

#### 5.17.13 – Prompts
*Summary:* Prompts shall be in accordance with the criteria of 5.17.13.1–.13.10.

- **IMPL-5.17.13-01** `[JS]` `[Global]` `notify.js` shall centralize prompt generation, applying the criteria of all following subsections in a single reusable component.

#### 5.17.13.1 – Use
*Summary:* Prompts and help instructions shall be used to explain commands, error messages, system capabilities, display formats, procedures and sequences, as well as to provide data.

- **IMPL-5.17.13.1-01** `[HTML][JS]` `[Main]` Inline prompts (`placeholder`, text above/below the field) shall cover command explanation, expected format, and procedure, generated from `i18n.js`.

#### 5.17.13.2 – Prompts for special modes
*Summary:* When operating in special modes, the system shall display the mode designation and the file(s) being processed.

- **IMPL-5.17.13.2-01** `[HTML][JS]` `[Header]` `#mode-indicator` in the header shall always show the current operating mode and, where relevant, the name of the file/resource being processed, updated by `state.js`.

#### 5.17.13.3 – User confirmation
*Summary:* Before processing user requests that would result in extensive or final changes to existing data, the system shall require user confirmation.

- **IMPL-5.17.13.3-01** `[JS][HTML]` `[Global]` See `IMPL-5.1.3.12.1.5-01`/`IMPL-5.17.10.6-01`: `dialog.js` shall require explicit confirmation for every action marked `data-extensive-change="true"`.

#### 5.17.13.4 – Confirm abort operations
*Summary:* When data entries or changes will be nullified by an abort action, the system shall require user confirmation.

- **IMPL-5.17.13.4-01** `[JS][HTML]` `[Main]` The "Cancel"/close control of a form with unsaved changes (`state.formDirty === true`) shall open a confirmation dialog (`dialog.js`) before discarding the data.

#### 5.17.13.5 – Prompt for missing data
*Summary:* When missing data are detected, the system shall prompt the user to supply the missing data.

- **IMPL-5.17.13.5-01** `[JS][HTML]` `[Main]` `validate.js` shall highlight (`aria-invalid="true"`, `.field--missing`) and generate an explicit prompt for every mandatory field missing at submission time.

#### 5.17.13.6 – Standard display
*Summary:* Prompting messages shall be displayed in a standardized area of the displays.

- **IMPL-5.17.13.6-01** `[CSS][HTML]` `[Footer]` All system prompts (not tied to a single field) shall appear in `#message-line`, a standard area consistent across every view.

#### 5.17.13.7 – Explicit prompts
*Summary:* Prompts and help instructions for system-controlled dialog shall be explicit.

- **IMPL-5.17.13.7-01** `[JS]` `[Global]` Prompt text in `i18n.js` shall explicitly state the required action (e.g., "Enter the date in DD/MM/YYYY format"), not generic indications.

#### 5.17.13.8 – Memorization
*Summary:* Prompts shall be presented so that the user does not need to memorize lengthy sequences or refer to secondary written procedures to respond.

- **IMPL-5.17.13.8-01** `[HTML]` `[Main]` The prompt shall report directly in the field (or beside it) all information needed to respond (format, example), without referring to external manuals.

#### 5.17.13.9 – Prompt clarity
*Summary:* Prompts shall be clear and understandable.

- **IMPL-5.17.13.9-01** `[JS]` `[Global]` Text review criterion (I): prompts in `i18n.js` shall be validated for clarity (plain language, short sentences) before release.

#### 5.17.13.10 – Coding schemes
*Summary:* Prompts shall not require reference to coding schemes or conventions that may be unfamiliar to occasional users.

- **IMPL-5.17.13.10-01** `[JS]` `[Main]` Prompts shall use natural language and decimal values (see `IMPL-5.1.3.12.1.13.1-01`), avoiding the need to know internal codes/enumerations to respond.

### 3.8 Notifications, messaging, alerts and audio

Requirements on notifications, messaging (chat), alarms/warnings and audio/voice signals, handled mainly by `notify.js` and `feedback.js` through `#alert-region`, `#alert-summary`, `#toast-region` and `#message-line` (see §2.2, §2.5).

#### 5.3.1.3 – Signal meaning
*Summary (shall):* Each audio signal shall have only one meaning (the ear effectively distinguishes periodic/complex tones in background noise).

- **IMPL-5.3.1.3-01** `[JS]` `[Global]` `feedback.js` defines in `config.js` a unique map `beep(type)` → meaning (e.g., `alarm`, `caution`, `confirm`, `error`, `message`), without reusing the same sound for different meanings.
- **IMPL-5.3.1.3-02** `[JS]` `[Global]` `notify.js` verifies, when queuing a message/alarm, that the category is associated with only one type of audio signal defined in the `config.js` table, preventing overlap.

#### 5.3.1.8 – Manual overrides
*Summary (shall):* Non-critical audio signals shall be capable of being turned off at the discretion of the user.

- **IMPL-5.3.1.8-01** `[HTML][JS]` `[Footer]` The preferences panel (reachable from `#user-menu`/`#btn-display-settings`) exposes `<input type="checkbox" id="pref-audio-noncritical">`, managed by `prefs.js`, to turn off non-critical audio signals in `feedback.js` (critical/alarm signals always remain active).
- **IMPL-5.3.1.8-02** `[JS]` `[Global]` The preference is saved in `localStorage` by `prefs.js` and read by `feedback.js.beep()` before emitting a non-critical sound.

#### 5.3.1.9 – Visual indication
*Summary (shall):* Where the user can turn off non-critical audio signals, a visual indication that the signal has been turned off shall be provided.

- **IMPL-5.3.1.9-01** `[HTML][CSS]` `[Header]` When non-critical audio is turned off, `#alert-summary` shows a persistent `.icon-audio-off` icon with the text "Audio off" (double icon+text coding).
- **IMPL-5.3.1.9-02** `[JS]` `[Global]` `state.js` exposes `state.audio.nonCriticalMuted`, updated by `prefs.js`; the icon in `#alert-summary` binds to this state via `CustomEvent`.

#### 5.3.2.1.5 – Persistence
*Summary (shall):* Audio warning signals shall persist until restoration of normal conditions or the warning is acknowledged.

- **IMPL-5.3.2.1.5-01** `[JS]` `[Global]` `feedback.js.beep('alarm')` repeats the signal at a configurable interval in `config.js` until `notify.js` receives the alarm's `acknowledge()` or the state returns to normal in `state.js`.
- **IMPL-5.3.2.1.5-02** `[HTML][JS]` `[Footer]` `#alert-summary` keeps the count of unacknowledged alarms, and the banner in `#alert-region` remains visible until the user presses the "Acknowledge" button.

#### 5.3.2.2.1 – Distinct from warning signals
*Summary (shall):* Caution signals shall be readily distinguishable from warning signals.

- **IMPL-5.3.2.2.1-01** `[JS]` `[Global]` `config.js` defines distinct sound patterns for `caution` and `warning` (different frequency/rhythm) used by `feedback.js.beep(type)`; see also IMPL-5.7.3.5.1-01/IMPL-5.7.3.5.2-01 for the corresponding visual coding.

#### 5.3.2.5 – Prioritization
*Summary (shall):* In case automatically initiated messages could be presented simultaneously, a priority system shall ensure that the most critical message overrides the initial presentation of lower-priority ones.

- **IMPL-5.3.2.5-01** `[JS]` `[Global]` `notify.js` implements a queue with numeric priority (`warning` > `caution` > `advisory` > `info`) defined in `config.js`.
- **IMPL-5.3.2.5-02** `[JS]` `[Global]` The functions `notify.enqueue(msg, priority)` and `notify.presentNext()` handle ordering and interrupt/postpone the current presentation when a higher-priority message arrives.

#### 5.3.2.6 – Top-priority messages
*Summary (shall):* After the top-priority message is presented, other messages shall follow priority order; no caution message shall be presented until all warnings are terminated.

- **IMPL-5.3.2.6-01** `[JS]` `[Global]` `notify.js` blocks the presentation of queued `caution` messages while unterminated/unacknowledged `warning` messages exist, maintaining the ordering of IMPL-5.3.2.5-01.

#### 5.3.2.7 – Relation to visual displays
*Summary (shall):* Audio alarm devices, if used together with visual displays, shall be supplementary and shall serve to direct the user's attention to the appropriate visual display.

- **IMPL-5.3.2.7-01** `[JS]` `[Global]` `feedback.js.beep(type)` is always invoked together with a visual update (highlighting in `#alert-region`/`#alert-summary`) via `notify.js`: no sound is emitted without a corresponding visual indication.

#### 5.3.3.2.1 – Manually initiated signals
*Summary (shall):* Manually initiated signals shall be manually terminated.

- **IMPL-5.3.3.2.1-01** `[HTML][JS]` `[Global]` Audio signals started by an explicit user action require an explicit stop control (`feedback.js.stop(id)` linked to a "Stop sound" button); they do not turn off automatically after a timeout.

#### 5.3.5.10.1 – Manual shutoff
*Summary (shall):* When a manual shutoff is provided, a visual indication that the warning signal has been turned off shall be provided.

- **IMPL-5.3.5.10.1-01** `[HTML][JS]` `[Header]` When the user uses the manual shutoff ("Silence" button in `#alert-region`), the persistent visual indication described in IMPL-5.3.1.9-01 appears until the audio is reactivated or the alarm resolved.

#### 5.3.5.10.4 – Redundant visual warning
*Summary (shall):* All nonverbal aural annunciations shall be accompanied by a visual annunciation that defines the condition.

- **IMPL-5.3.5.10.4-01** `[HTML][JS]` `[Footer]` Every nonverbal `feedback.js.beep('alarm'|'warning')` is accompanied by a banner in `#alert-region` with `role="alert"`, an icon and a semantic color defining the condition, managed by `notify.js`.

#### 5.3.5.11 – Volume control
*Summary (shall, with sub-criteria 5.3.5.11.1–.5):* Volume control movements shall prevent reducing the sound to an inaudible level or increasing it to an excessive level.

- **IMPL-5.3.5.11-01** `[HTML][CSS]` `[Footer]` The volume control is `<input type="range" id="pref-volume" min="20" max="100">` (design minimum/maximum values in `config.js`) in the preferences panel, with a touch target ≥ `--touch-target-min`; it does not allow setting 0% for critical signals.

#### 5.3.5.11.1 – Automatic or manual
*Summary (shall):* The volume of an alarm signal shall be controllable by the user, by an automatic mechanism, or by both, depending on the operating situation.

- **IMPL-5.3.5.11.1-01** `[JS]` `[Global]` `feedback.js` computes the effective volume by combining the manual value (`#pref-volume`, `prefs.js`) with an optional automatic factor derived from `state.js` (e.g., operating context), per rules defined in `config.js`.

#### 5.3.9.1 – Supportive function
*Summary (shall):* Audio signals used together with visual displays shall be supplementary to the visual signals.

- **IMPL-5.3.9.1-01** `[JS]` `[Global]` See IMPL-5.3.2.7-01: every audio signal is supplementary and paired with a visual display managed by `notify.js`.

#### 5.3.9.2 – Alert and direct user's attention
*Summary (shall):* Audio signals shall be used to alert and direct the user's attention to the appropriate visual display.

- **IMPL-5.3.9.2-01** `[JS][CSS]` `[Global]` `feedback.js.beep(type)` synchronously triggers highlighting (`.is-alerting` class, CSS animation) of the area of the display involved, to direct the user's attention.

#### 5.3.9.3 – Signal characteristics
*Summary (may):* Signals may be one-time or intermittent.

- **IMPL-5.3.9.3-01** `[JS]` `[Global]` `config.js` defines per signal type whether `mode: 'once'` or `mode: 'repeat'`; `feedback.js.beep(type)` respects the configured mode.

#### 5.3.9.4 – Intermittent signals
*Summary (shall):* Intermittent signals shall be automatically terminated when no longer applicable, or by user control.

- **IMPL-5.3.9.4-01** `[JS]` `[Global]` `feedback.js` automatically stops a `repeat` signal when `notify.js` detects that the condition is no longer valid, or when the user presses "Acknowledge"/"Silence".

#### 5.3.9.5 – Alarm settings
*Summary (shall):* When alarms are established on user-defined logic, the user shall be able to obtain status information on current settings (variables and critical values).

- **IMPL-5.3.9.5-01** `[HTML][JS]` `[Main]` An "Alarm settings" view (`#content-region`, reached via `nav.js`) shows, read-only, the thresholds/variables configured in `prefs.js`/`config.js`.

#### 5.3.9.6 – Alarm status information
*Summary (shall):* Alarm status information shall be available in monitoring situations with a change of responsibility (e.g., shift change).

- **IMPL-5.3.9.6-01** `[JS][HTML]` `[Main]` The view of IMPL-5.3.9.5-01 is accessible to any user with an enabled role (`auth.js`) and shows `state.alarms` updated in real time, useful for handover between operators.

#### 5.3.10.5 – Message priority control
*Summary (shall):* Where simultaneous voice messages could occur, they shall be prioritized so that the most critical message receives transmission priority.

- **IMPL-5.3.10.5-01** `[JS]` `[Global]` See IMPL-5.3.2.5-01/-02: `notify.js` applies the same priority queue to voice messages (text-to-speech) as well.

#### 5.3.10.7 – Message cancel capability
*Summary (shall):* A manual cancellation capability shall be provided for all voice displays after the initial presentation.

- **IMPL-5.3.10.7-01** `[HTML][JS]` `[Footer]` Every voice message presented by `notify.js` exposes a "Cancel" button (in `#message-line`/toast) that calls `speechSynthesis.cancel()`/`feedback.js.stop()`.

#### 5.3.10.8 – Say again capability
*Summary (shall):* Message repetition shall be provided on the user's command.

- **IMPL-5.3.10.8-01** `[HTML][JS]` `[Footer]` A "Repeat" button next to `#message-line` calls `notify.repeatLast()` to regenerate the last voice message on the user's command.

#### 5.3.13.3 – Adapt to the user and environment
*Summary (shall):* Speech recognition systems shall adapt to the variability of acoustic environments, speaking styles, and user errors.

- **IMPL-5.3.13.3-01** `[JS]` `[Global]` If speech recognition is implemented (Web Speech API), `config.js` allows sensitivity/noise thresholds to be adjusted; `validate.js` handles recurring errors, without requiring manual recalibration for each user.

#### 5.3.13.4 – Minimal training
*Summary (shall):* Speech recognition systems shall require minimal training.

- **IMPL-5.3.13.4-01** `[HTML][JS]` `[Main]` The voice command, if present, uses a minimal vocabulary documented in contextual help (`#btn-help`), without dedicated training phases.

#### 5.3.13.5 – Feedback
*Summary (shall):* Speech recognition systems shall provide feedback confirming to the user that they have been understood.

- **IMPL-5.3.13.5-01** `[JS][HTML]` `[Footer]` Every recognized voice command produces immediate visual feedback in `#status-line` (recognized text) within the Table V time limits (≤ 0.2 s), via `feedback.js.setStatus()`.

#### 5.3.13.6 – Input vocabulary
*Summary (shall):* Input vocabulary shall be minimized and phonetically distinct to eliminate misinterpretation.

- **IMPL-5.3.13.6-01** `[JS]` `[Global]` The set of recognized voice commands is defined in `config.js` with phonetically distinct terms, to minimize misinterpretation.

#### 5.3.13.7 – Interword delays
*Summary (shall):* Speech recognition shall not require interword delays nor exaggerated pronunciation.

- **IMPL-5.3.13.7-01** `[JS]` `[Global]` The speech recognition module (Web Speech API, `continuous: true`) does not impose artificial pauses between words nor emphasized pronunciation, per the `config.js` configuration.

#### 5.3.13.8 – Prompting
*Summary (shall):* Voice prompting from the computer shall be provided when it is advantageous to free the user from reading a display.

- **IMPL-5.3.13.8-01** `[JS]` `[Global]` When a function requires visual attention elsewhere, `notify.js` may generate a voice prompt (`SpeechSynthesisUtterance`) in addition to the text message.

#### 5.3.13.9 – Lack of user response
*Summary (shall):* Lack of user response to the prompt shall result in repetition of the prompt.

- **IMPL-5.3.13.9-01** `[JS]` `[Global]` `notify.js` repeats the voice/text prompt at a configurable interval in `config.js` if it does not receive a response (input or acknowledge) within the established timeout.

#### 5.3.13.10 – Correction capability
*Summary (shall):* A capability shall be provided to reject unintended sounds (sneezes, coughs, non-command words).

- **IMPL-5.3.13.10-01** `[JS]` `[Global]` Speech recognition applies a minimum confidence threshold (in `config.js`) and discards brief/inconsistent audio events before interpreting them as a command.

#### 5.3.14.1 – Speech recognition devices
*Summary (shall):* Speech recognition devices shall not be the sole control device.

- **IMPL-5.3.14.1-01** `[HTML][JS]` `[Global]` Every function activatable via voice command has an equivalent activatable via touch/keyboard on the same elements (`button`, `input`); `nav.js` ensures functional parity.

#### 5.3.14.2 – Alternative control device
*Summary (shall):* An alternative control device shall be provided in case of speech recognition degradation or failure.

- **IMPL-5.3.14.2-01** `[JS]` `[Global]` If speech recognition fails/is unavailable, `state.js` sets `state.voice.available=false` and the touch/keyboard interface always remains fully operational without requiring confirmation.

#### 5.7.1.2 – Effective alerting system
*Summary (should):* An effective alerting system should: a. not generate alerts when everything is normal; b. reduce assimilation/memory load; c. minimize fault detection/assessment time; d. provide visual alerting that is understandable in every ambient condition; e. be standardized; f. allow system growth without new components.

- **IMPL-5.7.1.2-01** `[JS]` `[Global]` (a) `notify.js` generates no audio or visual alarms/alerts when `state.system.status==='normal'` for all monitored subparts: the absence of alarms is the silent default state.
- **IMPL-5.7.1.2-02** `[JS][HTML]` `[Footer]` (b,c) `#alert-summary` and `#alert-region` aggregate and summarize error conditions (count, priority) to reduce memorization load and fault detection/assessment time.
- **IMPL-5.7.1.2-03** `[CSS]` `[Global]` (d) Alarm states use high-contrast colors (`--color-critical`, `--color-alarm`) and double coding (icon+text), legible with the `theme-light/dark/high-contrast` themes under every ambient brightness condition simulated by user preferences.
- **IMPL-5.7.1.2-04** `[CSS][JS]` `[Global]` (e) Alarm/caution/advisory styles and icons are standardized in `config.js` and in the CSS tokens (`--color-*`), reused identically across all views.
- **IMPL-5.7.1.2-05** `[JS]` `[Global]` (f) `notify.js` treats alarm categories as configurable data (array in `config.js`), so new alarm types can be added without modifying existing markup or components.

#### 5.7.1.7 – Signal precedence
*Summary (shall):* Each of the following signals takes precedence over those below it (warning > caution > advisory).

- **IMPL-5.7.1.7-01** `[JS]` `[Global]` `notify.js` defines the priority order `warning > caution > advisory` in `config.js` (see IMPL-5.3.2.5-01), applied both to the message queue and to the display in `#alert-region`/`#alert-summary`.

#### 5.7.1.7.1 – Warning (alarm)
*Summary (def.):* A signal that alerts the user to a dangerous condition requiring immediate action.

- **IMPL-5.7.1.7.1-01** `[CSS][HTML]` `[Global]` The `warning` category uses `--color-critical`, a dedicated icon and the text "Immediate action required" (i18n.js); see IMPL-5.7.3.5.1-01 for the flashing coding.

#### 5.7.1.7.2 – Caution (alert)
*Summary (def.):* A signal that alerts the user to an impending dangerous condition requiring attention but not necessarily immediate action.

- **IMPL-5.7.1.7.2-01** `[CSS][HTML]` `[Global]` The `caution` category uses `--color-warning` (yellow) and the text "Requires attention"; see IMPL-5.7.3.5.2-01.

#### 5.7.1.7.3 – Advisory
*Summary (def.):* A signal indicating a safe or normal configuration/condition, or an important but not urgent change of status.

- **IMPL-5.7.1.7.3-01** `[CSS][HTML]` `[Global]` The `advisory` category uses `--color-advisory` (cyan/blue) for normal configurations or non-urgent status changes, shown in `#alert-region` with lower priority.

#### 5.7.2.1.2 – Warning content
*Summary (shall):* Warning labels/placards shall explicitly describe the hazard, how to avoid it, and the consequences of not avoiding it.

- **IMPL-5.7.2.1.2-01** `[HTML][JS]` `[Main]` On-screen warning messages (`dialog.js`, `role="alertdialog"`) explicitly describe the dangerous condition, the action to take, and the consequence of failing to act, in three distinct text fields of the template.

#### 5.7.2.2 – Readability
*Summary (shall):* Messages on warning labels/placards shall account for the literacy and reading level of the target population.

- **IMPL-5.7.2.2-01** `[CSS][JS]` `[Global]` Warning message texts use `--fs-body`/`--fs-title` (never below the project's minimum size) and an adjustable language level via `i18n.js` based on the user profile.

#### 5.7.2.2.1 – Target population
*Summary (shall):* Messages shall be written so that the target population can read, understand, and comply with them.

- **IMPL-5.7.2.2.1-01** `[JS]` `[Global]` `i18n.js` provides translations of warning messages in the languages configured for the system's user population (`config.js`, supported language list).

#### 5.7.2.2.2 – Sentence style
*Summary (shall):* Sentences shall be short, direct, and in active voice to increase readability.

- **IMPL-5.7.2.2.2-01** `[JS]` `[Global]` Warning/error message templates (`notify.js`, `validate.js`) are written in short, direct, active-voice sentences (an editorial review criterion verifiable by inspecting the texts in `i18n.js`).

#### 5.7.2.2.3 – Multi-lingual or non-reading populations
*Summary (consider):* For multi-lingual or non-reading populations, consider commonly understood icons, supplemented by written labeling in the relevant languages.

- **IMPL-5.7.2.2.3-01** `[HTML][CSS]` `[Global]` Every warning/caution message is accompanied by a standardized icon (SVG, `aria-hidden="true"`) in addition to the translated text (`i18n.js`), for multi-lingual users (double coding icon+text, see §2.4).

#### 5.7.2.5 – Signal word
*Summary (shall):* Warning placards/labels shall display the appropriate signal word (DANGER, WARNING, CAUTION, NOTICE) in accordance with ANSI Z535.2/Z535.4.

- **IMPL-5.7.2.5-01** `[HTML][CSS]` `[Global]` Dialogs/banners display a standardized label (`DANGER`/`WARNING`/`CAUTION`/`NOTICE`, translated by `i18n.js`) with styling consistent with ANSI Z535 (color, position at the top of the panel), managed by `dialog.js`/`notify.js`.

#### 5.7.2.5.1 – DANGER
*Summary (shall):* DANGER shall be used when failure to avoid the hazard will almost certainly result in serious injury or death; reserved for the most serious hazards.

- **IMPL-5.7.2.5.1-01** `[CSS][JS]` `[Global]` The "DANGER" label is mapped in `config.js` to the highest priority (`warning`, `danger` level) with the most conspicuous `--color-critical` styling.

#### 5.7.2.5.2 – WARNING
*Summary (shall):* WARNING shall be used when failure to avoid the hazard may result in serious injury or death.

- **IMPL-5.7.2.5.2-01** `[CSS][JS]` `[Global]` The "WARNING" label is used by `notify.js` for conditions with risk of serious injury/death, with `--color-critical` styling and `warning` priority in `config.js`.

#### 5.7.2.5.3 – CAUTION
*Summary (shall):* CAUTION shall be used when failure to avoid the hazard may result in minor or moderate injury.

- **IMPL-5.7.2.5.3-01** `[CSS][JS]` `[Global]` The "CAUTION" label is used for conditions with risk of minor/moderate injury, mapped to `caution` priority and `--color-warning` (yellow) styling in `config.js`.

#### 5.7.2.5.4 – NOTICE
*Summary (should):* NOTICE should be used for important information not directly associated with a hazard, or for risk of damage to the system.

- **IMPL-5.7.2.5.4-01** `[CSS][JS]` `[Global]` The "NOTICE" label is used for important information not tied to a hazard (e.g., risk of system damage), mapped to `advisory`/`info` priority and `--color-advisory` styling in `config.js`.

#### 5.7.3.1 – General
*Summary (shall):* Visual displays shall provide the user with a clear indication of warning for off-normal equipment/system conditions.

- **IMPL-5.7.3.1-01** `[HTML][CSS]` `[Footer]` `#alert-region` and `#alert-summary` always provide a clear visual indication (color, icon, text) of every off-normal condition, managed by `notify.js`/`state.js`.

#### 5.7.3.2 – Warning and caution
*Summary (should):* A warning/caution display should indicate the triggering condition, to increase the probability of detection compared to observation alone.

- **IMPL-5.7.3.2-01** `[HTML][JS]` `[Main]` The banner in `#alert-region` explicitly indicates the triggering condition (variable/threshold exceeded) read from `state.js`.

#### 5.7.3.2.1 – Use
*Summary (shall):* Visual danger signals shall alert the user that a specific condition exists, providing information on the nature and priority of the condition.

- **IMPL-5.7.3.2.1-01** `[HTML][JS]` `[Footer]` Visual warning/caution signals in `#alert-region` include the nature (description) and priority (label/color) of the condition, generated by `notify.js` for each queued event.

#### 5.7.3.2.2 – Characteristics
*Summary (shall):* Danger signal displays shall be illuminated to be visible under all anticipated lighting conditions.

- **IMPL-5.7.3.2.2-01** `[CSS]` `[Global]` The `theme-light/dark/high-contrast` themes (`prefs.js`, `data-theme`) guarantee a contrast ratio ≥ 6:1 (target ≥ 10:1, §2.4) for danger signals under every ambient brightness condition simulated by the user.

#### 5.7.3.2.2.1 – Different from general lighting
*Summary (shall):* Danger signal displays shall be visually distinct from the area's general lighting.

- **IMPL-5.7.3.2.2.1-01** `[CSS]` `[Global]` Alarm styles (`--color-critical`, flashing animation) are distinct from the UI's neutral styles (`--color-bg`, `--color-fg`) used in the rest of the interface.

#### 5.7.3.2.2.2 – Specific meaning
*Summary (shall):* Danger signal displays shall have specific meaning within the operational area in which they are used.

- **IMPL-5.7.3.2.2.2-01** `[JS]` `[Global]` `config.js` uniquely associates each alarm color/icon with a single operational meaning throughout the application, consistent with IMPL-5.3.1.3-01.

#### 5.7.3.4 – Signal integration
*Summary (shall):* Visual warnings, cautions, and advisories shall be integrated with those presented in other sensory modalities (auditory, tactile).

- **IMPL-5.7.3.4-01** `[JS]` `[Global]` `feedback.js` synchronizes the visual signal (`.is-alerting`), audio (`beep(type)`) and any haptic feedback (`navigator.vibrate`) for every warning/caution/advisory event in a single call to `notify.js.raise()`.

#### 5.7.3.5 – Priority coding
*Summary (shall):* Discriminatory characteristics (flashing, color, shape, symbols, contrast, size, location) shall be used to establish the priority of visual signals.

- **IMPL-5.7.3.5-01** `[CSS]` `[Global]` Each priority level combines at least two discriminatory characteristics among flashing, color, shape/icon, size and location (`--color-*` tokens, `.badge-warning/.badge-caution/.badge-advisory` classes), defined in `config.js`.

#### 5.7.3.5.1 – Warning signals
*Summary (shall):* Visual warning signals shall flash red at 3–5 Hz with a 50% duty cycle.

- **IMPL-5.7.3.5.1-01** `[CSS]` `[Global]` Warning signals use `--color-critical` with `@keyframes blink-warning` at a 3–5 Hz frequency and 50% duty cycle (exact duration in the `--blink-warning-ms` custom property in `config.js`/CSS).

#### 5.7.3.5.1.1 – Flashing text
*Summary (should):* If the warning text to be read flashes, it should alternate intensities while maintaining a minimum legible intensity.

- **IMPL-5.7.3.5.1.1-01** `[CSS]` `[Global]` The animation alternates opacity/color between 100% and a minimum (`--blink-min-opacity`, default 0.4) that keeps the text legible, never opacity 0.

#### 5.7.3.5.1.2 – Synchronized flash rate
*Summary (shall):* The flash rate for all warning signals shall be synchronized.

- **IMPL-5.7.3.5.1.2-01** `[CSS]` `[Global]` All instances of the `blink-warning` animation share `animation-delay: 0s` and the duration defined by the `--blink-warning-ms` custom property on `:root`.

#### 5.7.3.5.1.3 – Use with caution signals
*Summary (shall):* If used together with caution signals, warning signals shall be coded to be easily distinguished from caution signals.

- **IMPL-5.7.3.5.1.3-01** `[CSS]` `[Global]` The `blink-warning`/`--color-critical` animation/color are always distinct from `blink-caution`/`--color-warning` (different frequency and color) when they appear together in `#alert-region`.

#### 5.7.3.5.2 – Caution signals
*Summary (shall):* If cautions take the form of flashing text, the text shall flash at no more than 2 Hz with an ON/OFF interval of approximately 70% on, to ensure legibility.

- **IMPL-5.7.3.5.2-01** `[CSS]` `[Global]` The flashing caution text/element uses `@keyframes blink-caution` at a frequency ≤ 2 Hz with an ON interval ≈ 70% (`--blink-caution-ms`, `--blink-caution-on-ratio: 0.7` in `config.js`/CSS).

#### 5.7.3.5.2.1 – Color
*Summary (shall):* Visual caution signals shall be yellow.

- **IMPL-5.7.3.5.2.1-01** `[CSS]` `[Global]` All visual caution signals use exclusively the `--color-warning` (yellow) token as the base color.

#### 5.7.3.5.2.2 – Rapid identification
*Summary (shall):* At least two discriminatory characteristics shall be employed to ensure rapid identification and interpretation of caution signals.

- **IMPL-5.7.3.5.2.2-01** `[CSS][HTML]` `[Global]` Caution signals combine at least two discriminatory characteristics (`--color-warning` color + `.icon-caution` icon + flashing), consistent with IMPL-5.7.3.5-01.

#### 5.7.3.6 – Text height
*Summary (shall):* Text for warning/caution signals shall use characters between 8.7 and 17.4 milliradians (30–60 minutes of arc) measured from the longest anticipated viewing distance, with a larger size used under adverse conditions.

- **IMPL-5.7.3.6-01** `[CSS]` `[Global]` Warning/caution signal text uses `--fs-title` (larger than the standard UI size) with a guaranteed minimum via `clamp()`, calibrated in `config.js` to the anticipated reading distance (e.g., ≥ 1 m).

#### 5.7.3.7 – Co-location
*Summary (shall):* Warning and caution signals and the information needed to respond to them shall be grouped in a single location.

- **IMPL-5.7.3.7-01** `[HTML][CSS]` `[Footer]` All active warning/caution signals and their related response information are grouped in `#alert-region` (a single, always-visible area).

#### 5.7.3.7.1 – Grouped separately
*Summary (shall):* When textual information about warning conditions is listed in a single location, warnings and cautions shall be grouped separately.

- **IMPL-5.7.3.7.1-01** `[HTML][CSS]` `[Footer]` Within `#alert-region`, `notify.js` separates warning and caution messages into two distinct sub-lists (`.alert-list--warning`, `.alert-list--caution`).

#### 5.7.3.7.2 – Sorting options
*Summary (shall):* The user shall have the option to sort warning/caution messages by priority, chronological order, or recency.

- **IMPL-5.7.3.7.2-01** `[HTML][JS]` `[Footer]` A `<select id="alert-sort">` control in `#alert-region` allows sorting messages by priority, chronological order, or recency, via `notify.js.sortAlerts(mode)`.

#### 5.7.3.8 – Master warning, caution, and advisory lights
*Summary (shall):* Master warning/caution/advisory indicators shall be set apart from subsystem status indicators, except in maintenance-only displays.

- **IMPL-5.7.3.8-01** `[HTML][CSS]` `[Header]` `#alert-summary` acts as the master indicator (aggregated count/color) distinct from the status indicators of individual subsystems shown in `#content-region`, except in maintenance-only views.

#### 5.7.3.9 – Additional warnings
*Summary (shall):* Additional warnings shall be indicated by redundant coding.

- **IMPL-5.7.3.9-01** `[CSS][HTML]` `[Global]` Additional/concurrent warning conditions are signaled with redundant coding (color + icon + text + optional flashing) in `#alert-region`.

#### 5.7.3.10 – False and nuisance alerts
*Summary (should):* The system should minimize the effects of undetected, false, or nuisance alerts.

- **IMPL-5.7.3.10-01** `[JS]` `[Global]` `notify.js` applies hysteresis/debounce (threshold and minimum duration in `config.js`) before raising an alarm from a state change, to reduce false positives and nuisance alerts.

#### 5.17.7.1 – Information presentation using speech output
*Summary (should):* Where possible, speech outputs should be used in applications expected to be used together with another activity requiring focused visual attention or hands-free response.

- **IMPL-5.17.7.1-01** `[JS]` `[Global]` When the application anticipates simultaneous use of other visual activities or occupied hands, `notify.js` offers speech output as an alternative (`SpeechSynthesisUtterance`), enabled via a user preference in `prefs.js`.

#### 5.17.7.1.1 – Speech output design
*Summary (shall):* The design of speech displays shall conform to the criteria in 5.3.10.

- **IMPL-5.17.7.1.1-01** `[JS]` `[Global]` See IMPL-5.3.10.5-01/IMPL-5.3.10.7-01/IMPL-5.3.10.8-01: speech displays in `notify.js` follow the same rules for priority, cancellation, and repetition.

#### 5.17.7.1.3 – Use
*Summary (should):* Speech recognition should be used when: a. the consequences of errors are low; b. identifying/correcting errors is easy; c. use is expected to be infrequent; d. an alternative input method exists that allows speech recognition to be disabled.

- **IMPL-5.17.7.1.3-01** `[JS]` `[Global]` (a-d) `config.js` enables speech recognition (Web Speech API) only for low error-risk functions that are easily correctable (`validate.js`), infrequently used, and always with an alternative method (touch/keyboard, see IMPL-5.3.14.2-01).

#### 5.17.7.2.1 – Common method use
*Summary (shall):* Visual displays of communication (e.g., chat) shall be provided when: a. the message is complex/long or needs to be recalled later; b. it does not require immediate action; c. the receiver's auditory system is overloaded; d. the environment is too noisy; e. the receiver remains in front of the displays; f. multi-lingual joint communication is needed.

- **IMPL-5.17.7.2.1-01** `[HTML][JS]` `[Main]` (a-f) A chat panel integrated into `#aux-region` (managed by `notify.js`) is shown when the message is long/complex, does not require immediate action, the user is auditorily overloaded, the environment is noisy, the user remains in front of the displays, or multi-lingual communication is needed (`i18n.js`).

#### 5.17.7.2.2 – Multimodal communication
*Summary (shall):* Visual displays of communication shall not be the only means of real-time communication of critical information under high-workload situations.

- **IMPL-5.17.7.2.2-01** `[JS]` `[Global]` Under high-workload conditions (`state.workload==='high'`), `notify.js` does not use the text/chat channel alone for critical information, always pairing it with an audio/visual alarm signal (see IMPL-5.3.2.7-01).

#### 5.17.7.2.3 – Notifications
*Summary (should):* Audio notifications should be programmable and available for cases such as arrival of a new message, messages from specific individuals, or messages containing key words.

- **IMPL-5.17.7.2.3-01** `[HTML][JS]` `[Footer]` The preferences panel (`prefs.js`) allows configuring audio notifications for new messages, specific senders, and key words (dedicated `<input>` lists), applied by `notify.js`.

#### 5.17.7.2.4.1 – Saving text
*Summary (should):* Message text should be automatically saved for long-term storage, with a mechanism also for short-term saving during the session.

- **IMPL-5.17.7.2.4.1-01** `[JS]` `[Global]` `net.js` automatically sends message text to the back end for long-term storage; a cache in `localStorage`/session maintains short-term saving in case of disconnection.

#### 5.17.7.2.4.2 – Receipt after viewing
*Summary (should):* A confirmation that the message has been seen should be sent to the sender during real-time, two-way discussions.

- **IMPL-5.17.7.2.4.2-01** `[JS]` `[Main]` When a real-time message is displayed (e.g., `IntersectionObserver` on the open chat panel), `net.js` sends a read receipt to the sender via WebSocket.

#### 5.17.7.2.4.3 – Promoting typing efficiency
*Summary (should):* Features such as voice-to-text, auto-fill, hotkeys, and preformatted messages should increase chat efficiency.

- **IMPL-5.17.7.2.4.3-01** `[HTML][JS]` `[Main]` The chat composition field (`<textarea>`) offers voice-to-text (Web Speech API), autocomplete, keyboard shortcuts (`nav.js`), and a `<select>` of preformatted messages.

#### 5.17.7.2.4.4 – Text-to-speech
*Summary (should):* The message content should be able to be read via a text-to-speech system if the user is unable to view the window.

- **IMPL-5.17.7.2.4.4-01** `[HTML][JS]` `[Main]` A "Listen" button next to each chat message invokes `SpeechSynthesisUtterance` to read its content, enabled via `prefs.js`.

#### 5.17.7.2.4.5 – Chat integration
*Summary (should):* Chat windows should be integrated into the primary displays that require attention, to keep the user focused on the displays.

- **IMPL-5.17.7.2.4.5-01** `[HTML][CSS]` `[Main]` The chat window is a panel integrated into `#aux-region` (not a separate window), positioned next to the main `#content-region`.

#### 5.17.7.2.4.6 – Standardized lexicon
*Summary (should):* A standardized lexicon should be provided to ensure uniform understanding across different staff.

- **IMPL-5.17.7.2.4.6-01** `[JS]` `[Global]` `i18n.js` provides a standardized glossary (abbreviations, operational terms) shared by all messaging modules.

#### 5.17.7.2.5 – Audio displays
*Summary (may):* Audio displays may be used when: a. the visual display is limited by overload/mobility and it is useful to attract/alert the user; b. feedback is needed after control actions, data entry, or the end of cycles/sequences.

- **IMPL-5.17.7.2.5-01** `[JS]` `[Global]` (a,b) `feedback.js` uses audio signals when the visual display is overloaded/the user has limited mobility, or to give feedback after a control action, data entry, or end of a timer/sequence (`feedback.js.beep('confirm'|'timeout')`).

#### 5.17.7.2.6 – Supportive function
*Summary (shall):* Audio signals used with visual displays shall be supplementary to the visual signals.

- **IMPL-5.17.7.2.6-01** `[JS]` `[Global]` See IMPL-5.3.9.1-01/IMPL-5.3.2.7-01: the same supplementarity principle applied to messaging audio signals.

#### 5.17.7.2.7 – Direct user
*Summary (shall):* Audio signals shall direct the user to the appropriate visual display.

- **IMPL-5.17.7.2.7-01** `[JS][CSS]` `[Global]` See IMPL-5.3.9.2-01: audio signals direct attention to the corresponding visual display (toast, chat panel in `#aux-region`) via synchronized highlighting.

#### 5.17.7.2.8 – Signal characteristics
*Summary (may):* Signals may be one-time or intermittent.

- **IMPL-5.17.7.2.8-01** `[JS]` `[Global]` See IMPL-5.3.9.3-01: `config.js` defines `once`/`repeat` modes also for message notification signals.

#### 5.17.7.2.9 – Intermittent signals
*Summary (shall):* Intermittent signals shall terminate automatically or under user control.

- **IMPL-5.17.7.2.9-01** `[JS]` `[Global]` See IMPL-5.3.9.4-01: automatic termination or termination under user control of repeated message-notification signals.

#### 5.17.7.2.10 – Alarm settings
*Summary (shall):* Alarm settings shall conform to the criteria in 5.17.7.2.10.1 and .2.

- **IMPL-5.17.7.2.10-01** `[JS]` `[Global]` See IMPL-5.3.9.5-01/IMPL-5.3.9.6-01 and the following two sub-items: `notify.js` applies the same criteria also to messaging notifications.

#### 5.17.7.2.10.1 – Customized settings
*Summary (shall):* When alarm settings are user-customized, the user shall be able to view status information about current settings established as critical.

- **IMPL-5.17.7.2.10.1-01** `[HTML][JS]` `[Main]` The alarm settings view (IMPL-5.3.9.5-01) also includes the customized thresholds/key words for messaging notifications, read-only for the current user.

#### 5.17.7.2.10.2 – Alarm status
*Summary (shall):* Alarm status information shall be provided where responsibility may pass from one user to another (e.g., shift change).

- **IMPL-5.17.7.2.10.2-01** `[JS]` `[Global]` See IMPL-5.3.9.6-01: messaging alert/alarm status available in `state.alarms` for handover purposes.

#### 5.19.1.11.2 – Disabling audible alerts
*Summary (shall):* Users shall be able to disable or silence audible alerts/signals with respect to proximity to hostile environments.

- **IMPL-5.19.1.11.2-01** `[HTML][JS]` `[Footer]` A quick "Silent mode" control (`#function-keys`/`#btn-display-settings`) invokes `prefs.js` to disable/silence all non-critical audible alerts with a single tap.

#### 5.19.1.11.3 – Audible alert status
*Summary (should):* The device should provide an indication of alert status, such as a silent-mode icon.

- **IMPL-5.19.1.11.3-01** `[HTML][CSS]` `[Header]` When silent mode is active, a `.icon-silent` icon appears in `#alert-summary`/`#mode-indicator` (double coding icon+text "Silent"), updated by `state.js`.

#### 5.19.4.5 – Alerts and notifications
*Summary (shall):* Devices shall have the capability to set the alert type (e.g., audio, visual, tactile).

- **IMPL-5.19.4.5-01** `[HTML][JS]` `[Footer]` The preferences panel (`prefs.js`) allows setting the alarm type for each category (`<select>` with "audio", "visual", "haptic" options or combinations), applied by `feedback.js` (`navigator.vibrate` for haptic).

### 3.9 Windows, portals and page structure

Requirements specific to web portals and portlets (5.17.8) and to displays, titles, and frames, mapped onto the `#nav-region`, `#content-region`, `#aux-region` areas and the portlets hosted in `#content-region` (§2.2), with support from `nav.js` and `dialog.js`.

#### 5.12.3.4.8 – Data space
*Summary (shall):* Interfaces and navigation aids shall provide information enabling the user to know where they are in the data space.

- **IMPL-5.12.3.4.8-01** `[HTML][JS]` `[Main]` `nav.js` maintains and displays a breadcrumb (`<nav aria-label="breadcrumb">` in `#nav-region`) and a page indicator (see IMPL-5.17.15.5-01) so the user always knows where they are in the navigation space.

#### 5.17.8.1 – Information from diverse sources
*Summary (shall):* Web portals shall bring together information from diverse sources in a single location.

- **IMPL-5.17.8.1-01** `[HTML][CSS]` `[Main]` `#content-region` hosts multiple portlets (`<section class="portlet">`), each fed by a different data source via `net.js`, arranged in a grid (`display: grid`) on the same page.

#### 5.17.8.2 – Dedicated area
*Summary (shall):* Each information source shall have its own dedicated area on the page to display information in a portlet.

- **IMPL-5.17.8.2-01** `[HTML][CSS]` `[Main]` Each information source has its own `<section class="portlet" id="portlet-<name>">` with a distinct visual container (border, shadow, `--color-bg`) separated from other portlets in the `#content-region` grid.

#### 5.17.8.2.1 – Title
*Summary (should):* The title of a portlet should be a short, descriptive phrase that clearly indicates the portlet's subject (see figure 95 of the source).

- **IMPL-5.17.8.2.1-01** `[HTML]` `[Main]` Every portlet has an `<h2 class="portlet-title">` header with a short, descriptive phrase about its content, in the portlet's title bar.

#### 5.17.8.2.1.1 – Multiple screens
*Summary (should):* If the portlet presents multiple screens of content, the screen name should be included in the portlet title or appear as a heading in the content area.

- **IMPL-5.17.8.2.1.1-01** `[HTML][JS]` `[Main]` If the portlet manages multiple screens (internal state in `nav.js`), the current screen's name is included in the title or as an `<h3>` in the portlet's content area.

#### 5.17.8.2.1.2 – Screen name part of title
*Summary (should):* When the screen name is part of the title, the format should be "Portlet Name – Screen Name".

- **IMPL-5.17.8.2.1.2-01** `[JS]` `[Main]` When the screen name is part of the title, `nav.js` composes the text in the format "Portlet Name – Screen Name" in the `.portlet-title` element.

#### 5.17.8.2.1.3 – Screen name as part of portlet title
*Summary (should):* When the screen name is included in the portlet title, it should not be duplicated as a heading in the content area.

- **IMPL-5.17.8.2.1.3-01** `[HTML]` `[Main]` When the screen name is already in the portlet title (IMPL-5.17.8.2.1.2-01), the portlet's content area does not repeat the same text as an additional heading.

#### 5.17.8.2.2 – Title bar
*Summary (shall):* The portlet title bar shall include action buttons for: a. Detach (separate window); b. Edit (modify appearance/content); c. Help; d. Maximize; e. Open/close (toggle); f. Properties; g. Refresh; h. Remove; i. Restore down.

- **IMPL-5.17.8.2.2-01** `[HTML][CSS]` `[Main]` (a,d,e,i) The portlet title bar (`.portlet-header`) includes icon buttons ≥ `--touch-target-min` for Detach (`window.open()`), Maximize/Restore down (`.is-maximized` class at full `#content-region` width) and Open/close (`.is-collapsed` toggle, showing only the title bar).
- **IMPL-5.17.8.2.2-02** `[HTML][JS]` `[Main]` (b,c,f,g,h) The title bar also includes buttons for Edit (opens `dialog.js` with a configuration form), Help (contextual guidance via `nav.js`), Properties (`dialog.js` with version/author), Refresh (`net.js.refetch(portletId)`), and Remove (removal of the portlet's DOM node, with `dialog.js` confirmation).

#### 5.17.8.3 – Action buttons
*Summary (shall):* Portlet action buttons shall be presented in the same order in every portlet of a portal.

- **IMPL-5.17.8.3-01** `[CSS][HTML]` `[Main]` The order of buttons in `.portlet-header` (Detach, Edit, Help, Maximize/Restore, Open/close, Properties, Refresh, Remove) is fixed, defined in a common HTML/CSS template reused by every portlet.

#### 5.17.8.4 – Number of portlets
*Summary (should):* A page should not contain more than seven portlets.

- **IMPL-5.17.8.4-01** `[JS][CSS]` `[Main]` `state.js`/`nav.js` limits to 7 the number of portlets displayable at once in `#content-region` (value in `config.js`); beyond that number, additional portlets are grouped into secondary tabs/pages.

#### 5.17.8.5 – Vertical height
*Summary (should):* Portlets should fit within three or fewer screen heights vertically, to minimize scrolling.

- **IMPL-5.17.8.5-01** `[CSS]` `[Main]` The `#content-region` grid layout sizes portlets (`grid-auto-rows`, `max-block-size`) so the whole set fits within `300dvh`, minimizing vertical scrolling.

#### 5.17.8.6 – Support for multiple tasks
*Summary (should):* A portlet supporting tasks on a page should be available on multiple pages where users might need to support the same tasks.

- **IMPL-5.17.8.6-01** `[JS]` `[Global]` `nav.js` registers reusable portlets in a shared registry (`config.js`) so the same `<section class="portlet">` component can be included in multiple pages/views that require the same task.

#### 5.17.8.7 – Portlets for specific roles
*Summary (should):* Portlets common to specific roles should be access-controlled by privilege; for users without permission, the access control shall be hidden and not retrievable.

- **IMPL-5.17.8.7-01** `[JS][HTML]` `[Main]` `auth.js` determines, based on the user's role (`state.user.role`), which portlets/tabs are rendered; inaccessible controls are removed from the DOM (not just hidden via CSS) so they cannot be retrieved.

#### 5.17.8.8 – Fixed-width portlets
*Summary (may):* A portlet may be fixed-width or adapt to the column width; a fixed-width portlet should be used in fixed-width column layouts.

- **IMPL-5.17.8.8-01** `[CSS]` `[Main]` A portlet may declare `data-width="fixed"` with a fixed `inline-size` in `rem`, to be used in fixed-width columns of the `#content-region` grid (`.col-fixed` class).

#### 5.17.8.9 – Variable-width portlets
*Summary (shall):* Variable-width portlets shall correctly display all content regardless of portlet size.

- **IMPL-5.17.8.9-01** `[CSS]` `[Main]` Portlets without `data-width="fixed"` use `inline-size: 100%`/`minmax(0, 1fr)` and a fluid internal layout (flex/grid), correctly displaying content at any assigned column width (from 800 px upward).

#### 5.17.8.10 – Access control
*Summary (should):* Portals should support public key infrastructure (PKI)-based access so the user logs in once and is automatically authenticated to all components/applications they have access to.

- **IMPL-5.17.8.10-01** `[JS]` `[Global]` `auth.js` should support a single sign-on flow (SSO/PKI, e.g., client certificate or token) that authenticates the user for all connected components/applications, without requiring a new login for each portlet.

#### 5.17.8.11 – Public pages
*Summary (shall):* A portal shall include a public page accessible before login.

- **IMPL-5.17.8.11-01** `[HTML][JS]` `[Main]` `nav.js` defines a public route (`#public-page`) accessible without authentication (`auth.js`), distinct from the protected views reachable only after login.

#### 5.17.8.12 – Public page content
*Summary (should):* The public page should provide organizational information, instructions for registering/obtaining an account, and direct access or a link to the login screen.

- **IMPL-5.17.8.12-01** `[HTML]` `[Main]` The public page (IMPL-5.17.8.11-01) includes organizational information, instructions for requesting an account/certificate, and a "Log in" button or link to the login screen.

#### 5.17.8.13 – Personalization
*Summary (should):* Users should be able to modify unrestricted portal pages, or add/delete default and custom pages, if permitted by the portal owner.

- **IMPL-5.17.8.13-01** `[JS]` `[Main]` When permitted by role (`auth.js`), `prefs.js` allows adding/removing/reordering portlets in `#content-region`, saving the per-user customized layout in `localStorage`/back end (`net.js`).

#### 5.17.8.14 – Content areas
*Summary (shall):* Modifiable content areas shall be predefined and selectable.

- **IMPL-5.17.8.14-01** `[JS][HTML]` `[Main]` Modifiable content areas are predefined in `config.js` as a selectable list (not free-form arbitrary content), chosen by the user via a `dialog.js` portlet-selection dialog.

#### 5.17.15.4 – Critical messages in dialog boxes
*Summary (shall):* Critical messages or those requiring user confirmation (e.g., errors) shall appear in their own dialog boxes.

- **IMPL-5.17.15.4-01** `[HTML][JS]` `[Global]` Critical messages or those requiring confirmation are presented by `dialog.js` in a modal `<dialog role="alertdialog">` hosted in `#dialog-host`, never inline in `#toast-region`/`#message-line`.

#### 5.17.15.5 – Page numbering
*Summary (should):* Each page of a multi-page display should be labeled with the current page and the total (e.g., "Page 2 of 5").

- **IMPL-5.17.15.5-01** `[HTML][JS]` `[Main]` For multi-page views, `nav.js` shows a `#page-indicator` with the text "Page X of Y" (`i18n.js`) in `#nav-region` or in the view's footer.

#### 5.17.15.6 – Display titles and headers
*Summary (shall):* Every display shall be labeled with a title or header.

- **IMPL-5.17.15.6-01** `[HTML]` `[Header]` Every view/display has an `<h1 id="app-title">` or section `<h2>` labeling the page/panel, updated by `nav.js` on each view change.

#### 5.17.15.6.1 – Unique
*Summary (shall):* The title or header shall be unique within the system.

- **IMPL-5.17.15.6.1-01** `[JS]` `[Global]` `nav.js` verifies, via the route registry (`config.js`), that every view title is unique throughout the application, flagging any duplicates during development.

#### 5.17.15.6.2 – Position
*Summary (should):* The title or header should be positioned at the top left or centered on the page.

- **IMPL-5.17.15.6.2-01** `[CSS]` `[Header]` `#app-title` is positioned at the top left of `#app-header` or centered (`justify-self: start|center`), consistently across all views.

#### 5.17.15.6.3 – Content
*Summary (shall):* The title or header shall briefly describe the content or purpose of the page.

- **IMPL-5.17.15.6.3-01** `[JS]` `[Header]` The text of `#app-title` briefly describes the purpose of the current page, set by `nav.js` on every view change via `i18n.js`.

#### 5.17.15.6.4 – Meaningful
*Summary (shall):* The title or header shall be meaningful enough to be easily learned and remembered.

- **IMPL-5.17.15.6.4-01** `[JS]` `[Global]` View titles (`config.js`, route registry) are chosen to be easily memorable, a criterion verifiable by inspection in editorial review.

#### 5.17.15.7 – Use of frames
*Summary (shall):* Frames shall border the window.

- **IMPL-5.17.15.7-01** `[CSS]` `[Main]` Every panel (`#nav-region`, `#content-region`, `#aux-region`, portlets) has a visible border (`border`, project token) that clearly delimits the panel area.

#### 5.17.15.7.1 – Position
*Summary (shall):* Frame identification shall be prominently displayed in the top-left corner of the frame.

- **IMPL-5.17.15.7.1-01** `[HTML][CSS]` `[Main]` The identification of each panel/portlet (title, IMPL-5.17.8.2.1-01) is positioned prominently in the top-left corner of the panel.

#### 5.17.15.7.2 – Size
*Summary (shall):* Frame identification shall not exceed the size of the frame when reduced to its minimum size.

- **IMPL-5.17.15.7.2-01** `[CSS]` `[Main]` The panel title uses `overflow: hidden; text-overflow: ellipsis; max-inline-size: 100%` so it never exceeds the panel's dimensions, even when reduced to the minimum.

#### 5.17.15.7.3 – Meaningful
*Summary (shall):* Frame identification shall be meaningful enough to be learned and remembered easily.

- **IMPL-5.17.15.7.3-01** `[JS]` `[Main]` See IMPL-5.17.15.6.4-01: the same meaningfulness rule applies to panel/portlet titles.

#### 5.17.15.7.4 – Content
*Summary (shall):* Frame identification shall describe the content present in the frame.

- **IMPL-5.17.15.7.4-01** `[JS]` `[Main]` The panel/portlet title text describes the actual content of the panel, set dynamically by `nav.js`/`i18n.js` based on the connected data source.

#### 5.17.15.7.5 – Separation
*Summary (shall):* At least one blank line shall separate the frame identification from the body of the frame.

- **IMPL-5.17.15.7.5-01** `[CSS]` `[Main]` A minimum vertical margin (`margin-block-end: 1em` or equivalent in `rem`) visually separates the panel/portlet title from the content body below.

#### 5.17.15.8.1 – Grouping
*Summary (shall):* All displays necessary to support an activity or sequence of activities shall be grouped together.

- **IMPL-5.17.15.8.1-01** `[HTML][JS]` `[Main]` `nav.js` groups together, in the same view/menu (`#nav-region`), all displays necessary to support a sequence of user activities, avoiding scattering them across unrelated pages.

#### 5.17.15.8.2 – Frequency of use
*Summary (shall):* The most frequently used displays shall be grouped together and placed in the optimum visual zone.

- **IMPL-5.17.15.8.2-01** `[HTML][CSS]` `[Main]` The most frequently used displays/portlets (usage data in `state.js`) are positioned in the optimum visual zone of `#content-region` (top-left in LTR), configurable via `prefs.js`.

#### 5.17.15.8.3 – Importance
*Summary (shall):* Important or critical displays shall be placed in the optimum visual zone or otherwise highlighted.

- **IMPL-5.17.15.8.3-01** `[CSS][HTML]` `[Main]` Critical/important displays are positioned in the optimum visual zone or highlighted with dedicated styling (border, `--color-critical`) relative to secondary portlets.

#### 5.17.15.8.4 – Function and sequence
*Summary (shall):* Displays shall be arranged according to the sequence of use or the functional relationships of the components represented, with a viewing flow from left to right or top to bottom; this does not apply to master warning/caution/advisory indicators.

- **IMPL-5.17.15.8.4-01** `[CSS][HTML]` `[Main]` The grid layout of `#content-region` arranges portlets according to order of use or functional relationship, with a reading flow from left to right and top to bottom (`grid-auto-flow: row`); master indicators in `#alert-region` are exempt from this constraint.

#### 5.17.15.8.5 – Consistency
*Summary (shall):* The arrangement of displays within a system shall be consistent in principle from one application to another.

- **IMPL-5.17.15.8.5-01** `[JS][CSS]` `[Global]` The grid template and the order of regions (`#nav-region`/`#content-region`/`#aux-region`, portlets) are defined once in `config.js`/shared CSS and reused identically across all views/applications of the same system.

### 3.10 Information content, format and presentation

Requirements on the amount and precision of information, abbreviations, quantitative data, format consistency, grouping, character sizes, text editing, labels, default values, tables and lists.

#### 5.17.14.1 – Amount of information
*Summary (shall):* The amount of information required shall comply with the criteria in 5.17.14.1.1-5.17.14.1.5.

- **IMPL-5.17.14.1-01** `[JS]` `[Global]` state.js shall expose only the data needed for the current view (minimal view model), applying the criteria of sub-requirements 5.17.14.1.1-5.17.14.1.5 (see following entries).

#### 5.17.14.1.1 – Scope
*Summary (shall):* Displayed information shall be clear enough to allow the user to perform the intended task.

- **IMPL-5.17.14.1.1-01** `[HTML][JS]` `[Main]` Each view in `#content-region` shall show only the fields/controls needed for the current task, determined by state.js based on role and mode (`#mode-indicator`).
- **IMPL-5.17.14.1.1-02** `[JS]` `[Global]` i18n.js shall provide text appropriate to the user's training level, configurable per role in config.js.

#### 5.17.14.1.2 – Precision
*Summary (shall):* Displayed information shall be limited to the precision required for the specific action or decision.

- **IMPL-5.17.14.1.2-01** `[JS]` `[Global]` The numeric formatting functions of i18n.js shall round each value to the number of decimal places defined per field (`precision` per data type) in config.js, not to the raw precision of the data source.

#### 5.17.14.1.3 – Context for displayed data
*Summary (shall):* Each data display shall provide contextual information, including data from previous screens where necessary.

- **IMPL-5.17.14.1.3-01** `[HTML][JS]` `[Main]` Views in `#content-region` shall include a context header (`<h2>`, breadcrumb in `#nav-region`, active filters) that recalls the selection that originated the view, maintained by nav.js/state.js.

#### 5.17.14.1.4 – Combining operator and maintainer information
*Summary (shall):* Operator and maintainer information shall not be combined in a single view unless content, format, and timeliness satisfy both uses.

- **IMPL-5.17.14.1.4-01** `[JS]` `[Global]` state.js/auth.js shall expose separate views per role (`operator`, `maintainer`) in config.js; nav.js shall not make a view intended exclusively for one role reachable from the other, unless a combined view is explicitly approved.

#### 5.17.14.1.5 – Information density
*Summary (shall):* Critical information shall be sufficiently separated and distinct from non-critical information to be salient.

- **IMPL-5.17.14.1.5-01** `[CSS]` `[Main]` `#alert-region` and `.priority-critical` elements shall have visual separation (margin ≥ `var(--touch-gap-min)`, border, background contrast) from the non-critical content of `#content-region`.
- **IMPL-5.17.14.1.5-02** `[HTML][CSS]` `[Global]` Critical elements shall use the `.priority-critical` class with `--color-critical` plus a redundant icon/text (see IMPL-5.17.25.11-01), never positioning alone as an indicator.

#### 5.17.14.2 – Presentation of information
*Summary (shall):* Information shall be presented according to the criteria in 5.17.14.2.1-5.17.14.2.7.

- **IMPL-5.17.14.2-01** `[JS]` `[Global]` See IMPL-5.17.14.2.1-01 and following for the criteria applied by i18n.js and the views.

#### 5.17.14.2.1 – Plain text
*Summary (shall):* Information shall be displayed in language appropriate to the user's level of training and specific to the application.

- **IMPL-5.17.14.2.1-01** `[JS]` `[Global]` i18n.js shall provide translation keys specific to the user profile/application domain (not generic text), configurable per role in config.js.

#### 5.17.14.2.2 – Abbreviations
*Summary (should):* Abbreviations should conform to JSSG-2010.

- **IMPL-5.17.14.2.2-01** `[JS]` `[Global]` i18n.js shall maintain a central abbreviation dictionary conforming to JSSG-2010 (or an equivalent project standard in config.js), reused by all views via common keys.

#### 5.17.14.2.3 – New abbreviations
*Summary (shall):* New abbreviations, where necessary, shall be developed using logical rules.

- **IMPL-5.17.14.2.3-01** `[JS]` `[Global]` New entries in the i18n.js dictionary shall follow a documented rule (e.g. truncation to N characters) defined in config.js, verifiable by inspection.

#### 5.17.14.2.4 – Distinctive
*Summary (shall):* Abbreviations shall be distinctive to avoid confusion.

- **IMPL-5.17.14.2.4-01** `[JS]` `[Global]` An automated test on i18n.js shall verify the absence of duplicate/ambiguous abbreviations in the dictionary (same string used for different meanings).

#### 5.17.14.2.5 – Consistent
*Summary (shall):* Words shall have only one consistent abbreviation.

- **IMPL-5.17.14.2.5-01** `[JS]` `[Global]` The i18n.js dictionary shall map each term to a single abbreviation; an automated test verifies the word-to-abbreviation uniqueness.

#### 5.17.14.2.6 – Punctuation
*Summary (shall):* No punctuation shall be used in abbreviations.

- **IMPL-5.17.14.2.6-01** `[JS]` `[Global]` An automated test on i18n.js shall reject abbreviations containing punctuation (`.`, `,`) in the dictionary, via a verification regular expression.

#### 5.17.14.2.7 – Definitions
*Summary (shall):* Definitions of all abbreviations, mnemonics and codes shall be accessible within the system.

- **IMPL-5.17.14.2.7-01** `[HTML][JS]` `[Global]` A "Glossary" view reachable from `#btn-help` shall list, via i18n.js, every abbreviation with its full definition; in the UI each abbreviation may use `<abbr title="...">`.

#### 5.17.14.3 – Quantitative information
*Summary (shall):* Quantitative information shall be displayed according to the rules in 5.17.14.3.1-5.17.14.3.5.

- **IMPL-5.17.14.3-01** `[JS]` `[Global]` See IMPL-5.17.14.3.1-01 and following.

#### 5.17.14.3.1 – Numeric digital displays
*Summary (shall):* Numeric digital displays shall be used when the task requires identifying a specific numeric quantity.

- **IMPL-5.17.14.3.1-01** `[HTML]` `[Main]` Values requiring exact identification (e.g. altitude, point speed) shall be rendered with a numeric text element (`<output>`/`<span class="value-numeric">`), not only in graphical form.

#### 5.17.14.3.2 – Pattern display
*Summary (shall):* Analog or graphic displays shall be used when comparison between quantities is important.

- **IMPL-5.17.14.3.2-01** `[HTML][CSS]` `[Main]` For comparison between quantities (levels, trends), a graphical/analog component (`<svg>`/`<canvas>`, `.gauge`/`.bar-chart` class) shall be present alongside or instead of the numeric value alone.

#### 5.17.14.3.3 – Comparison of quantities
*Summary (shall):* Numeric digital displays shall not be the only means of display when comparison between quantities is important for accurate interpretation.

- **IMPL-5.17.14.3.3-01** `[JS]` `[Main]` Views shall pair every "to be compared" numeric value with a graphical component (see IMPL-5.17.14.3.2-01); a review criterion verifies the absence of numbers alone where comparison is required.

#### 5.17.14.3.4 – Hybrid displays
*Summary (shall):* Hybrid displays, combining digital and analog/graphic display, shall be provided when a task requires both identification of a specific quantity and the ability to compare quantities.

- **IMPL-5.17.14.3.4-01** `[HTML][CSS]` `[Main]` The `.gauge` component shall include both the graphical representation (arc/bar) and the textual numeric value in the same element (e.g. `<figure class="gauge"><svg>…</svg><span class="value-numeric">…</span></figure>`).

#### 5.17.14.3.5 – Units
*Summary (shall):* Displays of quantitative information shall include units of measure.

- **IMPL-5.17.14.3.5-01** `[HTML][JS]` `[Global]` Every numeric value in the UI shall be accompanied by the unit of measure localized by i18n.js (e.g. `<span class="value-numeric">120</span><span class="unit">km/h</span>`), never the number alone.

#### 5.17.15.1 – Critical data
*Summary (shall):* Critical data shall not be hidden by pagination or scrolling.

- **IMPL-5.17.15.1-01** `[CSS]` `[Main]` `#alert-region` and `.priority-critical` elements shall remain visible (`position: sticky` or positioning outside the scrollable flow), never covered by pagination or internal scrolling of `#content-region`.

#### 5.17.15.2 – Readily usable form
*Summary (shall):* Information shall be presented in a directly usable form, without requiring the user to mentally transpose, compute, interpolate or translate it into other units, number bases or languages.

- **IMPL-5.17.15.2-01** `[JS]` `[Global]` i18n.js shall automatically convert units, number formats and language according to user preferences (prefs.js); no view shall require manual conversion calculations.

#### 5.17.15.3.1 – Display output consistency
*Summary (shall):* The content of displays and their formats shall be consistent within the system and across systems used by the same users.

- **IMPL-5.17.15.3.1-01** `[CSS][JS]` `[Global]` A common set of components/styles (tokens §2.4, config.js) shall be reused across all views; a review criterion verifies the absence of ad hoc formats for the same data type.

#### 5.17.15.3.2 – Consistency between output and input
*Summary (shall):* The same format shall be used for data entry and for displaying output data within the same task (e.g. a date entered as "mm/dd/yyyy" is shown in the same form).

- **IMPL-5.17.15.3.2-01** `[JS]` `[Global]` i18n.js/validate.js shall use the same format (`format`/`parse`) for input fields and for displaying the same data.

#### 5.17.15.3.3 – Recurring data field labels
*Summary (shall):* Recurring data fields within the system and across systems used by the same users shall have consistent labels.

- **IMPL-5.17.15.3.3-01** `[JS]` `[Global]` Labels for recurring fields shall come from a single shared i18n.js key, never duplicated strings per view.

#### 5.17.15.3.4 – Comparative data fields
*Summary (shall):* Data fields to be compared character by character shall be positioned one above the other with alignment of the characters to be compared.

- **IMPL-5.17.15.3.4-01** `[CSS]` `[Main]` Fields to be compared character by character shall be arranged in a column (`display: grid` with aligned columns) using a monospace font or `font-variant-numeric: tabular-nums`.

#### 5.17.15.3.5 – Position of recurring data fields
*Summary (shall):* Recurring data fields within the system and across systems used by the same users shall occupy consistent relative positions across displays.

- **IMPL-5.17.15.3.5-01** `[CSS]` `[Global]` View templates (CSS grid in `#content-region`) shall reuse the same areas/positions for recurring fields (e.g. application ID always top-left), via common layout classes.

#### 5.17.15.3.6 – Consistency of data entry formats
*Summary (should):* Where data shall be entered manually, order and format should be as consistent as possible.

- **IMPL-5.17.15.3.6-01** `[HTML][JS]` `[Global]` validate.js shall apply the same format pattern (`pattern`, input mask) to all fields of the same data type across all views.

#### 5.17.17 – Grouping within a display
*Summary (shall):* Information shall be ordered according to a specific purpose or context; where it does not suggest a method, it may be grouped according to the guidelines in 5.17.17.1-5.17.17.4 in order of preference.

- **IMPL-5.17.17-01** `[JS]` `[Main]` Views shall explicitly define an ordering/grouping criterion (configurable in config.js: `chronological|importance|functional|frequency`), applied by state.js before rendering.

#### 5.17.17.1 – Order and sequences
*Summary (should):* Where data fields have a natural order (chronological or sequential), that order should be reflected in the organization of the field format.

- **IMPL-5.17.17.1-01** `[JS]` `[Main]` Lists/tables with naturally ordered data (e.g. event logs) shall be sorted chronologically by default by state.js, unless the user explicitly chooses another order.

#### 5.17.17.2 – Grouped by importance
*Summary (may):* Critical data items or those requiring immediate response may be ordered at the top of the list.

- **IMPL-5.17.17.2-01** `[JS][CSS]` `[Main]` notify.js shall order `#alert-region` by descending priority (critical at the top), applying the `.priority-critical`/`.priority-alarm`/... classes.

#### 5.17.17.3 – Ordered by function
*Summary (may):* Sets of data associated with specific questions or particular functions may be grouped together to signal their functional relationships.

- **IMPL-5.17.17.3-01** `[HTML][CSS]` `[Main]` Controls/data relating to the same function shall be enclosed in a common semantic container (`<fieldset>`/`<section>` with `aria-labelledby`), visually distinct from other groups.

#### 5.17.17.4 – Ordered by frequency
*Summary (may):* Data items used more frequently may be ordered at the top of the list.

- **IMPL-5.17.17.4-01** `[JS]` `[Main]` prefs.js/state.js may track item usage frequency (menus, `#function-keys`) and reorder them accordingly, as a configurable option.

#### 5.17.18.2 – Alphanumeric character and symbol sizes
*Summary (shall/should):* Measured from the greatest anticipated viewing distance, the height of characters and pictorial symbols shall subtend not less than 2.9 milliradians (10') and should subtend not less than 4.5 milliradians (15') of visual angle.

- **IMPL-5.17.18.2-01** `[CSS]` `[Global]` `--fs-body` shall guarantee, at the design viewing distance (`--viewing-distance-mm` in config.js), a character height derived from ≥2.9 mrad (`h = 2 · d · tan(angle/2)`); the default complies with ≥16px at 800×600 (§2.3).
- **IMPL-5.17.18.2-02** `[CSS]` `[Global]` Pictorial symbols (icons) shall have `min-inline-size`/`min-block-size` consistent with `--fs-title` or greater, not smaller than the corresponding text.

#### 5.17.18.4 – Character stroke width
*Summary (shall):* Assuming character height conforms to 5.17.18.2, stroke width shall be not less than 0.0834 nor more than 0.1667 of the number of pixels of character height.

- **IMPL-5.17.18.4-01** `[CSS]` `[Global]` The font of `--font-family-ui` shall have a font-weight that produces a stroke width within the 8.34%-16.67% range of character height, verifiable by inspection of the chosen font (e.g. Arial regular/medium).

#### 5.17.18.5 – Character width
*Summary (shall):* Character width shall be approximately 0.9 of the height.

- **IMPL-5.17.18.5-01** `[CSS]` `[Global]` The font of `--font-family-ui` shall have a width/height ratio of ≈0.9 for uppercase characters (font selection criterion, verifiable by inspection).

#### 5.17.18.6 – Symbol size and image quality for complex shapes
*Summary (shall):* The size of a symbol/graphic shall be such that embedded text or graphics (e.g. label within the symbol) subtend not less than 2.9 milliradians from the greatest anticipated viewing distance.

- **IMPL-5.17.18.6-01** `[CSS]` `[Main]` Text embedded in complex SVG icons/symbols shall respect the same minimum threshold as IMPL-5.17.18.2-01 (no text-in-symbol below `--fs-small`).

#### 5.17.18.7 – Font characteristics
*Summary (shall):* Font style shall allow discrimination of similar characters (e.g. letter l/number 1, letter Z/number 2).

- **IMPL-5.17.18.7-01** `[CSS]` `[Global]` `--font-family-ui` shall use a font that clearly discriminates l/1, O/0, Z/2 (selection criterion; verifiable by inspection of the glyphs).

#### 5.17.18.7.1 – Standard font
*Summary (shall):* A common standard font shall be used (e.g. Arial, Times New Roman, Courier, Verdana).

- **IMPL-5.17.18.7.1-01** `[CSS]` `[Global]` `--font-family-ui: Arial, Verdana, Helvetica, sans-serif` (§2.4) shall be the only interface font, with no decorative/custom non-conforming fonts.

#### 5.17.18.7.2 – Sans-serif
*Summary (shall):* Where users shall read under adverse conditions (e.g. poor lighting), a sans-serif style shall be used (e.g. Arial, Verdana, Helvetica).

- **IMPL-5.17.18.7.2-01** `[CSS]` `[Global]` See IMPL-5.17.18.7.1-01: `--font-family-ui` is already sans-serif for all conditions of use, including `theme-dark`/low ambient light managed by prefs.js.

#### 5.17.18.10.1 – Alphanumeric organization
*Summary (shall):* When five or more alphanumeric characters are presented, they shall be separated into groups according to natural organization and punctuation patterns (e.g. "mm/dd/yyyy", "(123) 555-6789", "123-45-6789").

- **IMPL-5.17.18.10.1-01** `[JS]` `[Global]` i18n.js shall automatically format dates, phone numbers, codes, etc. according to the natural pattern of the data type (mask in config.js), applied both in display and in validate.js for input.

#### 5.17.18.10.2 – Combination of letters and digits
*Summary (shall):* When a code consists of both letters and digits, common character types shall be grouped together.

- **IMPL-5.17.18.10.2-01** `[JS]` `[Global]` Formatting masks in config.js for mixed codes shall group letters and digits into homogeneous blocks (e.g. `AAA-9999`), not alternated without criteria.

#### 5.17.18.10.3 – Blocking
*Summary (shall):* When five or more alphanumeric characters without natural organization are displayed, they shall be grouped in blocks of three to five characters.

- **IMPL-5.17.18.10.3-01** `[JS]` `[Global]` For codes without a natural format, i18n.js shall insert separators every 3-5 characters according to the mask defined in config.js.

#### 5.17.18.10.4 – Separation of groups
*Summary (shall):* When five or more alphanumeric characters without natural organization are displayed, the groups shall be separated from one another by at least one blank space.

- **IMPL-5.17.18.10.4-01** `[JS]` `[Global]` The formatting mask (see IMPL-5.17.18.10.3-01) shall use a space, hyphen or `/` as the minimum separator between groups, never adjacent groups without a separator.

#### 5.17.18.10.5 – Long numeric fields
*Summary (shall):* When five or more numeric characters without natural organization are displayed, long numeric fields shall be punctuated with commas, spaces or slashes.

- **IMPL-5.17.18.10.5-01** `[JS]` `[Global]` i18n.js shall format numbers of ≥5 digits with a localized thousands separator (`Intl.NumberFormat`), never as an unpunctuated string of digits.

#### 5.17.18.10.6 – Leading zeros
*Summary (shall):* Leading zeros shall not be used in numeric data.

- **IMPL-5.17.18.10.6-01** `[JS]` `[Global]` The numeric formatting functions of i18n.js shall not produce leading non-significant zeros, unless a fixed-width format is explicitly required (e.g. codes/times) declared in config.js.

#### 5.17.19 – Text and program editing
*Summary (shall):* Text and program editing shall comply with the criteria in 5.17.19.1-5.17.19.12.

- **IMPL-5.17.19-01** `[JS]` `[Global]` See IMPL-5.17.19.1-01 and following; advanced editing functions apply only to views with extended text fields (notes, logs, configuration), not to simple forms.

#### 5.17.19.1 – Buffer
*Summary (shall):* Where appropriate, when inserting characters/words/phrases (editing), the items to be inserted shall be collected in a buffer area and displayed in the prescribed insertion area for subsequent insertion by user command.

- **IMPL-5.17.19.1-01** `[JS]` `[Main]` For extended text fields with an "insert" function, the editor shall maintain a visible buffer (`.insert-buffer`) before confirming insertion via an explicit command.

#### 5.17.19.2 – Edit commands
*Summary (shall):* Editing commands (MOVE, COPY, PASTE, INSERT, DELETE) shall be provided for adding, inserting or deleting text and program segments.

- **IMPL-5.17.19.2-01** `[HTML][JS]` `[Main]` Extended text editors shall expose touch controls for Move/Copy/Paste/Insert/Delete (dedicated command bar, `min-block-size: var(--touch-target-min)`), in addition to standard keyboard shortcuts.

#### 5.17.19.2.1 – Text edit commands
*Summary (shall):* In text editing, commands shall be based on character, word, sentence, paragraph, and higher-order segments.

- **IMPL-5.17.19.2.1-01** `[JS]` `[Main]` Selection commands in the editor shall operate at different granularities (character/word/sentence/paragraph): double tap = word, long-press = paragraph.

#### 5.17.19.2.5 – Tab controls
*Summary (shall):* For editing programs or tabular data, cursor tab controls or other means shall be provided to move easily from field to field.

- **IMPL-5.17.19.2.5-01** `[HTML][JS]` `[Main]` Forms and editable tables shall support field-to-field navigation with Tab/Enter and touch "next/previous field" buttons, managed by nav.js.

#### 5.17.19.2.6 – Keying edit commands
*Summary (shall):* Where editing commands are entered by typing into the display, they shall be easily distinguishable from the displayed textual material.

- **IMPL-5.17.19.2.6-01** `[CSS]` `[Main]` Text editing commands entered as special syntax (where present) shall have a distinct style (`.edit-command` class: color/monospace) compared to normal text.

#### 5.17.19.3 – Highlighted text
*Summary (shall):* Where text is the subject of control commands (underline, bold, move, copy, delete), the affected segment shall be highlighted to indicate its boundaries.

- **IMPL-5.17.19.3-01** `[CSS]` `[Main]` Text selection in the editor shall use visible highlighting (`::selection` with `background: var(--color-selected)`) that clearly delimits the boundaries of the selected segment.

#### 5.17.19.4 – String search
*Summary (shall):* A string search capability shall be provided that allows a text string to be specified and requests automatic advance (or retreat) of the cursor to the next (or previous) occurrence.

- **IMPL-5.17.19.4-01** `[HTML][JS]` `[Main]` Extended text editors/viewers shall provide a search field (`<input type="search">`) with "next/previous" buttons that move focus/cursor to the found occurrence.

#### 5.17.19.5 – String search variance
*Summary (shall):* The search capability shall offer flexibility in choosing the search variant.

- **IMPL-5.17.19.5-01** `[HTML][JS]` `[Main]` The search field (see IMPL-5.17.19.4-01) shall offer "case sensitive", "whole word", "regular expression" options via toggles next to the input.

#### 5.17.19.6 – Automatic word wrap
*Summary (shall):* Automatic word wrap shall be provided when text reaches the right margin for entry or editing of unformatted text.

- **IMPL-5.17.19.6-01** `[CSS]` `[Main]` Multi-line unformatted text fields shall have `white-space: pre-wrap; overflow-wrap: break-word` for automatic word wrap at the margin.

#### 5.17.19.7 – Override of word wrap
*Summary (shall):* The user shall be provided the ability to disable automatic word wrap.

- **IMPL-5.17.19.7-01** `[HTML][JS]` `[Main]` Extended text editors shall offer an option (`#opt-wordwrap`) to disable automatic word wrap, saved by prefs.js.

#### 5.17.19.8 – Format control
*Summary (shall):* A means shall be provided to specify the format control characteristics required during text entry or editing (margins, tabs, line spacing).

- **IMPL-5.17.19.8-01** `[HTML][JS]` `[Main]` Formatted text editors shall expose a settings panel (margins, tabs, line spacing) in `#aux-region`, applied to the current document.

#### 5.17.19.9 – Predefined formats
*Summary (shall):* Where text formats shall follow predefined standards, the required format shall be provided automatically.

- **IMPL-5.17.19.9-01** `[JS]` `[Main]` Where a field/document has a mandatory standard format (e.g. a report), validate.js/the editor shall apply it automatically upon creation, without manual configuration.

#### 5.17.19.10 – Text formats as a user option
*Summary (shall):* Where text formats are a user option, a convenient means shall be provided to specify and store formats generated for particular applications, for future use.

- **IMPL-5.17.19.10-01** `[JS]` `[Main]` prefs.js shall allow a set of text format settings to be saved as a named preset, recallable in subsequent editing.

#### 5.17.19.11 – Frequently used text
*Summary (shall):* The capability shall be provided to label and store frequently used text segments (signatures, organization names, call signs, coordinates) and recall them by assigned label.

- **IMPL-5.17.19.11-01** `[HTML][JS]` `[Main]` There shall be a library of labeled text snippets, managed by prefs.js/state.js, insertable at the cursor position via a dedicated menu/command.

#### 5.17.19.12 – Head and foot of file
*Summary (shall):* A means shall be provided to quickly move the cursor to the beginning or end of the file.

- **IMPL-5.17.19.12-01** `[HTML][JS]` `[Main]` Extended text editors shall provide commands/shortcuts (Ctrl+Home/End, touch buttons `#btn-doc-start`/`#btn-doc-end`) to position the cursor at the start/end of the document.

#### 5.17.20.1 – Labels
*Summary (shall):* Every element of a display shall be labeled, unless its purpose is intuitively obvious to the intended user population.

- **IMPL-5.17.20.1-01** `[HTML]` `[Global]` Every interactive control/field shall have a visible label (`<label>`) or, with justified alternative, `aria-label`/`aria-labelledby`; a review criterion verifies the absence of controls without an accessible label.

#### 5.17.20.1.1 – Use
*Summary (should):* User-requested element identification (e.g. tooltip) is acceptable where space constraints preclude the use of labels.

- **IMPL-5.17.20.1.1-01** `[HTML][JS]` `[Global]` Where space does not allow visible labels, controls shall provide a tooltip (`title`/accessible popover) activatable also in touch (long-press or contextual `#btn-help`), not only via `:hover`.

#### 5.17.20.1.2 – Label characteristics
*Summary (should):* Labels should: a) relate uniquely to the described element; b) be consistent with other labels; c) be distinguishable and more salient than other elements; d) use a highlighting technique different from that used for emergency/critical messages.

- **IMPL-5.17.20.1.2-01** `[HTML]` `[Global]` Every `<label>` shall use a unique `for`/implicit association with its related control (criterion a).
- **IMPL-5.17.20.1.2-02** `[CSS]` `[Global]` Labels shall share a common style (`.field-label`: font, weight, `--color-fg`) throughout the application (criterion b) and be visually distinct from the field value (criterion c).
- **IMPL-5.17.20.1.2-03** `[CSS]` `[Global]` The label highlighting technique shall differ from that reserved for critical/emergency messages (`--color-critical` + icon), never using uppercase alone for both uses (criterion d).

#### 5.17.20.2 – Default values
*Summary (shall):* Default values shall comply with requirements 5.17.20.2.1-5.17.20.2.6.

- **IMPL-5.17.20.2-01** `[JS]` `[Global]` See IMPL-5.17.20.2.1-01 and following.

#### 5.17.20.2.1 – Use when feasible
*Summary (shall):* To reduce user workload, default values shall be used where feasible.

- **IMPL-5.17.20.2.1-01** `[JS]` `[Main]` validate.js/state.js shall pre-fill fields with a default value (from config.js or the last transaction) wherever a reasonable default exists.

#### 5.17.20.2.2 – Display automatically
*Summary (shall):* Currently defined default values shall be displayed automatically in the appropriate fields at the start of a data entry transaction.

- **IMPL-5.17.20.2.2-01** `[HTML][JS]` `[Main]` When a form is opened, fields with defaults shall show them already populated (`value`/`defaultValue` from state.js), not empty with only a placeholder.

#### 5.17.20.2.3 – Acceptance
*Summary (shall):* The user shall be able to indicate acceptance of the default value with a single keystroke.

- **IMPL-5.17.20.2.3-01** `[JS]` `[Main]` A single tap on "Confirm"/Enter on the field with a default shall accept it without further action, managed by validate.js.

#### 5.17.20.2.4 – Replace default value
*Summary (shall):* The user shall be able to replace any default value during a given transaction without modifying the default definition.

- **IMPL-5.17.20.2.4-01** `[JS]` `[Main]` Modifying a pre-filled field shall update only the current instance (transaction state in state.js), not the default in config.js.

#### 5.17.20.2.5 – Generate default values
*Summary (shall):* The user shall have the ability to generate default values if the system has not provided them.

- **IMPL-5.17.20.2.5-01** `[HTML][JS]` `[Main]` Where the system does not provide a default, a "Set as default" command shall be available on the current value, saved via prefs.js/config.js.

#### 5.17.20.2.6 – Default options
*Summary (shall):* Where a series of default values has been defined for a data entry sequence, the user shall be able to accept those default values.

- **IMPL-5.17.20.2.6-01** `[HTML][JS]` `[Main]` Multi-field forms with defaults shall offer a single "Accept all defaults" command that confirms the entire sequence in one action.

#### 5.17.20.3 – Tabular data
*Summary (shall):* Tabular data shall comply with requirements 5.17.20.3.1-5.17.20.3.14.

- **IMPL-5.17.20.3-01** `[HTML]` `[Main]` Tables shall use semantic markup `<table>`/`<thead>`/`<tbody>`/`<th scope="col|row">`; see IMPL-5.17.20.3.1-01 and following for detailed criteria.

#### 5.17.20.3.1 – Use
*Summary (shall):* Tabular displays shall be used to present row-column data in support of detailed comparison of ordered data sets.

- **IMPL-5.17.20.3.1-01** `[HTML]` `[Main]` Data requiring row-column comparison shall be rendered with `<table>`, not with lists or card grids lacking columnar alignment.

#### 5.17.20.3.2 – Standard formats
*Summary (shall):* The position of recurring data shall be consistent across all tabular displays and common throughout the system.

- **IMPL-5.17.20.3.2-01** `[CSS]` `[Global]` A common table component (`.data-table`) with a standard column order per data type shall be reused by all tabular views, not ad hoc implementations.

#### 5.17.20.3.3 – Arrangement
*Summary (should):* Where tabular data is ordered within rows, it should be in increasing order from left to right; data ordered within columns should follow an order dictated by context.

- **IMPL-5.17.20.3.3-01** `[JS]` `[Main]` The default ordering applied by state.js to `.data-table` rows shall be increasing from left to right within a row, unless a contextual criterion applies per column (e.g. chronological), configurable per table.

#### 5.17.20.3.4 – Titles
*Summary (shall):* Where tabular data is divided into classifications, the classification titles shall be displayed.

- **IMPL-5.17.20.3.4-01** `[HTML]` `[Main]` Tables with classified row groups shall use `<caption>`/group header rows (`<tr class="group-header">` with `colspan`) to show the classification title.

#### 5.17.20.3.5 – Vertical extension – titles
*Summary (shall):* Where tabular data extends over more than one page vertically, columns shall be titled identically on each page.

- **IMPL-5.17.20.3.5-01** `[CSS][JS]` `[Main]` For paginated/scrollable tables, `<thead>` shall remain `position: sticky` at the top of `#content-region` or be repeated identically on each page managed by state.js.

#### 5.17.20.3.6 – Horizontal extension
*Summary (should):* Tabular displays should not extend over more than one page horizontally.

- **IMPL-5.17.20.3.6-01** `[CSS]` `[Main]` `.data-table` shall adapt its columns (collapsible secondary columns, responsive layout) to remain within the width of `#content-region` at 800px, without horizontal page scrolling (§2.3).

#### 5.17.20.3.7 – Horizontal extension – titles
*Summary (shall):* If horizontal extension over more than one page is required, rows shall be titled identically on each page.

- **IMPL-5.17.20.3.7-01** `[CSS]` `[Main]` If `.data-table` uses an internal horizontal scroll, the first column (row labels) shall remain `position: sticky` visible through every scroll.

#### 5.17.20.3.8 – Distinctive and informative labels
*Summary (shall):* Rows and columns shall be labeled distinctively to guide data entry.

- **IMPL-5.17.20.3.8-01** `[HTML]` `[Main]` Every editable row/column `<th>` shall have unique, descriptive text, associated with its `<td>` cells via `scope`/`headers`.

#### 5.17.20.3.9 – Layout of numeric entry
*Summary (shall):* Users shall be able to make numeric entries in tables without concern for justification; the computer shall right-justify integers or justify them with respect to the decimal point when present.

- **IMPL-5.17.20.3.9-01** `[CSS][JS]` `[Main]` Numeric cells of `.data-table` shall have `text-align: right` with alignment to the decimal separator (`font-variant-numeric: tabular-nums`); validate.js shall not require manual justification.

#### 5.17.20.3.10 – Labeling units of measurement
*Summary (shall):* The units of displayed data shall be consistently included in the displayed column and row labels.

- **IMPL-5.17.20.3.10-01** `[HTML]` `[Main]` Column/row `<th>` elements of `.data-table` shall include the unit of measurement (e.g., "Speed (km/h)"), localized by i18n.js, not repeated in every cell.

#### 5.17.20.3.11 – Consistent column spacing
*Summary (shall):* The widths of columns containing the same data elements shall be uniform and consistent within a table and from one table to another.

- **IMPL-5.17.20.3.11-01** `[CSS]` `[Global]` Columns of the same data type in `.data-table` shall share the same width class (`.col-w-date`, `.col-w-numeric`) across all tables in the application.

#### 5.17.20.3.12 – Consistent row spacing
*Summary (shall):* The heights of rows containing the same data elements shall be uniform and consistent within a table and from one table to another.

- **IMPL-5.17.20.3.12-01** `[CSS]` `[Global]` `.data-table tr` shall have a uniform `min-block-size`, consistent with `--touch-target-min` when the row is selectable, reused across all tables.

#### 5.17.20.3.13 – Column scanning cues
*Summary (shall):* A column separation of not less than three spaces shall be maintained.

- **IMPL-5.17.20.3.13-01** `[CSS]` `[Global]` `.data-table th, .data-table td` shall have a minimum `padding-inline` equivalent to at least 3 characters (`ch`) for visual separation between columns.

#### 5.17.20.3.14 – Row scanning cues
*Summary (shall):* A group of rows shall be separated at regular intervals of not more than every five lines.

- **IMPL-5.17.20.3.14-01** `[CSS]` `[Main]` `.data-table` shall highlight a group of rows every 5 (`:nth-child(5n)` with alternating light background) to facilitate visual scanning.

#### 5.17.20.4.1 – List arrangement
*Summary (shall):* Items in lists shall be arranged in a recognizable order (chronological, alphabetical, sequential, functional, or by importance).

- **IMPL-5.17.20.4.1-01** `[JS]` `[Main]` Every data `<ul>/<ol>` shall have an explicit ordering criterion applied by state.js, user-selectable where relevant.

#### 5.17.20.4.2 – List lines
*Summary (shall):* Each item in a list shall start on a new line.

- **IMPL-5.17.20.4.2-01** `[HTML][CSS]` `[Main]` Lists shall use `<li>` for each item with `display: list-item`, never inline-concatenated elements on the same line.

#### 5.17.20.4.3 – Vertical extension
*Summary (shall):* Where lists extend over more than one display page, the last line of one page shall be the first line of the following page.

- **IMPL-5.17.20.4.3-01** `[JS]` `[Main]` Pagination/infinite scroll of long lists (state.js) shall not skip or duplicate items between one page and the next (continuity guaranteed by index/cursor).

#### 5.17.20.4.4 – Marking multi-line items in a list
*Summary (shall):* Where an item continues for more than one line, it shall be marked (e.g., blank line, indentation) so that the continuation of the item is obvious.

- **IMPL-5.17.20.4.4-01** `[CSS]` `[Main]` Multi-line list items shall use `text-indent`/`padding-inline-start` for the lines after the first, making the continuation visually distinguishable.

#### 5.17.20.4.5 – Vertical ordering in multiple columns
*Summary (shall):* Where items in a list are displayed in multiple columns, they shall be ordered vertically within each column.

- **IMPL-5.17.20.4.5-01** `[CSS]` `[Main]` Multi-column lists (`grid-auto-flow: column`) shall fill each column vertically before moving to the next, not row by row.

#### 5.17.20.4.6 – Arabic numerals
*Summary (shall):* When listed items are to be numbered, Arabic numerals shall be used rather than Roman numerals.

- **IMPL-5.17.20.4.6-01** `[CSS]` `[Main]` Numbered `<ol>` elements shall use `list-style-type: decimal`, never `upper-roman`/`lower-roman`.

#### 5.17.20.4.7 – Hierarchic structure for long lists
*Summary (shall):* Where lists are long and extend beyond one page, a hierarchic structure shall be used to permit logical partitioning into shorter related lists.

- **IMPL-5.17.20.4.7-01** `[HTML][JS]` `[Main]` Long lists shall be organized into expandable categories/subcategories (`<details>`/tree with `aria-expanded`), managed by nav.js, instead of a single flat scrollable list.

### 3.11 Coding of information (color, brightness, flash, size, pattern, symbol, shape)

Visual coding techniques and their translation into CSS tokens/classes; Table XL (Appendix A) is translated into the `--color-*` mapping below.

#### 5.17.24.1 – Use of coding
*Summary (shall):* Coding shall facilitate discrimination between views (different operating modes), identify related displays, show relationships between displays, and identify critical information, unusual values, changed/to-be-changed items, high-priority messages, special display areas, entry errors, command criticality, and targets.

- **IMPL-5.17.24.1-01** `[CSS][JS]` `[Global]` The application shall define and reuse a common set of coding classes (`.priority-critical`/`.priority-alarm`/`.priority-warning`/`.priority-normal`/`.priority-advisory`/`.is-changed`/`.is-new`/`.has-error`) applied by feedback.js/notify.js/validate.js for all listed cases.

#### 5.17.24.2 – Meaningful coding
*Summary (shall):* Meaningful codes shall be used, not arbitrary ones.

- **IMPL-5.17.24.2-01** `[CSS]` `[Global]` Each coding class/token (color, icon, pattern) shall have a documented meaning in config.js (`codeMeaning` table), not values chosen arbitrarily per view.

#### 5.17.24.3 – Common coding conventions
*Summary (should):* Commonly used coding conventions should be used.

- **IMPL-5.17.24.3-01** `[CSS]` `[Global]` The color→meaning mapping shall follow the conventions of Table XL (see IMPL-5.17.25.8-01), not proprietary conventions conflicting with common use (e.g., red = normal).

#### 5.17.24.4 – Impact on performance
*Summary (shall):* Coding shall not reduce legibility nor adversely affect user or system performance.

- **IMPL-5.17.24.4-01** `[CSS]` `[Global]` The contrast criteria (`--contrast-*`, §2.4, ≥6:1) shall also hold with active coding colors/patterns; a review criterion verifies minimum contrast on every coding-class/background combination.

#### 5.17.24.5 – Consistent coding
*Summary (shall):* All coding within the system shall be consistent.

- **IMPL-5.17.24.5-01** `[CSS][JS]` `[Global]` The coding classes/tokens defined in config.js/CSS shall be the only ones permitted; a review criterion verifies the absence of hardcoded colors or patterns in view-local CSS.

#### 5.17.24.6 – Techniques
*Summary (may):* The following visual characteristics may be used for coding: color, brightness, flash, size, pattern, location, underlining, symbol, or shape.

- **IMPL-5.17.24.6-01** `[CSS]` `[Global]` config.js/CSS shall make all techniques available as reusable tokens/classes: `--color-*` (color), `.brightness-high`/`.brightness-low` (brightness, 5.17.26), `.is-flashing` (flash, 5.17.27), `.size-s`/`.size-m`/`.size-l` (size, 5.17.28), `.pattern-*` (pattern, 5.17.29), location (layout), `.is-underlined` (underlining, 5.17.30), SVG icons (symbol, 5.17.31), `.shape-*` (shape, 5.17.32).

#### 5.17.25.1 – Use
*Summary (may):* Color coding may be used to differentiate classes of information in complex, dense, or critical displays, or to indicate similarity among items (grouping) along a meaningful dimension.

- **IMPL-5.17.25.1-01** `[CSS]` `[Global]` Color as coding shall be reserved for differentiating classes of information or meaningful groupings (`.color-*` classes of §2.4), never used as a purely decorative means without meaning.

#### 5.17.25.2 – Foveal view only
*Summary (shall):* Color shall not be used to attract attention outside the optimal visual field.

- **IMPL-5.17.25.2-01** `[CSS][JS]` `[Global]` Alerts requiring immediate attention (notify.js) shall not rely solely on a peripheral color change; they shall also use `#alert-summary`/`#alert-region` in the central reading area, sound (`beep()`), or vibration.

#### 5.17.25.3 – Consistency
*Summary (shall):* Color coding shall be used consistently within a display and, where appropriate, across displays of other systems used by the same users.

- **IMPL-5.17.25.3-01** `[CSS]` `[Global]` The tokens `--color-critical`/`--color-alarm`/`--color-warning`/`--color-normal`/`--color-advisory`/`--color-inactive` (§2.4) shall be the only semantic colors used, with the same meaning in every view.

#### 5.17.25.4 – Color customization
*Summary (shall):* Color customization by the user shall be allowed only for information that is not tactically significant.

- **IMPL-5.17.25.4-01** `[JS]` `[Global]` prefs.js shall allow customization only of non-semantic colors (general theme `theme-light`/`theme-dark`), not of the `--color-critical`/`--color-alarm`/... tokens, which remain fixed for tactical/safety significance.

#### 5.17.25.5 – Color recognition
*Summary (shall):* When the user shall recognize categories of information (e.g., different variables in a chart), at most 11 nameable colors shall be used to represent categorically different information.

- **IMPL-5.17.25.5-01** `[CSS][JS]` `[Main]` The categorization palette for charts/maps (`config.js: categoryColors`) shall not define more than 11 distinct nameable colors; an automated test verifies the count.

#### 5.17.25.6 – Color differentiation
*Summary (should):* When color groups elements of a display with no meaning beyond grouping, the colors should be discriminable.

- **IMPL-5.17.25.6-01** `[CSS]` `[Global]` Colors used solely for visual (non-semantic) grouping shall meet the minimum threshold of 20 ΔE (see IMPL-5.17.25.16.2-01), verifiable by inspection of the values in the `categoryColors` palette.

#### 5.17.25.7 – Saturation coding
*Summary (may):* Saturation may indicate relative intensity (e.g., hotter, more dangerous): a) from hotter to colder, saturation varies from red to blue; b) saturation of red increases with danger.

- **IMPL-5.17.25.7-01** `[CSS][JS]` `[Main]` Where a relative intensity is represented (temperature, hazard level), the color scale shall vary from `--color-advisory` (cold/low) to `--color-critical` (hot/dangerous) monotonically, defined as a scale in config.js (criteria a, b).

#### 5.17.25.8 – Color meanings (Table XL)
*Summary (shall):* Colors shall be associated with the common meanings of Table XL.

- **IMPL-5.17.25.8-01** `[CSS]` `[Global]` The `--color-*` tokens translate Table XL: `--color-critical` (red: critical, danger, emergency, alarm, stop, failure), `--color-alarm` (orange: alarm/alert, elevated threat), `--color-warning` (yellow: caution, abnormal status), `--color-normal` (green: normal, safe, ready, on), `--color-advisory` (blue/cyan: advisory, moderate threat, cold), `--color-inactive` (gray: inactive/unavailable).
- **IMPL-5.17.25.8-02** `[CSS]` `[Global]` For Table XL meanings not covered by the main tokens, config.js shall define: `--color-hazard` (magenta: radiation alarm/hazard), class `.is-outline` (black: border/outline, not a status color) and `.is-neutral` (white: empty/action in progress/medical personnel), without introducing meanings not foreseen by the table.
- **IMPL-5.17.25.8-03** `[JS]` `[Global]` config.js shall expose a `colorMeaning` table (id → {token, label}) consistent with Table XL, used by notify.js/state.js to automatically assign the correct `.color-*` class based on event/status type, the single source of truth for the mapping.
- **IMPL-5.17.25.8-04** `[HTML][CSS]` `[Global]` Every element that uses a semantic color from Table XL shall apply the corresponding `.color-critical`/`.color-alarm`/`.color-warning`/`.color-normal`/`.color-advisory`/`.color-inactive` class plus a redundant icon/text (see IMPL-5.17.25.11-01), never color as the sole indicator.

#### 5.17.25.8.1 – Cool colors
*Summary (shall):* Cool colors (shorter wavelength, e.g., blue or green) shall be used to display infrequently used information and to convey the status of background information.

- **IMPL-5.17.25.8.1-01** `[CSS]` `[Global]` Low-usage-frequency background/secondary information shall use `--color-advisory`/`--color-normal` (blue/green), never warm colors reserved for action.

#### 5.17.25.8.2 – Warm colors
*Summary (shall):* Warm colors (longer wavelength, e.g., red or orange) shall be used to convey an action or the need for a response.

- **IMPL-5.17.25.8.2-01** `[CSS]` `[Global]` Elements that require a user action or response (confirmations, alarms) shall use `--color-critical`/`--color-alarm` (red/orange), consistent with IMPL-5.17.25.8-01.

#### 5.17.25.10 – Color-blind users
*Summary (should):* If the user population includes color-blind users, every effort should be made to use non-confusable colors for coding.

- **IMPL-5.17.25.10-01** `[CSS][JS]` `[Global]` `theme-high-contrast`/prefs.js shall offer an alternative palette safe for color blindness (deuteranopia/protanopia), verified with a simulation tool; the `--color-*` tokens remain perceptually distinguishable in both palettes.

#### 5.17.25.11 – Redundant coding
*Summary (shall):* If non-confusable colors cannot be used for color-blind users, redundant coding shall be used.

- **IMPL-5.17.25.11-01** `[HTML][CSS]` `[Global]` Every semantic use of color (`.color-*`) shall be accompanied by at least a second channel (SVG icon, text, pattern, or shape, "double coding" per §2.4); a review criterion verifies the absence of information conveyed by color alone.

#### 5.17.25.13 – Object size
*Summary (shall/should):* When accurate color perception is required, the major dimension of isolated large symbols shall subtend not less than 8.7 milliradians (30') and should subtend not less than 13.1 milliradians (45').

- **IMPL-5.17.25.13-01** `[CSS]` `[Main]` Isolated colored symbols that require accurate color recognition (e.g., large status indicators) shall have a minimum size calculated from `--viewing-distance-mm`/`--px-per-mm` to meet the 8.7 mrad threshold, documented in config.js.

#### 5.17.25.14 – Object height
*Summary (should):* When accurate color perception is required, the height of small symbols and characters should subtend at least 5.8 milliradians (20') from the longest anticipated viewing distance.

- **IMPL-5.17.25.14-01** `[CSS]` `[Main]` Small colored symbols/characters (e.g., status badges) shall meet a minimum height equivalent to 5.8 mrad at the design distance, consistent with the minimum `--fs-small` defined in config.js.

#### 5.17.25.15 – Fill symbols
*Summary (shall):* To improve detectability and discriminability, filled (fill) colored symbols shall be used instead of outline-only symbols.

- **IMPL-5.17.25.15-01** `[CSS]` `[Global]` Status icons/semantic indicators shall use a solid `fill` (not only `stroke`/outline) with the corresponding `--color-*` color.

#### 5.17.25.16.3 – Text/background pairings for daylight or artificial lighting
*Summary (should):* Text ≤14pt should have a contrast ratio >4.5:1; text >14pt should have a ratio ≥3:1; black/dark text on a light background.

- **IMPL-5.17.25.16.3-01** `[CSS]` `[Global]` `theme-light` shall guarantee a text/background contrast ratio >4.5:1 for `--fs-body`/`--fs-small` (≤14pt) and ≥3:1 for `--fs-title` (>14pt), with dark text on a light background, verifiable with an automated contrast tool.

#### 5.17.25.16.4 – Text/background pairings for dark conditions
*Summary (should):* White/light text should be on a dark background.

- **IMPL-5.17.25.16.4-01** `[CSS]` `[Global]` `theme-dark` (`data-theme="theme-dark"`, prefs.js) shall use light text on a dark background, with the same minimum contrast ratios as IMPL-5.17.25.16.3-01.

#### 5.17.26.1 – Use
*Summary (should):* Brightness-intensity coding should be used only to differentiate an information item from adjacent information.

- **IMPL-5.17.26.1-01** `[CSS]` `[Global]` `.brightness-high`/`.brightness-low` shall be used only to distinguish an element from adjacent ones within the same view, not as primary semantic coding (reserved for color/icon, see 5.17.25.8).

#### 5.17.26.2 – Brightness intensity levels
*Summary (shall):* No more than two levels of brightness shall be used, and each level shall be separated from the nearest other level by not less than a 2:1 ratio.

- **IMPL-5.17.26.2-01** `[CSS]` `[Global]` config.js/CSS shall define at most two brightness levels (`--brightness-high`, `--brightness-low`) with a luminance ratio ≥2:1 between them; an acceptance test verifies the absence of a third level in use.

#### 5.17.26.3 – Brightness inversion
*Summary (may/shall):* Brightness inversion ("reverse video") may be used to highlight critical elements; if used for alerting purposes, it shall be reserved exclusively for that purpose and not for general highlighting.

- **IMPL-5.17.26.3-01** `[CSS][JS]` `[Global]` The `.reverse-video` class shall be reserved exclusively for alert states managed by notify.js/feedback.js (`.priority-critical`); it shall not be applied for generic non-alert highlighting.

#### 5.17.27.1 – Use
*Summary (should):* Flash coding should be used to call the user's attention only to mission-critical events.

- **IMPL-5.17.27.1-01** `[CSS][JS]` `[Global]` The `.is-flashing` class shall be applied by notify.js only to mission-critical events (`.priority-critical`/`.priority-alarm`), never for decorative emphasis or non-critical information.

#### 5.17.27.2 – Duty cycle
*Summary (shall):* The "on" time percentage shall be equal to or not less than the "off" time percentage; a 50 percent duty cycle is preferred.

- **IMPL-5.17.27.2-01** `[CSS]` `[Global]` The `@keyframes flash` animation of `.is-flashing` shall have an on/off duty cycle of 50% (default, never <50% on).

#### 5.17.27.3 – Flash rate
*Summary (shall):* No more than two flash rates shall be used.

- **IMPL-5.17.27.3-01** `[CSS]` `[Global]` CSS shall define at most two variants (`.is-flashing-fast`, `.is-flashing-slow`) as the only flash rates permitted in the application.

#### 5.17.27.3.1 – Flash rate differences
*Summary (shall):* The two flash rates shall differ by not less than 2.0 Hz.

- **IMPL-5.17.27.3.1-01** `[CSS]` `[Global]` The `animation-duration` values of `.is-flashing-fast` and `.is-flashing-slow` shall correspond to rates that differ by ≥2.0 Hz, documented in config.js.

#### 5.17.27.3.2 – Higher flash rate
*Summary (shall):* The higher flash rate shall not be greater than 5.0 Hz.

- **IMPL-5.17.27.3.2-01** `[CSS]` `[Global]` `.is-flashing-fast` shall have a rate ≤5.0 Hz (`animation-duration ≥ 0.2s`).

#### 5.17.27.3.3 – Slower flash rate
*Summary (shall):* The slower flash rate shall not be less than 0.8 Hz.

- **IMPL-5.17.27.3.3-01** `[CSS]` `[Global]` `.is-flashing-slow` shall have a rate ≥0.8 Hz (`animation-duration ≤ 1.25s`).

#### 5.17.27.3.4 – Critical information
*Summary (shall):* The higher flash rate shall reflect the more critical information.

- **IMPL-5.17.27.3.4-01** `[CSS][JS]` `[Global]` notify.js shall assign `.is-flashing-fast` exclusively to `.priority-critical` and `.is-flashing-slow` to lower priorities that still require flashing (`.priority-alarm`).

#### 5.17.27.4 – Flash rate synchronization
*Summary (shall):* Items flashing at the same rate shall be synchronized.

- **IMPL-5.17.27.4-01** `[CSS]` `[Global]` All instances of `.is-flashing-fast`/`.is-flashing-slow` shall share the same `animation` with `animation-delay: 0s` and a common timeline, to remain synchronized.

#### 5.17.27.5 – Flash rate text
*Summary (shall/may):* Characters that shall be read shall not flash; an adjacent flashing symbol, a flashing background, or alternating intensity may add emphasis to text.

- **IMPL-5.17.27.5-01** `[CSS]` `[Global]` `.is-flashing` shall not be applied directly to readable text; emphasis shall be obtained by flashing an adjacent icon/background (`.is-flashing` on the container/icon, text always stable).

#### 5.17.27.6 – Flash suppression
*Summary (shall):* An event acknowledgment or flash suppression control shall be provided.

- **IMPL-5.17.27.6-01** `[HTML][JS]` `[Global]` Every `.is-flashing` element shall be paired with an "Acknowledge" command (`feedback.js: acknowledge(el)`) that removes the class/stops the animation after user interaction.

#### 5.17.27.7 – Flashing area
*Summary (should):* Only a small area of a display should flash at a given time.

- **IMPL-5.17.27.7-01** `[CSS][JS]` `[Global]` notify.js shall limit the number of simultaneous `.is-flashing` elements (threshold configurable in config.js, e.g., max 1-2 per view) to prevent a large area of the screen from flashing at once.

#### 5.17.28 – Size coding
*Summary (shall):* No more than three size levels shall be used; the larger size shall be not less than 150% of the major dimension of the smaller.

- **IMPL-5.17.28-01** `[CSS]` `[Global]` config.js/CSS shall define at most three size classes (`.size-s`, `.size-m`, `.size-l`) for coding, with `.size-l` ≥150% of `.size-s` (based on `--fs-small`/`--fs-body`/`--fs-title`).

#### 5.17.29 – Pattern and location coding
*Summary (may):* Pattern and location coding may be used to reduce user search time, restricting the area to be searched to prescribed segments.

- **IMPL-5.17.29-01** `[CSS]` `[Main]` Functional areas (`#nav-region`, `#content-region`, `#aux-region`, `#alert-region`) shall maintain a fixed, consistent position across views; `.pattern-*` classes (hatching, background texture) may serve as secondary coding redundant to color for data categories.

#### 5.17.30 – Underlining coding
*Summary (may):* Underlining may be used to indicate unusual values, entry errors, changed items, or items to be changed.

- **IMPL-5.17.30-01** `[CSS][JS]` `[Global]` The `.is-underlined` class (paired with `.is-changed`/`.has-error`) managed by validate.js/state.js may be used as additional (not exclusive) coding for unusual values, errors, and changed or to-be-changed items.

#### 5.17.31.1 – Use
*Summary (may):* Symbol coding may be used to enhance information assimilation from data displays.

- **IMPL-5.17.31.1-01** `[HTML][CSS]` `[Global]` Common SVG icons shall be reused as a shared library to improve information assimilation in data views, instead of text/color alone.

#### 5.17.31.2 – Symbol selection
*Summary (shall):* Symbols shall be analogs of the event or system element they represent, or in general use and well known to the intended users.

- **IMPL-5.17.31.2-01** `[HTML]` `[Global]` Icons used shall be chosen from a standard/conventional set (e.g., ISO 7000 icons, Material Symbols) analogous to the represented meaning, not arbitrary invented symbols.

#### 5.17.31.3 – Special symbols
*Summary (shall):* When special symbols are used to signal critical conditions, they shall be used only for that purpose.

- **IMPL-5.17.31.3-01** `[HTML][CSS]` `[Global]` The symbol reserved for critical conditions (e.g., alarm triangle `--color-critical`) shall not be reused for other meanings in any view; a review criterion verifies uniqueness of use.

#### 5.17.31.4 – Markers close to words marked
*Summary (shall):* When a special symbol is used to mark a word, the symbol shall be separated from the start of the word by one space.

- **IMPL-5.17.31.4-01** `[CSS]` `[Global]` Inline symbols/icons adjacent to text (e.g., an error icon before a label) shall have `margin-inline-end` ≥1 character between the icon and the marked word.

#### 5.17.32 – Shape coding
*Summary (may/should):* Shape coding may be used for search and identification tasks; selected codes should be based on established standards or conventional meanings.

- **IMPL-5.17.32-01** `[CSS]` `[Global]` The `.shape-circle`/`.shape-triangle`/`.shape-square`/`.shape-diamond` classes (badges/indicators) shall follow established conventional meanings (e.g., triangle = caution, circle = status, square = stop/block), documented in config.js, never arbitrary shapes.

#### 5.17.25.16.1 – Color contrast
*Summary (shall):* Colored symbols shall differ from the background by not less than 100 ΔE (CIE L*u*v*), calculated with the formula ΔE(Yu'v') = [(155·ΔY/YM)² + (367·Δu')² + (167·Δv')²]^(1/2), with ΔY the luminance difference, YM the maximum display luminance, Δu'/Δv' the UCS coordinate differences.

- **IMPL-5.17.25.16.1-01** `[CSS]` `[Global]` Every colored symbol/background pair in the `--color-*` tokens shall meet ΔE(Yu'v') ≥ 100 per the standard formula; verification is an acceptance test by inspection/colorimetric instrument on the values defined in config.js.

#### 5.17.25.16.2 – Color differences
*Summary (shall):* Colors in a set shall differ from one another by not less than 20 ΔE (CIE L*u*v*), referenced to the display white (D6500K or 9300K+27MPCD), per the document's formula.

- **IMPL-5.17.25.16.2-01** `[CSS]` `[Global]` The set of `--color-*` semantic colors and the `categoryColors` palette (config.js) shall have a pairwise ΔE(L*u*v*) distance ≥ 20, verified with a tool/acceptance test during palette definition.

### 3.12 Graphics, dynamic displays, geospatial displays, printing and data transmission

Requirements for generic graphical interfaces, graphs/diagrams, real-time data updates, geospatial displays, printing, and transmission of messages/data; implemented in `#content-region` with `<canvas>`/SVG elements, tables, and forms, supported by `state.js`, `net.js`, and `notify.js`.

#### 5.2.1.1.3 – Motion of graphic elements
*Summary (shall):* Animated graphic elements shall not move faster than 60°/s of visual angle (≤20°/s preferred).

- **IMPL-5.2.1.1.3-01** `[JS]` `[Main]` Animations in `#content-region` (e.g., rotations, translations on `<canvas>`/SVG) shall limit angular speed to a design value in `config.js` (`maxAngularSpeedDegPerSec`, default 20, maximum 60), applied via `requestAnimationFrame`.
- **IMPL-5.2.1.1.3-02** `[JS]` `[Global]` `feedback.js` shall expose `clampAnimationSpeed(deltaDeg, deltaTimeMs)` used by all dynamic display modules to avoid exceeding the configured threshold.

#### 5.2.1.1.4 – Orientation of objects
*Summary (shall):* Displays shall include a vertical/horizontal reference (e.g., "North"), provide cues of relative orientation between objects (front/back, top/bottom), and dynamic cues (direction/speed).

- **IMPL-5.2.1.1.4-01** `[HTML][CSS]` `[Main]` Every orientable graphic display shall include a textual/iconic reference indicator (`.orientation-ref`, e.g., "N↑") anchored with `position: sticky` at the corner of the `<canvas>`/SVG.
- **IMPL-5.2.1.1.4-02** `[JS]` `[Main]` `state.js` shall provide `facing`, `zOrder`, and `velocityVector` attributes for each graphic object, rendered visually (arrows, front/back shading) by the rendering module.
- **IMPL-5.2.1.1.4-03** `[JS]` `[Main]` The direction and speed of object movement shall be coded with vectors/arrows scaled proportionally to `velocityVector`, redrawn on every frame.

#### 5.2.1.1.5 – Flash coding
*Summary (shall):* Flash coding shall comply with the criteria of 5.17.27 (see the requirements dedicated to flash coding in another section).

- **IMPL-5.2.1.1.5-01** `[CSS][JS]` `[Global]` Flashing graphic elements shall use the common `.flash-code` class (see `IMPL-5.17.27-*`), with rate and duration controlled by `config.js`, without duplicating the logic here.

#### 5.17.21.1 – Graphical user interfaces
*Summary:* Reference to ANSI/HFES 200 for additional GUI guidance (no additional direct requirement).

- **IMPL-5.17.21.1-01** `[HTML][CSS]` `[Global]` Graphical interfaces in `#content-region` shall reuse common components/tokens (§2.4, §2.6) instead of introducing ad hoc visual patterns, as a code review criterion.

#### 5.17.21.1.1 – Selecting from displayed attributes
*Summary (shall):* During graphic data entry, the user shall be able to specify attributes for elements (font, plotting symbol, line type).

- **IMPL-5.17.21.1.1-01** `[HTML]` `[Main]` A `#graphic-attrs-panel` panel in `#aux-region` shall expose controls (`<select>` for `font-family`, symbol, `line-dash`) linked to the selected graphic element.
- **IMPL-5.17.21.1.1-02** `[JS]` `[Main]` `validate.js` shall apply attribute changes via a `CustomEvent('graphic-attr-change')` handled by `state.js`.

#### 5.17.21.1.2 – Examples
*Summary (should):* The capability to specify attributes should include illustrative examples of the available options.

- **IMPL-5.17.21.1.2-01** `[HTML][CSS]` `[Main]` Every `<select>` in `#graphic-attrs-panel` should show visual previews of the options (e.g., line swatch, sample glyph) via `<option>` with a background icon or an illustrated `<datalist>`.

#### 5.17.21.1.3 – Selecting graphic elements
*Summary (shall):* The user shall have a means to designate/select displayed graphic elements when their manipulation is required (typically a pointer/cursor).

- **IMPL-5.17.21.1.3-01** `[HTML][JS]` `[Main]` Selectable graphic elements shall handle `pointerdown`/`pointerup` (*last contact* strategy), setting `aria-selected="true"` and `.is-selected`, with a hit area ≥ `--touch-target-min`.

#### 5.17.21.1.4 – Displaying current attributes
*Summary (shall):* Attributes that will be affected by a change shall be highlighted.

- **IMPL-5.17.21.1.4-01** `[CSS][JS]` `[Main]` When an attribute control is focused/being edited, the target graphic element shall receive `.is-highlighted` (distinctive outline) until the change is confirmed or canceled.

#### 5.17.21.1.5 – Easy storage and retrieval
*Summary (shall):* The user shall have a means to save and retrieve graphic data displays.

- **IMPL-5.17.21.1.5-01** `[HTML][JS]` `[Main]` The `#btn-save-graphic`/`#btn-load-graphic` controls shall serialize the graphic state (`state.js`) to JSON, saved via `net.js` or `localStorage` through `prefs.js`.

#### 5.17.21.1.6 – Automatic data registration
*Summary (shall):* The system shall provide automatic registration/alignment of computer-generated graphic data with respect to the fixed background or data map, at any scale.

- **IMPL-5.17.21.1.6-01** `[JS]` `[Main]` The rendering module shall apply a shared coordinate transform (`state.js` → `viewTransform`) to the overlay and base map, automatically recalculated on every zoom/pan to keep alignment.

#### 5.17.21.1.7 – Predefined graphic formats
*Summary (shall):* Where graphic data shall be plotted in predefined standard formats (e.g., target areas on maps, flight plans), templates or skeletal displays shall be provided to aid data entry.

- **IMPL-5.17.21.1.7-01** `[HTML][JS]` `[Main]` A `#template-select` selector shall be available that preloads predefined skeletal shapes/overlays (from `config.js`) into the drawing area.

#### 5.17.21.1.8 – Changing size
*Summary (shall):* During graphic data editing, the user shall be able to change the size (scale) of a selected element instead of deleting and recreating it.

- **IMPL-5.17.21.1.8-01** `[HTML][JS]` `[Main]` Selected graphic elements shall expose resize handles (`.resize-handle`, ≥ `--touch-target-min`) that update `scale`/`size` in `state.js` without destroying the element.

#### 5.17.21.1.9 – Highlighting critical data
*Summary (shall):* When the user's attention shall be directed to a portion of the graphic display with critical/abnormal data, that element shall be highlighted with a distinctive data coding.

- **IMPL-5.17.21.1.9-01** `[CSS][JS]` `[Main]` Graphic elements with critical data shall receive the `.is-critical` class (`--color-critical` color, icon, and, where relevant, `.flash-code`), applied by `notify.js` based on `config.js` thresholds.

#### 5.17.21.1.10 – Computer derivation of graphic data
*Summary (should):* Common or complex computations (e.g., interpolation curves, plotting of coordinates) should be performed by the system on user request.

- **IMPL-5.17.21.1.10-01** `[JS]` `[Main]` A `#btn-compute-fit` command should invoke computation functions (e.g., `computeCurveFit()`, `computePlotFromCoords()`) exposed by the graphic module, avoiding manual calculations by the user.

#### 5.17.21.1.11 – Normal orientation for labels
*Summary (shall):* Text on dynamic graphic displays shall remain upright when the displayed image rotates.

- **IMPL-5.17.21.1.11-01** `[CSS][JS]` `[Main]` Text labels shall apply a counter-rotation (`transform: rotate(calc(-1 * var(--map-rotation)))`) synchronized with the rotation of the map/image, so as to always remain upright.

#### 5.17.21.1.12 – Display of scale
*Summary (shall):* If a graphic/map display has zoom functionality, the current zoom level or scale shall be displayed.

- **IMPL-5.17.21.1.12-01** `[HTML][JS]` `[Main]` A `#zoom-scale-indicator` indicator shall show the current zoom level or scale (e.g., "1:50 000"), updated by `state.js` on every zoom change.

#### 5.17.21.1.13 – Display accuracy
*Summary (shall, if applicable):* If applicable, the current display accuracy shall be displayed.

- **IMPL-5.17.21.1.13-01** `[HTML][JS]` `[Main]` Where relevant, `#display-accuracy-indicator` shall show the current accuracy (e.g., ± m), the value provided by `state.js`/data source.

#### 5.17.21.1.14 – Interpolation
*Summary (shall):* The system shall provide accurate numeric information for any point on the graphic display upon user request.

- **IMPL-5.17.21.1.14-01** `[JS]` `[Main]` A `pointerup` handler on explicit request (e.g., a "read value" button) shall invoke `interpolateValueAt(x, y)` and show the result in a tooltip/`#aux-region`.

#### 5.17.21.1.15 – Unobtrusive grids
*Summary (shall):* Displayed grid lines shall be unobtrusive.

- **IMPL-5.17.21.1.15-01** `[CSS]` `[Main]` Grid lines shall use `.grid-line` with low contrast/opacity (`opacity: 0.25`–`0.4`) relative to the data, without competing visually with it.

#### 5.17.21.1.16 – Obscuration of data elements
*Summary (shall):* Displayed grid lines shall not obscure data elements.

- **IMPL-5.17.21.1.16-01** `[CSS]` `[Main]` The z-level of grids (`.grid-line`) shall always be lower (`z-index`/draw order) than that of data elements, so as not to cover them.

#### 5.17.21.1.17 – Grid line suppression
*Summary (shall):* The user shall be able to suppress or display grid lines.

- **IMPL-5.17.21.1.17-01** `[HTML][JS]` `[Main]` A `#toggle-grid` control (`aria-pressed`) shall toggle the visibility of `.grid-line` via `prefs.js`, persisting the choice.

#### 5.17.21.2 – Graphs and charts
*Summary (shall):* Graphs and charts shall comply with the criteria of 5.17.21.2.1–5.17.21.2.6 (introduction, no standalone implementation requirement beyond the following ones).

- **IMPL-5.17.21.2-01** `[Global]` No dedicated implementation requirement; see `IMPL-5.17.21.2.1-01` … `IMPL-5.17.21.2.6-01` for the specific criteria.

#### 5.17.21.2.1 – Graph axes
*Summary (shall):* The axes of graphs shall be labeled.

- **IMPL-5.17.21.2.1-01** `[HTML][CSS]` `[Main]` Every chart in `#content-region` shall include `<text>`/`<figcaption>` labels for the X and Y axes with units of measurement, class `.chart-axis-label`.

#### 5.17.21.2.2 – Trend lines
*Summary (should):* When multiple trend lines shall be compared, they should be shown on a single chart.

- **IMPL-5.17.21.2.2-01** `[JS]` `[Main]` The chart component should support rendering multiple overlapping series (`series[]`) on a single `<canvas>`/SVG, with a distinctive `.chart-legend` legend by color/dash pattern.

#### 5.17.21.2.3 – Reference index
*Summary (shall):* When the user shall compare graphic data with a significant level or critical value, that level/value shall be included in the display.

- **IMPL-5.17.21.2.3-01** `[JS][CSS]` `[Main]` Charts shall be able to draw a `.chart-threshold-line` reference line (value from `config.js` or data) with a distinct style (dash pattern, `--color-warning`/`--color-critical` color).

#### 5.17.21.2.4 – Data annotation
*Summary (shall):* When precise reading of a graphic display may be required, it shall be possible to supplement the graphic representation with the represented numeric values.

- **IMPL-5.17.21.2.4-01** `[HTML][JS]` `[Main]` A control `#toggle-data-labels` (`aria-pressed`) shall show/hide numeric labels `.chart-data-label` on the chart's data points.

#### 5.17.21.2.5 – Consistent scaling
*Summary (shall):* When users shall compare graphic data across a series of charts, the same scale shall be used for each chart.

- **IMPL-5.17.21.2.5-01** `[JS]` `[Main]` The multi-instance chart component shall compute and apply a common domain/scale (`sharedScale` in `state.js`) to all comparable charts in the same view.

#### 5.17.21.2.6 – Single scale only
*Summary (shall):* In charts, only a single scale shall be shown per axis, not different scales for different curves in the same chart.

- **IMPL-5.17.21.2.6-01** `[HTML][CSS]` `[Main]` Each chart axis shall render a single scale label (`.chart-axis-label`); series with different units require separate charts or explicitly labeled secondary axes, never overlapping ambiguous scales.

#### 5.17.21.3.1 – Drawing shapes
*Summary (shall):* When shape drawing is required, the user shall be provided with aids for drawing shapes.

- **IMPL-5.17.21.3.1-01** `[HTML][JS]` `[Main]` A toolbar `#drawing-tools` shall offer aids for predefined shapes (rectangle, circle, polygon) with snapping via `pointermove`/`pointerup`.

#### 5.17.21.3.2 – Drawing lines
*Summary (shall):* When line drawing is required, the user shall be provided with aids for drawing straight line segments.

- **IMPL-5.17.21.3.2-01** `[HTML][JS]` `[Main]` `#drawing-tools` shall include a straight-line tool with a real-time preview during `pointermove` and confirmation on `pointerup`.

#### 5.17.21.3.3 – Drawing intersecting lines
*Summary (should):* When a user draws the end of a line segment near a displayed element, the system should automatically connect the segment to the element.

- **IMPL-5.17.21.3.3-01** `[JS]` `[Main]` During drawing, a snapping algorithm should automatically connect the segment's endpoint to elements within a tolerance (`config.js: snapToleranceMm` converted using `--px-per-mm`).

#### 5.17.21.3.4 – Override
*Summary (shall):* The user shall be provided a means to disable the automatic line segment connection feature.

- **IMPL-5.17.21.3.4-01** `[HTML][JS]` `[Main]` A control `#toggle-snap` (`aria-pressed`) shall allow temporarily disabling the automatic snapping referred to in `IMPL-5.17.21.3.3-01`.

#### 5.17.21.3.5 – Drawing lines and figures with numeric coordinates
*Summary (shall):* When lines/figures shall represent numeric coordinates, computer aids shall include methods for entering the coordinates and selecting the appropriate units.

- **IMPL-5.17.21.3.5-01** `[HTML][JS]` `[Main]` A form `#coord-entry-form` with validated numeric fields (`validate.js`) and a unit `<select>` (degrees, meters, MGRS) shall allow creating/placing figures for exact coordinates.

#### 5.17.22.1.1 – Changing values
*Summary (shall):* Alphanumeric values that change in real time shall be updated according to the criteria in 5.17.22.1.1.1–5.17.22.1.1.2 (introduction).

- **IMPL-5.17.22.1.1-01** `[Global]` No standalone implementation requirement; see `IMPL-5.17.22.1.1.1-01` and `IMPL-5.17.22.1.1.2-01`.

#### 5.17.22.1.1.1 – Reliable reading
*Summary (shall):* Values that the user shall reliably read shall not be updated more than once per second.

- **IMPL-5.17.22.1.1.1-01** `[JS]` `[Main]` "Reliable reading" value displays (class `.value-readable`) shall limit the DOM update rate to ≤1 Hz via throttling in `state.js`/`net.js`, even if the source data arrives more often.

#### 5.17.22.1.1.2 – Identify rate of change
*Summary (shall):* Values used to identify rate of change or read gross values shall be updated 3–4 times per second.

- **IMPL-5.17.22.1.1.2-01** `[JS]` `[Main]` Trend displays (class `.value-trend`) shall update at a design-configured frequency `config.js: trendUpdateHz` (default 3.5 Hz, range 3–4 Hz).

#### 5.17.22.1.2 – Update rate
*Summary (should):* Update rate should be in accordance with program-specific requirements.

- **IMPL-5.17.22.1.2-01** `[JS]` `[Global]` All dynamic display refresh rates shall be centralized in `config.js` (`refreshRates`), configurable per program/view without code changes.

#### 5.17.22.1.2.1 – Display freeze
*Summary (shall):* A display freeze mode shall be provided to view any selected frame that is updated/advanced automatically by the system.

- **IMPL-5.17.22.1.2.1-01** `[HTML][JS]` `[Main]` A control `#btn-freeze-display` (`aria-pressed`) shall suspend automatic updating of the dynamic display (`state.js: isFrozen`) while keeping the current frame visible.

#### 5.17.22.1.2.2 – Resume from freeze
*Summary (shall):* A single-action option shall be provided to resume from the freeze point or from the current real-time point.

- **IMPL-5.17.22.1.2.2-01** `[HTML][JS]` `[Main]` Two single-action controls, `#btn-resume-freeze-point` and `#btn-resume-realtime`, shall resume from the frozen frame or from the current live point, respectively.

#### 5.17.22.1.2.3 – Display freeze prohibition
*Summary (shall):* The user shall be prevented from entering freeze mode when doing so would have an adverse effect on the mission being performed by the system.

- **IMPL-5.17.22.1.2.3-01** `[JS]` `[Main]` `#btn-freeze-display` shall be disabled (`disabled`/`aria-disabled="true"`) when `state.js` reports a critical operating condition incompatible with freeze (design value in `config.js`).

#### 5.17.22.1.2.4 – Freeze feedback
*Summary (shall):* A label shall remind the user when the display is in freeze mode.

- **IMPL-5.17.22.1.2.4-01** `[HTML][CSS]` `[Main]` When `state.js: isFrozen` is true, a persistent label `.freeze-label` (e.g. "FROZEN") shall appear overlaid on the affected display, with contrast conforming to `IMPL-5.2.2.7-01`.

#### 5.17.22.1.2.5 – Timeliness
*Summary (shall):* Displays requiring refreshed information (e.g., head-up displays) shall be updated synchronously.

- **IMPL-5.17.22.1.2.5-01** `[JS]` `[Main]` Synchronous displays shall update via a single `requestAnimationFrame`/timer loop centralized in `state.js`, avoiding independent asynchronous updates among related components.

#### 5.17.22.1.2.6 – Advisory and alerting
*Summary (shall):* Devices displaying simultaneous and integrated information (e.g., multifunction displays) shall alert/cue operating personnel to information before it becomes critical on the display.

- **IMPL-5.17.22.1.2.6-01** `[JS]` `[Main]` `notify.js` shall monitor the thresholds of displayed values and generate an early advisory (class `.is-advisory`/`--color-advisory`) before a value reaches the critical threshold configured in `config.js`.

#### 5.17.22.2.1 – Hard copy
*Summary (shall):* The system shall provide the capability for the user to obtain a paper copy of the exact contents of the alphanumeric or digital graphic display.

- **IMPL-5.17.22.2.1-01** `[HTML][JS]` `[Main]` A control `#btn-print-display` shall invoke `window.print()` on a dedicated `@media print` stylesheet that faithfully reproduces the current display content (data, not just a screenshot).

#### 5.17.22.2.2 – Display print
*Summary (shall):* The system shall provide the capability for the user to print a screen by simple request (e.g., PRINT-SCREEN).

- **IMPL-5.17.22.2.2-01** `[HTML][JS]` `[Main]` `#function-keys` shall include a single-action "Print screen" function key, mapped to `#btn-print-display` (see `IMPL-5.17.22.2.1-01`).

#### 5.17.22.2.3 – Print page
*Summary (shall):* The system shall provide the capability to request printing of a single page or sequence of pages by specifying page numbers, or to print all pages without specifying them.

- **IMPL-5.17.22.2.3-01** `[HTML][JS]` `[Main]` The `#print-options-dialog` dialog (built on `<dialog>`, managed by `dialog.js`) shall include a `page-range` field (e.g., "1-3,5") and an "All pages" option.

#### 5.17.22.2.4 – Text displayed as printed
*Summary (shall):* The system shall allow displaying text as it will be printed, including underline, bold, superscript, subscript, special characters/symbols, and different styles/sizes.

- **IMPL-5.17.22.2.4-01** `[CSS][JS]` `[Main]` A "print preview" view (`.print-preview`) shall apply the same `@media print` styles used for printing (bold, italic, superscript/subscript, sizes) before the actual print job is sent.

#### 5.17.22.2.5 – Flexible printing options
*Summary (shall):* When printing text, the system shall allow choosing among the available output formats (line spacing, font size, margins, headers, footers) and specifying which pages to print.

- **IMPL-5.17.22.2.5-01** `[HTML][JS]` `[Main]` `#print-options-dialog` shall include controls for line spacing, font size, margins, and header/footer, applied to the `@media print` stylesheet before printing.

#### 5.17.22.3.1 – Functional integration
*Summary (shall):* Data transmission functions shall be integrated with the other information handling functions, so that the user transmits data using the same system/procedures used for general entry, display, and processing.

- **IMPL-5.17.22.3.1-01** `[JS]` `[Global]` Message transmission (`net.js: sendMessage()`) shall reuse the same data entry and display views/forms of the application, without a separate subsystem with inconsistent UI.

#### 5.17.22.3.2 – Consistent procedures
*Summary (shall):* Procedures for preparing, sending, and receiving data/messages shall be consistent from one transaction to another and with the procedures of other information handling tasks.

- **IMPL-5.17.22.3.2-01** `[HTML][CSS]` `[Main]` The message composition form (`#message-compose-form`) shall follow the same layout, validation (`validate.js`), and confirmation (`dialog.js`) patterns as the other application forms.

#### 5.17.22.3.3 – Minimal memory load on users
*Summary (shall):* The system shall provide computer aids for automatically inserting standard information (e.g., headers, distribution lists) to minimize the user's memory load.

- **IMPL-5.17.22.3.3-01** `[JS]` `[Main]` `#message-compose-form` shall automatically prefill header, sender, and default distribution list from `state.js`/`config.js`, editable but not requiring manual re-entry.

#### 5.17.22.3.4 – Messages
*Summary (shall):* Messages shall comply with the criteria in 5.17.22.3.4.1–5.17.22.3.4.8 (introduction).

- **IMPL-5.17.22.3.4-01** `[Global]` No standalone implementation requirement; see `IMPL-5.17.22.3.4.1-01` … `IMPL-5.17.22.3.4.8-01`.

#### 5.17.22.3.4.1 – Stored message forms
*Summary (shall):* Where message formats conform to a standard or are predictable, pre-loaded forms shall be provided to aid preparation.

- **IMPL-5.17.22.3.4.1-01** `[HTML][JS]` `[Main]` A selector `#message-template-select` shall preload standard forms (`config.js: messageTemplates`) into `#message-compose-form`.

#### 5.17.22.3.4.2 – Incorporate existing files
*Summary (shall):* The user shall be able to incorporate an existing data file into a message, combine several files into a single message, and combine stored data with new data.

- **IMPL-5.17.22.3.4.2-01** `[HTML][JS]` `[Main]` `#message-compose-form` shall include an attachment control (`<input type="file" multiple>`) allowing existing files to be combined with the message text via `net.js`.

#### 5.17.22.3.4.3 – Reenter data
*Summary (shall):* It shall not be necessary to reenter data already entered for other purposes.

- **IMPL-5.17.22.3.4.3-01** `[JS]` `[Main]` The composition form shall read from `state.js` the fields already available (e.g., coordinates, station ID) and populate them automatically instead of requiring them to be typed again.

#### 5.17.22.3.4.4 – Interrupt
*Summary (shall):* The user shall be able to interrupt message preparation, review, or disposition and resume from where it was interrupted.

- **IMPL-5.17.22.3.4.4-01** `[JS]` `[Main]` The message draft state shall be saved automatically (`state.js: draftMessage`, debounced) so that closing and reopening `#message-compose-form` resumes from the point of interruption.

#### 5.17.22.3.4.5 – Data group labels
*Summary (shall):* Each data group or message shall contain a descriptive title, phrase, or word designating its content.

- **IMPL-5.17.22.3.4.5-01** `[HTML]` `[Main]` Each message/data group shall have a mandatory `subject`/`title` field (validated by `validate.js`) displayed as a heading in message lists.

#### 5.17.22.3.4.6 – Location of message labels
*Summary (shall):* Labels shall be consistently positioned adjacent (preferably above or to the left) to the data group/message they describe.

- **IMPL-5.17.22.3.4.6-01** `[CSS]` `[Main]` Message field labels shall use a consistent layout `label { grid-column: 1; }` positioned above or to the left of the related control throughout the application.

#### 5.17.22.3.4.7 – Emergency or critical messages
*Summary (shall):* Labels shall use an accentuation technique different and easily distinguishable from the one used to highlight/code emergency or critical messages.

- **IMPL-5.17.22.3.4.7-01** `[CSS]` `[Main]` Labels for ordinary messages (`.message-label`) shall have a distinct style (weight, color) from `.message-label--critical` used for emergency messages, avoiding visual ambiguity.

#### 5.17.22.3.4.8 – Originator identification
*Summary (shall, except broadcast):* Except for broadcast communication systems, the sender of each message shall be automatically identified, if possible.

- **IMPL-5.17.22.3.4.8-01** `[JS]` `[Main]` `net.js` shall automatically attach the current user identity (`state.js: currentUser`) to every message sent via `#message-compose-form`, except when the message type is marked as broadcast.

#### 5.17.22.3.5 – Addresses
*Summary (shall):* When the user shall specify the address for messages, prompting shall be provided to guide the user through the process.

- **IMPL-5.17.22.3.5-01** `[HTML][JS]` `[Main]` The recipient field shall use `<input list="address-directory">`/combobox with real-time suggestions (`validate.js`) as the user types.

#### 5.17.22.3.6 – Directories
*Summary (shall):* The user shall be provided with a built-in directory showing all acceptable forms of message addressing for each destination in the system and for links to external systems.

- **IMPL-5.17.22.3.6-01** `[HTML][JS]` `[Main]` An `#address-directory` view shall list all valid destinations (internal and external) with their accepted address forms, fed by `net.js`.

#### 5.17.22.3.6.1 – Computer aids
*Summary (shall):* Computer aids shall be provided to search a directory by specifying a complete or partial name.

- **IMPL-5.17.22.3.6.1-01** `[JS]` `[Main]` `#address-directory` shall offer a search field with incremental filtering (complete/partial name) on `input`, without an explicit submit.

#### 5.17.22.3.6.2 – Extract addresses
*Summary (shall):* The user shall be able to extract selected addresses from a directory for direct insertion into a header, in order to specify a message's destinations.

- **IMPL-5.17.22.3.6.2-01** `[JS]` `[Main]` Selecting one or more entries in `#address-directory` shall offer an "Add to recipients" action that inserts the address into the recipient field of `#message-compose-form`.

#### 5.17.22.4.1 – Use
*Summary (shall):* Geographic displays shall be used only for data that can be geo-referenced.

- **IMPL-5.17.22.4.1-01** `[JS]` `[Main]` The map component (`#geo-display`) shall be rendered only for datasets that include valid geographic coordinates (`validate.js`), as a verifiable architectural criterion checked by code inspection.

#### 5.17.22.4.2 – Types
*Summary:* Editable geographic coordinate displays allow entry in one of several systems (decimal, MGRS, DMS); non-editable displays are system-generated values in the selected coordinate format.

- **IMPL-5.17.22.4.2-01** `[HTML][JS]` `[Main]` An editable coordinate field `#geo-coord-input` shall accept and validate (`validate.js`) decimal, MGRS, and DMS formats, selectable via `<select name="coord-system">`.
- **IMPL-5.17.22.4.2-02** `[JS]` `[Main]` Read-only displays (`.geo-coord-readonly`) shall format system-generated values in the coordinate format currently selected by the user, without allowing direct editing.

#### 5.17.22.4.3 – Default geographic coordinate systems
*Summary (should):* Geospatial displays should have the option to save a default geographic coordinate system (e.g., decimal, MGRS, DMS).

- **IMPL-5.17.22.4.3-01** `[JS]` `[Main]` `prefs.js` should store the `defaultCoordSystem` chosen by the user and apply it as the preselection in `#geo-coord-input` and in read-only displays.

#### 5.17.22.4.4 – Geospatial display options
*Summary (should):* For customization/decluttering, a list of options on visual elements should be available: a. toggleable visibility; b. opacity; c. stacking precedence; d. hierarchical grouping.

- **IMPL-5.17.22.4.4-01** `[HTML][JS]` `[Main]` A panel `#geo-layers-panel` in `#aux-region` shall list the geospatial display's overlays/elements with `aria-pressed` controls to show/hide (point a).
- **IMPL-5.17.22.4.4-02** `[HTML][JS]` `[Main]` Each entry in `#geo-layers-panel` shall include an opacity slider (`<input type="range">`) (point b).
- **IMPL-5.17.22.4.4-03** `[HTML][JS]` `[Main]` `#geo-layers-panel` shall support reordering by precedence (drag or up/down buttons) that updates the rendering `z-order` (point c).
- **IMPL-5.17.22.4.4-04** `[HTML][JS]` `[Main]` `#geo-layers-panel` shall support hierarchical grouping of elements/layers, applying show/hide/opacity/precedence to the group as well (point d).

#### 5.17.22.4.5 – Geospatial display drawings
*Summary (should):* If the user can draw on the geospatial display: a. manipulation (move, resize with lockable aspect ratio, delete); b. zoom/pan during drawing; c. editing of drawing properties (color, opacity, labeling) before and after drawing.

- **IMPL-5.17.22.4.5-01** `[JS]` `[Main]` Drawings on `#geo-display` should be movable, resizable (with a "lock proportions" option), and deletable after placement (point a), reusing `IMPL-5.17.21.1.8-01`.
- **IMPL-5.17.22.4.5-02** `[JS]` `[Main]` While drawing on `#geo-display`, it shall remain possible to zoom/pan the underlying map without interrupting the active drawing tool (point b).
- **IMPL-5.17.22.4.5-03** `[HTML][JS]` `[Main]` A panel `#drawing-props-panel` should allow editing the drawing's color, opacity, and label both before and after its creation (point c).

#### 5.17.22.4.6 – Relative positioning
*Summary (should):* Drawings should be able to be aligned relative to other visual elements, such as other drawings or a grid overlay.

- **IMPL-5.17.22.4.6-01** `[JS]` `[Main]` The drawing tool on `#geo-display` should offer alignment snapping (edges, center, grid) relative to other visual elements, with tolerance configurable in `config.js`.

### 3.13 Display legibility, contrast and small-screen adaptation

Requirements on the visual rendering of the web HMI (contrast, polarity, user display adjustments, small-screen adaptation), applied via the `data-theme` themes and the `--contrast-*`, `--font-family-ui` tokens of §2.4, and managed by `prefs.js`.

#### 5.2.1.2.3 – Luminance control
*Summary (shall):* Luminance shall be adjustable so that display content is visible and usable over the entire expected range of ambient illumination.

- **IMPL-5.2.1.2.3-01** `[HTML][JS]` `[Global]` A control `#brightness-control` (slider) in the display settings panel shall adjust a CSS variable `--display-brightness` applied as `filter: brightness()` on the document, persisted by `prefs.js`.

#### 5.2.1.3 – Display contrast
*Summary (shall):* Display contrast shall be such that content is visible over the entire expected range of ambient illumination.

- **IMPL-5.2.1.3-01** `[CSS][JS]` `[Global]` The `theme-high-contrast` theme (token `--contrast-*`) shall be selectable from `#btn-display-settings` to ensure visibility under strong ambient illumination or glare conditions.

#### 5.2.2.6 – Image polarity
*Summary (shall):* To maintain dark adaptation, displays shall use light characters on a dark background when ambient illumination is < ~0.1 lx; otherwise dark characters on a light background.

- **IMPL-5.2.2.6-01** `[CSS][JS]` `[Global]` The `theme-light`/`theme-dark` themes (`data-theme` attribute on `<html>`) shall implement dark-on-light and light-on-dark polarity respectively; `prefs.js` shall allow manual selection and, if an ambient light sensor is available, propose the `dark` theme below the 0.1 lx threshold.

#### 5.2.2.7 – Contrast ratio
*Summary (shall):* The contrast ratio between characters and background shall be ≥6:1 (preferably ≥10:1); c. a control shall be provided to vary the contrast between characters and background.

- **IMPL-5.2.2.7-01** `[CSS]` `[Global]` All text/background combinations defined in the `--color-*` tokens shall guarantee a contrast ratio ≥6:1 (target ≥10:1), verified by inspection/automated test (contrast checker) on each theme.
- **IMPL-5.2.2.7-02** `[HTML][JS]` `[Global]` A control `#contrast-control` (slider or predefined selection) in the display settings panel shall allow the user to vary text/background contrast, applying the `--contrast-level` variable and persisting the choice via `prefs.js` (point c).

#### 5.2.2.10.2 – Adapt formatting
*Summary (should):* When converting a display from traditional size to a small screen, the design should adapt (not merely copy) the current system design.

- **IMPL-5.2.2.10.2-01** `[CSS]` `[Global]` At the **S** breakpoint (800 px, §2.3) the layout shall reorganize content (single column, pop-up panels for `#aux-region`) instead of proportionally shrinking the desktop layout with `transform: scale()`.

#### 5.2.2.10.3 – Font characteristics
*Summary (should/shall):* Font size should be large enough to discriminate similar characters (e.g., "I" and "1", "Z" and "2") and shall conform to the requirements of 5.17.18.2.

- **IMPL-5.2.2.10.3-01** `[CSS]` `[Global]` The `--font-family-ui` font shall guarantee distinguishable glyphs for similar characters (I/1, Z/2, O/0); the minimum size shall comply with `--fs-body`/`--fs-small` as per `IMPL-5.17.18.2-*` (see the section dedicated to character size).

#### 5.2.2.10.3.1 – Standard font
*Summary (should):* A common standard font should be used (e.g., Arial, Times New Roman, Courier, or Verdana).

- **IMPL-5.2.2.10.3.1-01** `[CSS]` `[Global]` `--font-family-ui` shall use a standard font stack (`Arial, Verdana, Helvetica, sans-serif`), consistent throughout the application without unjustified local exceptions.

#### 5.2.2.10.3.2 – Sans serif
*Summary (shall):* Where users shall read displays quickly under adverse conditions (e.g., poor lighting), a sans serif style shall be used (e.g., Arial, Verdana, or Helvetica).

- **IMPL-5.2.2.10.3.2-01** `[CSS]` `[Global]` `--font-family-ui` shall remain sans-serif (`Arial, Verdana, Helvetica`) for all operational interface text, without introducing serif families in primary displays.

#### 5.2.2.10.4 – Text organization
*Summary (shall):* To increase the ability to skim text quickly, subtitles and hypertext shall be used in text passages longer than a single screen.

- **IMPL-5.2.2.10.4-01** `[HTML][CSS]` `[Main]` Text content longer than one screen (e.g., help, documentation in `#aux-region`) shall use subtitles (`<h2>`/`<h3>`) and internal hyperlinks (`<a href="#...">`) for quick navigation.

#### 5.2.2.10.4.1 – Variable length lines
*Summary (shall):* Variable length lines shall be avoided through hyphenation of words at line breaks, to improve legibility on small screens.

- **IMPL-5.2.2.10.4.1-01** `[CSS]` `[Global]` Text blocks shall apply `hyphens: auto; overflow-wrap: break-word;` with `lang` set correctly (for hyphenation rules), especially at the **S**/**M** breakpoints.

#### 5.2.2.10.4.2 – Scrolling markers
*Summary (shall):* When content cannot be displayed on a single screen, scrolling markers shall be provided to let the user identify their position on the page.

- **IMPL-5.2.2.10.4.2-01** `[CSS][JS]` `[Main]` Scrollable areas (`#main-panel`, `#aux-region`) shall show scroll position indicators (e.g., styled persistent scrollbar, `.scroll-progress`, or overflow shadows) updated dynamically during scrolling.

### 3.14 Automation and supervisory control

Applies when the web HMI supervises or controls automated functions. General process/principle requirements are translated into architectural constraints on `state.js` (automation-level state machine), `notify.js`/`feedback.js` (state-change signaling and feedback), and `dialog.js` (confirmations, overrides, decision-aid explanations).

#### 5.12.3.1.2.1 – Performance improvement
*Summary (shall):* Functions shall be automated only if they improve user/system performance, safety, or workload.

- **IMPL-5.12.3.1.2.1-01** `[JS]` `[Global]` Every automation feature registered in `config.js` (`automationFeatures`) shall include a non-empty `rationale` field (performance/safety/workload justification); an automated test (T) verifies the presence of this field for every active feature.

#### 5.12.3.1.2.2 – User focused
*Summary (shall):* Automation shall support the user where appropriate (human-centered) and not be introduced simply because it is available (technology-centered).

- **IMPL-5.12.3.1.2.2-01** `[JS]` `[Global]` `config.js` exposes a `humanCentered:boolean` flag for each `automationFeature`; a design review (acceptance criterion D) verifies that no feature is activated without documented human-centered justification.

#### 5.12.3.2.1 – User in command
*Summary (shall):* Automated systems shall not remove the user from the command role.

- **IMPL-5.12.3.2.1-01** `[JS]` `[Global]` `state.js` maintains `controlMode` (`manual`/`supervised`/`auto`); no primary function shall be executable only in `auto` without an equivalent manual path reachable from `#function-keys`.
- **IMPL-5.12.3.2.1-02** `[HTML]` `[Footer]` The corresponding manual control always remains present in the DOM (not `display:none`) even when automation is active, possibly disabled with `[aria-disabled]` and a tooltip explaining why.

#### 5.12.3.2.2 – User involvement
*Summary (shall):* The user shall have an active role through relevant and meaningful tasks, regardless of the automation level.

- **IMPL-5.12.3.2.2-01** `[JS]` `[Main]` `#aux-region` presents a list of pending supervisory tasks (confirmations, checks) generated by `state.js`, updated independently of the current automation level.

#### 5.12.3.2.3 – Situation awareness
*Summary (shall):* The system shall provide the user with awareness of system state (position, status), capability (health, readiness) and, where possible, a projection of future state in a timely manner.

- **IMPL-5.12.3.2.3-01** `[JS]` `[Header][Main]` `state.js` exposes `systemState` (status, capability, readiness); `#mode-indicator` and `#content-region` reflect it with updates within the times of Table V (local ≤0.5 s, from host ≤2.0 s) via `net.js`.
- **IMPL-5.12.3.2.3-02** `[JS]` `[Main]` Optional projection panel in `#aux-region` (see 5.12.3.2.3.3).

#### 5.12.3.2.3.1 – System state parameters
*Summary (shall):* The system shall provide state parameters appropriate to the user's current role and task.

- **IMPL-5.12.3.2.3.1-01** `[JS]` `[Main]` `state.js` filters the set of parameters rendered in `#content-region` based on `state.currentUser.role`, defined in `config.js` (`roleParameterSets`).

#### 5.12.3.2.3.2 – Current situation
*Summary (shall):* The system shall provide the current situation within the functional and mission context appropriate to the role/task.

- **IMPL-5.12.3.2.3.2-01** `[HTML][JS]` `[Main]` `#content-region` includes a contextual panel bound to `state.missionContext`, updated by `net.js`; content selected based on role and active task.

#### 5.12.3.2.3.3 – Future system states
*Summary (should):* The system should provide a projection of likely future states based on the current situation.

- **IMPL-5.12.3.2.3.3-01** `[JS]` `[Main]` "Predicted state" widget in `#aux-region`, enabled from `config.js` (`enablePredictivePanel`), fed by `state.js`/`net.js`.

#### 5.12.3.2.4 – Predictability
*Summary (shall):* Automated systems shall behave predictably so that the user understands the purpose of the automation and the effect of its operations.

- **IMPL-5.12.3.2.4-01** `[JS]` `[Global]` `notify.js` publishes a descriptive notification before/at the time of every relevant automatic action (what is about to happen/has happened); state transitions are deterministic and covered by automated tests (T).

#### 5.12.3.2.5 – Consistency
*Summary (shall):* The interaction of automated systems with users shall reflect a high degree of consistency within and between systems.

- **IMPL-5.12.3.2.5-01** `[CSS][JS]` `[Global]` Automation controls reuse the same patterns (classes, `--color-*` tokens, `dialog.js` components) across all views; a regression test (T) compares markup/ids between the different views with analogous functions.

#### 5.12.3.2.6 – Intuitive
*Summary (should):* Automated systems and their displays should be easy to understand, learn, use, and should meet user expectations.

- **IMPL-5.12.3.2.6-01** `[HTML][CSS]` `[Main]` Standard icons and labels (`i18n.js`) for automation controls, with tooltips/contextual help in `#aux-region`; acceptance criterion via demonstration (D) with representative users.

#### 5.12.3.2.7 – Input and setup
*Summary (shall):* Automated systems shall provide a way to verify the automation's configuration and the data used as input.

- **IMPL-5.12.3.2.7-01** `[HTML][JS]` `[Main]` `dialog.js` opens a "Verify configuration" window showing the automation's current parameters/inputs before activation, with an explicit confirmation action.

#### 5.12.3.2.9 – Multiple systems
*Summary (shall):* Designs with multiple systems shall meet the criteria in 5.12.3.2.9.1 and 5.12.3.2.9.2.

- **IMPL-5.12.3.2.9-01** `[JS]` `[Global]` Architectural constraint: all builds/platforms share the same set of components, tokens, and dictionary (see `IMPL-5.12.3.2.9.1-01`, `IMPL-5.12.3.2.9.2-01`); a configuration test verifies the absence of undocumented local variants.

#### 5.12.3.2.9.1 – Controls and displays
*Summary (shall):* Controls and displays shall be standardized across the different platforms controlled by the same users, including reassigned ones.

- **IMPL-5.12.3.2.9.1-01** `[HTML][CSS]` `[Global]` A shared component library (same `id`s/classes/tokens `--touch-target-min`, `--color-*`) reused across all platforms; a visual regression test compares the DOM between builds.

#### 5.12.3.2.9.2 – Terminology
*Summary (shall):* Terminology shall be standardized across the platforms controlled by the same users.

- **IMPL-5.12.3.2.9.2-01** `[JS]` `[Global]` A single terminology dictionary in `i18n.js` shared by all builds/platforms; the build step fails if a platform defines duplicate/divergent keys.

#### 5.12.3.2.10.1 – Recovery
*Summary (shall):* Automation shall maintain sufficient user engagement to allow safe recovery during emergencies or manual operation in case of automation failure.

- **IMPL-5.12.3.2.10.1-01** `[HTML][JS]` `[Main]` `#alert-region` shows a banner "Automation unavailable – switch to manual" with `role="alert"`; the equivalent manual command path remains always available in `#content-region`/`#function-keys`, managed by `state.js` (fallback).

#### 5.12.3.2.10.3 – Override capability
*Summary (shall):* The user shall be provided with the ability to cancel, shut down, or neutralize the automated system.

- **IMPL-5.12.3.2.10.3-01** `[HTML][CSS]` `[Footer]` Persistent "Override"/"Stop automation" button in `#function-keys`, sized ≥ `--touch-target-min`, always visible during automatic control.
- **IMPL-5.12.3.2.10.3-02** `[JS]` `[Global]` Activation requires confirmation (`dialog.js`), produces `feedback.js.acknowledge()`, and is logged by `log.js`.

#### 5.12.3.3.2 – High-risk actions or decisions
*Summary (shall):* For tasks with greater uncertainty/risk, automation shall not exceed the level of recommending a preferred decision/action.

- **IMPL-5.12.3.3.2-01** `[JS]` `[Main]` For tasks with `config.js riskLevel:'high'`, `state.js` constrains `automationLevel` to a maximum of `'recommend'`; execution always requires user confirmation via `dialog.js`, never automatic start.

#### 5.12.3.4.1 – Relationship with user task
*Summary (should):* The relationships among displays, controls, decision aids, information structure, and user tasks should be clear.

- **IMPL-5.12.3.4.1-01** `[HTML]` `[Main]` A common `data-task` attribute on related controls, indicators, and decision aids in `#content-region`/`#aux-region`, with an explicit visual/textual link in contextual help.

#### 5.12.3.4.2 – Clear roles and responsibilities
*Summary (shall):* The automated system shall clearly indicate whether it is up to the user or the system to perform a task at a given moment.

- **IMPL-5.12.3.4.2-01** `[HTML][CSS]` `[Header]` `#mode-indicator` shows, per task, a text+icon badge ("Manual"/"Automatic") according to `state.js taskOwner`, with dual color/icon/text coding.

#### 5.12.3.4.2.1 – Changes of state
*Summary (shall):* The interface shall clearly indicate all changes of state and automation level, especially in case of mode reversion due to failure or operator change.

- **IMPL-5.12.3.4.2.1-01** `[JS]` `[Header][Main]` Every change of `state.automationLevel` generates a toast (`notify.js`), a banner in `#alert-region` if caused by a failure, an audible signal `feedback.js.beep('mode-change')`, and an entry in `log.js`.

#### 5.12.3.4.2.2 – Appropriate to level and mode of control
*Summary (shall):* Information elements appropriate to the level/mode of control shall be made salient when function allocation or automation level changes.

- **IMPL-5.12.3.4.2.2-01** `[CSS][JS]` `[Main]` `#content-region` applies `.mode-manual`/`.mode-supervised`/`.mode-auto` classes that show/emphasize (color `--color-advisory`, order) the widgets relevant to the current level.

#### 5.12.3.4.2.3 – Return to manual control
*Summary (should):* When automation determines that the operator should return to manual control, the system should provide clear indications of the information and actions required.

- **IMPL-5.12.3.4.2.3-01** `[HTML][JS]` `[Main][Footer]` `#alert-region` banner "Manual control resumption required" with a bulleted list of required actions; dedicated "Take control" button in `#function-keys`.

#### 5.12.3.4.3 – Continuous feedback
*Summary (should):* The system should provide an appropriate level of feedback on control, status, and output under normal and malfunction conditions.

- **IMPL-5.12.3.4.3-01** `[JS]` `[Footer]` `#status-line` (`role="status"`) continuously updated by `feedback.js.setStatus()`; malfunctions generate a priority notification via `notify.js` in addition to the normal status update.

#### 5.12.3.4.4 – System response
*Summary (shall):* Automated system responses to user commands shall be brief and unambiguous, consistent with the expected response times.

- **IMPL-5.12.3.4.4-01** `[JS]` `[Footer]` Automatic responses rendered in `#message-line` with brief text (max ~80 characters) within the times of Table V (echo ≤0.2 s, local update ≤0.5 s).

#### 5.12.3.4.5 – Visualize consequences of decisions
*Summary (should):* The automated system should provide information to visualize the expected outcome of a decision, whether human or automatic.

- **IMPL-5.12.3.4.5-01** `[HTML][JS]` `[Main]` "Expected outcome" panel in `#aux-region`/`dialog.js` shown before confirming a decision action, with a summary of the expected effect.

#### 5.12.3.4.6 – Consistency
*Summary (should):* The behavior of automated systems should be consistent with users' expectations and understanding.

- **IMPL-5.12.3.4.6-01** `[JS]` `[Global]` A behavioral regression test (T) compares the outcomes of the same automation actions in different contexts, verifying behavior invariance for equal inputs.

#### 5.12.3.4.7 – Organization
*Summary (should):* Automation interfaces should be organized to match expectations, task processes, and system capabilities.

- **IMPL-5.12.3.4.7-01** `[HTML][JS]` `[Main]` `#nav-region` groups automation controls by task (`nav.js taskGroups`), not by technical subsystem.

#### 5.12.3.4.10 – Timeliness
*Summary (shall):* Dynamic information shall be presented in real time and on demand to ensure accurate and timely decisions.

- **IMPL-5.12.3.4.10-01** `[JS]` `[Main]` `net.js` pushes updates (WebSocket) to `#content-region` within the times of Table V (local ≤0.5 s, from host ≤2.0 s); a "Refresh" button for explicit requests is always available.

#### 5.12.3.4.11 – Error checking
*Summary (should):* Displays and data entry devices should provide a means to cross-check the accuracy of manually entered data.

- **IMPL-5.12.3.4.11-01** `[JS]` `[Main]` `validate.js` exposes a cross-check function that compares the entered value against an expected range/secondary source, setting `[aria-invalid]` and a message in case of discrepancy.

#### 5.12.3.4.12 – Automation modes
*Summary (shall):* Modes shall meet the criteria in 5.12.3.4.12.1 through 5.12.3.4.12.7.

- **IMPL-5.12.3.4.12-01** `[JS]` `[Global]` `state.js` defines a closed, enumerated list of permitted modes (`config.js automationModes`); the interface cannot generate/display modes not present in the list (constraint verifiable by inspection I).

#### 5.12.3.4.12.1 – Identification
*Summary (shall):* When control/display/automation functions change between modes, the system shall provide a clear indication of the change, current mode, function identification, and status.

- **IMPL-5.12.3.4.12.1-01** `[HTML][JS]` `[Header]` `#mode-indicator` with `aria-live="polite"` updates text+icon+function name+status on every mode change.

#### 5.12.3.4.12.2 – Accessibility
*Summary (should):* Modes used more frequently should be more accessible than those used rarely.

- **IMPL-5.12.3.4.12.2-01** `[JS]` `[Footer]` `prefs.js` tracks mode usage frequency and reorders the quick controls in `#function-keys` accordingly.

#### 5.12.3.4.12.3 – Number of modes
*Summary (should):* The number of different modes for a given system should be minimized.

- **IMPL-5.12.3.4.12.3-01** `[JS]` `[Global]` Design constraint in `config.js` (`automationModes.length` ≤ design value, e.g. 5); reviewed at design stage (criterion I).

#### 5.12.3.4.12.4 – Switching
*Summary (shall):* The user shall be able to switch easily between modes.

- **IMPL-5.12.3.4.12.4-01** `[HTML][CSS]` `[Header]` Mode selector (`<select id="mode-switch">` or button group) always visible in `#app-header`, size ≥ `--touch-target-min`, single-step action.

#### 5.12.3.4.12.5 – Consistent
*Summary (shall):* Features and functions common across display modes shall be consistent.

- **IMPL-5.12.3.4.12.5-01** `[CSS][HTML]` `[Main]` Controls common across the `.mode-*` variants share the same markup/ids/classes; only state/visibility changes via CSS, never the structure.

#### 5.12.3.4.12.6 – Alerts
*Summary (should):* The automated system should alert the user when a mode may be potentially hazardous.

- **IMPL-5.12.3.4.12.6-01** `[JS]` `[Global]` Modes flagged `hazardous:true` in `config.js` generate a `notify.js` notification with `warning` priority on selection, before activation.

#### 5.12.3.4.12.7 – Inadvertent activation
*Summary (should):* The automated system should safeguard against inadvertent activation of a potentially unsafe mode.

- **IMPL-5.12.3.4.12.7-01** `[JS]` `[Main]` Switching to a `hazardous:true` mode requires explicit confirmation (`dialog.js`), consistent with the anti-inadvertent-activation pattern of `IMPL-5.19.1.9.1-01`.

#### 5.12.3.4.13 – Monitoring functions
*Summary (should):* The system should actively involve users in control and monitoring of automated systems, including status and trend information.

- **IMPL-5.12.3.4.13-01** `[HTML][JS]` `[Main]` `#aux-region` includes a status/trend panel always rendered (not hidden in full-auto mode), fed by `state.js`.

#### 5.12.3.4.13.1 – Changing data
*Summary (should):* Changing data that shall be monitored should be displayed in graphic format.

- **IMPL-5.12.3.4.13.1-01** `[HTML][CSS]` `[Main]` Monitored dynamic values rendered as SVG sparklines/gauges in `#content-region` rather than numeric text alone.

#### 5.12.3.4.13.3 – Multiple displays
*Summary (should):* When users shall monitor multiple displays, notification of important events should occur on the same physical display, in the same location.

- **IMPL-5.12.3.4.13.3-01** `[CSS]` `[Header][Main]` `#alert-summary` and `#alert-region` are fixed and consistent in position (same area/coordinates) across all views via `position: sticky`/a shared grid, a single centralized notification point.

#### 5.12.3.4.13.4 – Monitoring indication
*Summary (should):* Automated systems without incidents for long periods should provide an indication that the automation is still monitoring.

- **IMPL-5.12.3.4.13.4-01** `[JS]` `[Footer]` `#status-line` shows a "heartbeat" indicator (pulsing dot + timestamp of last check) updated periodically by `net.js`.

#### 5.12.3.4.13.5 – User interactions
*Summary (should):* Automated systems should be able to monitor user interactions and warn of errors.

- **IMPL-5.12.3.4.13.5-01** `[JS]` `[Global]` `validate.js`/`log.js` track repeated error patterns; `notify.js` warns the user beyond a threshold configured in `config.js`.

#### 5.12.3.4.14.4 – Feedback
*Summary (shall):* To promote situation awareness, the user shall be given immediate feedback to command and control orders.

- **IMPL-5.12.3.4.14.4-01** `[JS]` `[Global]` Every command sent to automation invokes `feedback.js.acknowledge()` (visual + haptic `navigator.vibrate`) within ≤0.1 s (Table V).

#### 5.12.3.4.14.6 – Override
*Summary (should):* When the user might need to operate in out-of-tolerance conditions, a deliberate override action should be provided.

- **IMPL-5.12.3.4.14.6-01** `[HTML][JS]` `[Main]` Distinct "Force override" control (`--color-warning` style), separate from the general override of `IMPL-5.12.3.2.10.3-01`, with mandatory confirmation via `dialog.js`.

#### 5.12.3.4.15.1 – Incomplete data
*Summary (shall):* The system shall indicate when data are incomplete, missing, unreliable, invalid, or when relying on backup data, with color coding where displayed graphically.

- **IMPL-5.12.3.4.15.1-01** `[HTML][CSS]` `[Main]` Affected cells/values receive the `.data-stale`/`.data-invalid` class, `--color-warning`/`--color-inactive` color, an icon, and `aria-label="unreliable data"` (triple coding).

#### 5.12.3.4.15.2 – Accurate status
*Summary (shall):* Information presented shall accurately reflect system/environment status so the user rapidly recognizes, understands, and projects outcomes relative to goals.

- **IMPL-5.12.3.4.15.2-01** `[JS]` `[Main]` `#content-region` is always bound to `state.js` as the single source of truth (no unsynchronized cached derived value); timestamp of the last update shown next to the value.

#### 5.12.3.4.15.3 – Information presentation
*Summary (should):* Both the content of information made available through automation and the ways it is presented should be consistent with task priorities.

- **IMPL-5.12.3.4.15.3-01** `[JS]` `[Main]` Layout order of `#content-region` determined by a priority list per task/role in `config.js`.

#### 5.12.3.4.15.4 – Cueing important information
*Summary (should):* When information shall be updated quickly, the most important information should be cued so it is processed first by the user.

- **IMPL-5.12.3.4.15.4-01** `[JS]` `[Main]` `notify.js` sorts the queue by priority, showing the most critical alert on top with visual emphasis and `aria-live="assertive"`.

#### 5.12.3.4.15.5 – Highlight changed data
*Summary (should):* Data changes following automatic display update should be temporarily highlighted.

- **IMPL-5.12.3.4.15.5-01** `[CSS][JS]` `[Main]` Transient `.flash-update` class (CSS animation) applied dynamically by JS when a monitored value changes, removed after `config.js highlightDurationMs`.

#### 5.12.3.4.15.6 – Store and prioritize information
*Summary (should):* Long lists of information and tasks should be stored and prioritized by the automated aid to reduce the number of decision alternatives and the visual processing load.

- **IMPL-5.12.3.4.15.6-01** `[JS]` `[Main]` Task/event list in `#aux-region` ordered by priority computed in `state.js`, with pagination/collapsing to reduce visual load.

#### 5.12.3.4.15.7 – Information history
*Summary (should):* Provisions should be made to display historical information allowing review of changes to critical information.

- **IMPL-5.12.3.4.15.7-01** `[HTML][JS]` `[Main]` "History" button/tab in `#aux-region` opens a timeline view fed by `log.js` with the changes to critical information.

#### 5.12.3.4.16.3 – Determine decision aid use
*Summary (should):* Users should be able to determine when and how the decision aid should be used.

- **IMPL-5.12.3.4.16.3-01** `[HTML][JS]` `[Main]` "Decision aid: on/off" switch in the preferences panel (`prefs.js`), applicable per task.

#### 5.12.3.4.16.4 – Terms and criteria
*Summary (shall):* Decision aids shall use terminology and criteria appropriate to the target user group.

- **IMPL-5.12.3.4.16.4-01** `[JS]` `[Global]` `i18n.js` selects the glossary (`config.js userGroupGlossary`) based on the user's profile/role for aid-generated text.

#### 5.12.3.4.16.6 – Assist user decisions
*Summary (should):* Decision aids should assist, rather than replace, the human decision maker by providing data for judgment rather than commands to execute.

- **IMPL-5.12.3.4.16.6-01** `[HTML][JS]` `[Main]` The recommendation is presented as an information card with data/rationale and a manual action button; no automatic execution of the decision.

#### 5.12.3.4.16.8 – Task interruption
*Summary (shall):* Use of decision aids shall not require ongoing user tasks to be cancelled.

- **IMPL-5.12.3.4.16.8-01** `[HTML]` `[Main]` The decision aid opens in `#aux-region` as a non-modal panel (not a blocking `<dialog>`), leaving the task flow in `#content-region` uninterrupted.

#### 5.12.3.4.16.14 – Decision aid limit
*Summary (should):* Decision aids should alert the user when a problem/situation is beyond the aid's capability.

- **IMPL-5.12.3.4.16.14-01** `[JS]` `[Main]` `notify.js` issues an "Analysis beyond aid limits" warning when confidence/range exceed the threshold in `config.js`; event logged by `log.js`.

#### 5.12.3.4.16.16 – Estimate uncertainty
*Summary (should):* Decision aids should estimate and indicate the certainty of the analysis and provide the rationale for it.

- **IMPL-5.12.3.4.16.16-01** `[HTML]` `[Main]` The recommendation card includes a confidence percentage/range and a brief textual rationale.

#### 5.12.3.4.16.17 – Decision data and information accessibility
*Summary (should):* Procedural information, derived or processed information, and the data from which it derives should be visible or accessible for decision verification.

- **IMPL-5.12.3.4.16.17-01** `[HTML][JS]` `[Main]` Expandable "Show source data" section in the recommendation card, revealing source data and procedural steps.

#### 5.12.3.4.16.18 – Decision logging
*Summary (shall):* The system shall log decision aid data (displays, procedural information, rules, data, tested hypotheses, summary), with a configurable logging level.

- **IMPL-5.12.3.4.16.18-01** `[JS]` `[Global]` `log.js` exposes `logDecisionAid(snapshot)`; `prefs.js`/`config.js` define `loggingLevel` (`all`/`some`/`none`) selectable by the authorized user.

#### 5.12.3.4.16.19 – Decision explanation detail
*Summary (should):* When the system provides explanations, it should initially offer a short explanation with the possibility of more detail on request, including access to rules/knowledge base/solutions.

- **IMPL-5.12.3.4.16.19-01** `[HTML][JS]` `[Main]` "Why?" button opens a panel (`dialog.js`) with brief text and an expandable "Details" section for rules/knowledge base.

#### 5.12.3.4.16.20 – Clear explanations to user
*Summary (shall):* Explanations shall use terms familiar to the user and maintain consistency with the immediate task.

- **IMPL-5.12.3.4.16.20-01** `[JS]` `[Global]` Explanation text is generated via the common `i18n.js` glossary, with no terminology diverging from the rest of the interface.

#### 5.12.3.4.16.22 – Alert to newly available information
*Summary (shall):* Decision aids shall alert the user to changes in the status of important system information, e.g. when new critical information becomes available during use.

- **IMPL-5.12.3.4.16.22-01** `[JS]` `[Main]` `notify.js` observes the status keys relevant to the decision aid and posts a toast/alert when they change while the panel is open.

### 3.15 Software safety

Requirements for *safety-critical* user interfaces (5.7.19), applicable when the HMI can initiate hazardous functions or present safety-critical information; implemented mainly via `notify.js`, `dialog.js`, `validate.js` and `state.js`.

#### 5.7.19.2.1 – Cancel current process
*Summary (shall):* The software shall cancel current processing with a single user action and revert the system to a predefined safe state.

- **IMPL-5.7.19.2.1-01** `[HTML][JS]` `[Global]` An always-accessible control `#btn-cancel-process` (or dedicated function key in `#function-keys`) shall cancel any ongoing safety-critical process with a single action (`pointerup`), invoking `state.js: revertToSafeState()`.

#### 5.7.19.2.2 – Reaction time
*Summary (shall):* When operator reaction time is not sufficient to prevent a mishap, the software shall revert the system to a known safe state, report the failure, and report system status to the operator.

- **IMPL-5.7.19.2.2-01** `[JS]` `[Global]` `state.js` shall automatically detect the exceedance of critical time thresholds (`config.js: maxReactionTimeMs`) and invoke `revertToSafeState()` without waiting for operator action, followed by a notification via `notify.js` (failure + status) in `#alert-region`.

#### 5.7.19.3.1 – Two or more actions
*Summary (shall):* The system shall require two or more unique operator actions to initiate any potentially hazardous function or sequence of functions.

- **IMPL-5.7.19.3.1-01** `[HTML][JS]` `[Main]` Controls that initiate hazardous functions shall require a sequence of two distinct actions: `pointerup` on the control followed by explicit confirmation on a `<dialog>` managed by `dialog.js` (e.g. "Arm"→"Confirm").

#### 5.7.19.3.2 – Inadvertent actuation
*Summary (shall):* Required actions shall minimize the potential for inadvertent actuation and shall be checked for correct sequence (see separation distances 5.1.3/5.1.4).

- **IMPL-5.7.19.3.2-01** `[CSS][JS]` `[Main]` Controls for hazardous functions shall respect `--touch-gap-min`/`--touch-gap-max` (separation, see §2.4), and `dialog.js` shall verify the correct order of the action sequence, rejecting out-of-sequence confirmations.

#### 5.7.19.4 – Safety-critical displays
*Summary (should):* Operator displays, legends, and other safety-critical interface functions should be clear, concise, and unambiguous and, where possible, duplicated on separate display devices.

- **IMPL-5.7.19.4-01** `[HTML][CSS]` `[Main]` `.safety-critical-display` elements shall use concise text, unambiguous labels, and dual coding (color+icon+text, see §2.4); where the hardware architecture allows for it, the same status shall be duplicable on a secondary display via `net.js`/`state.js`.

#### 5.7.19.5.1 – Positive confirmation
*Summary (shall):* The software shall provide positive confirmation of valid data entry or actions taken (visual or audible feedback so the operator knows the system has accepted the action and is processing it).

- **IMPL-5.7.19.5.1-01** `[JS]` `[Global]` Every valid action/entry shall invoke `feedback.js: acknowledge(el)`, providing visual feedback (`.is-acknowledged` class) and audible feedback (`beep('confirm')`) within the response times of Table V (§2.6).

#### 5.7.19.5.2 – Real-time indication
*Summary (shall):* The system shall provide a real-time indication that it is functioning.

- **IMPL-5.7.19.5.2-01** `[HTML][JS]` `[Global]` A persistent indicator `#system-heartbeat` in `#app-header`/`#status-line` shall show, via `state.js`, that the system is operating (e.g. regular-interval animation/pulse, not just static text).

#### 5.7.19.5.3 – Status indication
*Summary (shall):* Processing functions with delays ≥2 seconds shall require a status indicator for the operator during processing.

- **IMPL-5.7.19.5.3-01** `[HTML][JS]` `[Main]` `#progress-indicator` (footer, §2.2) shall activate automatically for any operation whose expected duration is ≥2 s, per the thresholds of Table V managed by `feedback.js: showProgress()`.

#### 5.7.19.5.4.1 – Improper operator entries
*Summary (shall):* The software shall be able to detect improper operator entries or sequences of entries/operations, to prevent execution of safety-critical functions.

- **IMPL-5.7.19.5.4.1-01** `[JS]` `[Global]` `validate.js` shall check type, format, range, and sequence of every entry/action directed at safety-critical functions, blocking execution in case of anomaly (`aria-invalid="true"`).

#### 5.7.19.5.4.2 – Alert the operator
*Summary (shall):* The software shall alert the operator to the erroneous entry or operation and indicate the error and corrective action.

- **IMPL-5.7.19.5.4.2-01** `[JS]` `[Main]` When `validate.js` detects an anomaly (see `IMPL-5.7.19.5.4.1-01`), `notify.js` shall show in `#message-line`/`#toast-region` a message indicating the specific error and the corrective action to take.

#### 5.7.19.6.1 – Distinguish from routine alerts
*Summary (shall):* Routine alerts shall be coded visually and/or acoustically differently from safety-critical alerts.

- **IMPL-5.7.19.6.1-01** `[CSS][JS]` `[Global]` `notify.js` shall apply distinct classes/sounds: `.alert-routine` (`--color-advisory`/`--color-normal`, short beep) vs `.alert-safety-critical` (`--color-critical`, distinct sound pattern, optional `.flash-code`).

#### 5.7.19.6.2 – Clear a safety-critical alert
*Summary (shall):* The operator shall not be able to clear a safety-critical alert without taking corrective action or performing the subsequent actions required to complete the ongoing operation.

- **IMPL-5.7.19.6.2-01** `[JS]` `[Main]` `.alert-safety-critical` alerts in `#alert-region` shall not expose a simple dismiss control; `notify.js` shall remove them only after confirmation of the required corrective action (via `dialog.js`) or completion of the operational sequence.

#### 5.7.19.6.3 – Unsafe situation alerts
*Summary (shall):* The system shall provide signals alerting the user to unsafe situations via the user interface.

- **IMPL-5.7.19.6.3-01** `[JS]` `[Global]` `state.js` shall emit a `CustomEvent('unsafe-condition')` intercepted by `notify.js` to immediately generate an `.alert-safety-critical` alert in `#alert-region` whenever an unsafe condition is detected.

#### 5.7.19.7.1 – Computer control
*Summary (shall):* Safety-critical timing functions shall be controlled by the computer rather than relying on human input.

- **IMPL-5.7.19.7.1-01** `[JS]` `[Global]` All safety-critical timers (e.g. safety timeouts, confirmation windows) shall be managed by `state.js`/`config.js` client-side (and validated server-side), never dependent on the operator's manual timing.

#### 5.7.19.7.2 – Not modifiable by operator
*Summary (shall):* The system shall prevent the operator from modifying safety-critical timing values from system consoles, except when specifically required by the system design and in accordance with approval protocols.

- **IMPL-5.7.19.7.2-01** `[HTML][JS]` `[Global]` Safety-critical timing fields in `#dialog-host`/configuration panels shall be read-only (`readonly`/`disabled`) for standard user roles in `auth.js`; modification shall be allowed only to a role with explicit privilege and tracked by `log.js`.

### 3.16 Use on mobile and handheld devices (responsive web HMI)

Software/application-level requirements for PED (*Portable Electronic Device*) applications when the web HMI is used from a phone or tablet; hardware/device aspects (enclosure, mounting, power supply, temperature) are excluded. **Primary target:** consistent with §2.1, the primary input device remains the **touchscreen**, with a minimum resolution of **800 × 600 px** scalable upward; there is no separate "mobile" layout. The PED aspects of this section (small-screen adaptation, orientation, divided attention, etc.) are implemented **exclusively through the responsive layout** defined in §2.3 (same S–XXL breakpoints, same header/main/footer grid, same components), not through a separate application or device-specific code.

#### 5.19.1.9.1 – Inadvertent activation
*Summary (shall):* Accidental activation on PEDs shall be avoided by defining unique user action sequences required for activation.

- **IMPL-5.19.1.9.1-01** `[JS]` `[Main][Footer]` `config.js` lists the critical actions requiring confirmation; such controls invoke `dialog.js` (confirmation) or a two-step gesture (long-press + release) instead of a single tap, consistent with `IMPL-5.12.3.4.12.7-01`.

#### 5.19.2.5.2 – Indication of connection status
*Summary (should):* PEDs should provide an indication of connection status in terms of strength/reliability in both wireless and wired modes.

- **IMPL-5.19.2.5.2-01** `[HTML][CSS][JS]` `[Header]` `#connection-indicator` shows a tiered icon (signal strength) + text, updated by `net.js` (`pollConnectionQuality()`), with `--color-normal/--color-warning/--color-critical` color and dual text/icon coding.

#### 5.19.5.1 – General
*Summary (shall):* In addition to the following requirements, PED applications shall be designed according to the applicable criteria of 5.1, 5.2, and 5.17.

- **IMPL-5.19.5.1-01** `[CSS][JS]` `[Global]` Architectural constraint: the same design system (token `--touch-target-min`, `--color-*` palette, `feedback.js`/`validate.js`/`notify.js` modules) defined in §2 applies without exception to narrow breakpoints; no divergent PED-specific component (verified by inspection I).

#### 5.19.5.1.1 – PED adaptation of existing design
*Summary (shall):* When a PED application is based on an existing design, it shall adapt, not replicate it, tailoring it for the PED's screen size.

- **IMPL-5.19.5.1.1-01** `[CSS]` `[Global]` A single HTML markup and a single component library are rearranged via `grid-template`/media queries (breakpoint S 800 px) without duplicating the DOM tree for a "mobile version"; no second codebase.

#### 5.19.5.1.2 – Design around PED capabilities and constraints
*Summary (should):* PED applications should be designed around the PED's capabilities/constraints: (a) consistency of appearance/interaction across multiple devices; (b) task focus with logical sequences/dialogs (see 5.19.5.4).

- **IMPL-5.19.5.1.2-01** `[JS]` `[Global]` `prefs.js` applies the same theme/labels/dialog pattern regardless of device (a).
- **IMPL-5.19.5.1.2-02** `[HTML][JS]` `[Main]` `nav.js` routes by task; at breakpoint S, `#nav-region` collapses into a bar/hamburger menu while keeping the same labels and the same logical order (b).

#### 5.19.5.1.3 – Dynamically changing operational contexts
*Summary (shall):* The design shall support operations in changing contexts (brightness, noise, weather): (a) configure output according to preferences (text size, brightness); (b) change key attributes in a few steps; (c) provide access options to key functions/attributes.

- **IMPL-5.19.5.1.3-01** `[HTML][JS]` `[Header]` `#btn-display-settings` opens a `prefs.js` dialog with sliders for text size/contrast/theme (a); since physical screen brightness is not controllable from the browser, theme (`theme-light/dark/high-contrast`) is assumed as the software equivalent — stated assumption.
- **IMPL-5.19.5.1.3-02** `[JS]` `[Footer]` A single tap on `#function-keys` cycles through themes ("brightness" change in minimal steps) (b).
- **IMPL-5.19.5.1.3-03** `[HTML]` `[Footer]` Quick-settings shortcut reachable in ≤2 taps from any view (c).

#### 5.19.5.1.4 – Limited and divided attention
*Summary (should):* PED design should accommodate limited/divided attention through audible/visual/tactile signals; PED use should not distract the user from other tasks in the cases (a) limited attention on a non-PED task, (b) divided attention PED/non-PED, (c) limited attention with incoming information on another task.

- **IMPL-5.19.5.1.4-01** `[JS]` `[Global]` `feedback.js` uses multimodal signals (visual + `beep()` + `navigator.vibrate`) for alerts, perceivable without sustained visual attention.
- **IMPL-5.19.5.1.4-02** `[HTML][CSS]` `[Main]` Non-critical toasts (`notify.js`) are non-modal, do not steal focus, and auto-dismiss, so as not to interrupt the ongoing task.

#### 5.19.5.1.5 – Application optimization
*Summary (should):* PED applications should be optimized for performance (load time, media playback, saving, connection) and should minimize power consumption.

- **IMPL-5.19.5.1.5-01** `[JS]` `[Global]` `config.js` defines performance budgets aligned with Table V (page change ≤1.0 s, update from host ≤2.0 s); `net.js` prefers WebSocket/push over frequent polling to reduce power consumption; content not visible in `#aux-region` is lazy-loaded.

#### 5.19.5.1.6.1 – Use of industry guidelines
*Summary (should):* Industry guidelines should be used for PED functionalities (e.g. camera, gyroscope) and platforms (iOS, Android, Windows), avoiding interactions that differ from the expected default behavior.

- **IMPL-5.19.5.1.6.1-01** `[JS]` `[Global]` Any PED hardware functionality is accessed via standard web APIs (`MediaDevices`, `DeviceOrientation`) with the browser's native permission UX, without overriding platform conventions (e.g. the standard "back" gesture/button preserved in `nav.js`).

#### 5.19.5.1.6.2 – User expectations
*Summary (should):* The design should allow the user to apply prior knowledge and quickly build a mental model through familiar objects and consistency, reducing mental load.

- **IMPL-5.19.5.1.6.2-01** `[HTML][CSS]` `[Main]` Use of standard HTML controls and common ARIA patterns (buttons, selects, checkboxes) and conventional icons, rather than unfamiliar custom widgets.

#### 5.19.5.1.6.3 – Multiple applications
*Summary (should):* PED applications should behave consistently with other applications on the PED with which the user is familiar.

- **IMPL-5.19.5.1.6.3-01** `[JS]` `[Global]` `nav.js` uses the Session History API consistently with the browser/OS's expected navigation/back behavior, without custom interception of the "back" gesture.

#### 5.19.5.1.6.4 – Designing for multiple PEDs
*Summary (should):* If designing for multiple PEDs, the established design conventions for each PED should be followed, with consistent appearance/functionality across PEDs.

- **IMPL-5.19.5.1.6.4-01** `[CSS]` `[Global]` A single responsive codebase (S–XXL breakpoints of §2.3) guarantees the same set of components/tokens across all PEDs; visual regression testing (T) on the main viewport presets.

#### 5.19.5.1.7 – Facilitation of intended action
*Summary (shall):* PED application functions shall support time-critical user actions.

- **IMPL-5.19.5.1.7-01** `[HTML][CSS]` `[Footer]` Primary time-critical actions remain reachable from `#function-keys` without nested menus, target ≥ `--touch-target-min` (see also `IMPL-5.19.5.4.1-01`).

#### 5.19.5.1.8 – Textual information
*Summary (should):* Where appropriate, the amount of textual information should be minimized to limit the reading required, consistent with 5.17.18.

- **IMPL-5.19.5.1.8-01** `[HTML][JS]` `[Main]` `#content-region` favors icons/short labels with `aria-label`/tooltip for detail; long text collapsed behind "Read more" (`<details>`), text managed by `i18n.js` in concise form.

#### 5.19.5.2 – Error control
*Summary (shall):* PED applications shall meet the error control requirements of 5.1.2.1.7.

- **IMPL-5.19.5.2-01** `[JS]` `[Main]` The error-handling pattern of `validate.js` (inline message + `[aria-invalid]` + correction hint) is applied identically at all breakpoints, verified at 800×600 portrait/landscape.

#### 5.19.5.3 – Feedback
*Summary (shall):* PED application feedback to the user shall meet the feedback requirements of 5.1.2.1.6.

- **IMPL-5.19.5.3-01** `[JS][CSS]` `[Global]` The time budgets of `feedback.js` (Table V) do not vary with viewport size; the feedback's visual area still scales ≥ `--touch-target-min` to remain perceivable on small screens.

#### 5.19.5.4.1 – Critical information
*Summary (should):* When designing PED displays, critical information should be reachable in no more than two key actions (e.g. scrolling and tapping).

- **IMPL-5.19.5.4.1-01** `[HTML][JS]` `[Main][Footer]` `nav.js` ensures that every critical piece of information is reachable with at most 2 actions (e.g. `#btn-home` + 1 tap); acceptance criterion: demonstration (D) counting the taps for each critical piece of information.

#### 5.19.5.4.2 – Command shortcuts
*Summary (should):* Shortcuts (hot keys, voice activation, keystroke combinations) should be provided for frequently used PED commands.

- **IMPL-5.19.5.4.2-01** `[JS]` `[Footer]` `#function-keys` configurable with shortcuts (`config.js shortcutMap`); optional support for voice commands via Web Speech API in `nav.js` and physical keyboard accelerators as secondary input.

#### 5.19.5.4.3 – Application control
*Summary (should):* PED applications should be designed for speed and recovery: fast loading, stop/start/resume/change with minimal effort.

- **IMPL-5.19.5.4.3-01** `[JS]` `[Global]` `state.js` persists session/task state and restores it on `visibilitychange`/app restart; initial rendering with a skeleton interface to meet the ≤1.0 s page-change time of Table V.

#### 5.19.5.4.4 – Auto-rotation of screen
*Summary (shall):* PED applications shall support user-selectable screen auto-rotation to adapt presentation and data entry.

- **IMPL-5.19.5.4.4-01** `[JS]` `[Global]` `prefs.js` exposes the `orientationLock` setting (`auto`/`portrait`/`landscape`) applied via the `screen.orientation` API.
- **IMPL-5.19.5.4.4-02** `[CSS]` `[Main]` `@media (orientation: landscape|portrait)` rearranges `#nav-region`/`#content-region`/`#aux-region` between three columns, two columns, and a single column as per §2.3.

#### 5.19.5.4.5 – Simplicity of application design
*Summary (should):* The design should be simple and uncluttered through: (a) optional hierarchical nesting; (b) word selection rather than text entry; (c) plain language rather than jargon.

- **IMPL-5.19.5.4.5-01** `[HTML]` `[Main]` Use of `<details>`/accordion for hierarchical drill-down in `#content-region`, leaving the choice to expand to the user (a).
- **IMPL-5.19.5.4.5-02** `[HTML][JS]` `[Main]` Forms that favor `<select>`/choice chips over free-text fields, validated by `validate.js` (b).
- **IMPL-5.19.5.4.5-03** `[JS]` `[Global]` `i18n.js` maintains a plain-language glossary, avoiding jargon unless a specialist user population is explicitly stated (c).

#### 5.19.5.4.6 – Language
*Summary (shall):* Language usage shall be consistent with 5.1.3.11.1.8.

- **IMPL-5.19.5.4.6-01** `[JS]` `[Global]` Same `i18n.js` dictionary/style used across all PED and non-PED views, with no duplicated PED-specific strings.

#### 5.19.5.4.7 – Simplicity of text input
*Summary (should):* User input should be simple and based on the operational context: (a) minimize text entry; (b) clearly indicate the required type/format; (c) allow choice by selection rather than manual typing; (d) show prefilled data in forms where possible.

- **IMPL-5.19.5.4.7-01** `[HTML]` `[Main]` Fields with `inputmode`, `pattern`, and a descriptive placeholder managed by `validate.js` (b).
- **IMPL-5.19.5.4.7-02** `[HTML][JS]` `[Main]` Forms favoring `<select>`/autocomplete lists over free text (a, c).
- **IMPL-5.19.5.4.7-03** `[JS]` `[Main]` Fields prefilled from `state.js`/`prefs.js` when the data is already known (d).

#### 5.19.5.4.8 – PED application navigation
*Summary (should):* (a) Navigation structures should be simple and focused on one specific task at a time; (b) navigation between form fields should be predictable.

- **IMPL-5.19.5.4.8-01** `[HTML][CSS]` `[Main]` `#nav-region` highlights only one active task (`.is-current`) per view (a).
- **IMPL-5.19.5.4.8-02** `[HTML][JS]` `[Main]` Natural DOM tab order and consistent `inputmode` across all forms, managed by `validate.js` (b).

#### 5.19.5.4.9 – Dialog closure
*Summary (should):* Dialogs, being a finite interaction between user and PED, should provide closure with a clear beginning, middle, and end of the action sequence.

- **IMPL-5.19.5.4.9-01** `[HTML][JS]` `[Main]` `dialog.js` modal flows show a progress indicator ("Step 2 of 3") and explicit "Confirm"/"Cancel"/"Done" buttons marking closure.

#### 5.19.5.5.1 – Minimize clutter
*Summary (should):* The PED application should provide essential data and functionality for immediate action, minimizing unnecessary elements/clutter/information.

- **IMPL-5.19.5.5.1-01** `[CSS]` `[Main]` At breakpoint S (800 px), non-essential widgets are collapsed into `#aux-region` as a pop-up panel; the default view shows only task-critical controls.

#### 5.19.5.5.2 – Selection highlighting
*Summary (shall):* The item selected on a PED screen shall be clearly indicated, using color, background, text change, or other visual mechanisms.

- **IMPL-5.19.5.5.2-01** `[CSS][HTML]` `[Main]` `.is-selected`/`[aria-selected="true"]` class applies `--color-selected` background color, border, and a checkmark icon plus text change, per the dual-coding convention of §2.6.

#### 5.19.5.5.3 – Basic control availability
*Summary (shall):* Software/hardware controls for basic navigation (e.g. "Back to home", "Back to search results", "Back") shall always be available.

- **IMPL-5.19.5.5.3-01** `[HTML][CSS]` `[Footer]` `#function-keys` permanently keeps "Back"/"Home"/"Search" visible in all views and at all breakpoints, managed by `nav.js` (`history.back()`, `goHome()`); never hidden or removed from the DOM.

#### 5.19.5.5.4 – Content density
*Summary (should):* Content density on the PED screen should be minimized: (a) do not fragment tasks/interaction across too many screens; (b) screens should scroll in only one dimension; (c) minimize text to reduce reading under movement conditions.

- **IMPL-5.19.5.5.4-01** `[JS]` `[Global]` `nav.js` limits the maximum depth of navigation flows (`config.js maxNavDepth`) to avoid fragmentation (a).
- **IMPL-5.19.5.5.4-02** `[CSS]` `[Main]` `#content-region` has `overflow-x: hidden` and vertical-only scrolling, consistent with §2.3 (b).
- **IMPL-5.19.5.5.4-03** `[JS]` `[Global]` `i18n.js` keeps text concise, in line with `IMPL-5.19.5.1.8-01` (c).

#### 5.19.5.5.5.1 – Ability to skim text
*Summary (shall):* To increase the ability to skim text, subtitles and hypertext shall be used in text passages longer than a single screen.

- **IMPL-5.19.5.5.5.1-01** `[HTML]` `[Main]` Text blocks in `#content-region`/`#aux-region` longer than the viewport height use `<h2>`/`<h3>` subtitles and internal anchor links (hypertext).

#### 5.19.5.5.5.2 – Avoid variable line lengths
*Summary (shall):* Variable line lengths shall be avoided by hyphenating words at line breaks to improve readability on small screens.

- **IMPL-5.19.5.5.5.2-01** `[CSS]` `[Main]` `hyphens: auto; overflow-wrap: break-word;` applied to text blocks in `#content-region`/`#aux-region` at narrow breakpoints.

#### 5.19.5.5.5.3 – Scrolling markers
*Summary (shall):* Scrolling markers shall be provided to let users identify their position on the page when content cannot be shown in a single screen.

- **IMPL-5.19.5.5.5.3-01** `[CSS][JS]` `[Main]` Always-visible scrollbar (not overlay-only) plus a JS "X of Y" indicator/sticky mini-map in `#content-region`, fed by the scroll position tracked in `state.js`.

#### 5.19.5.5.6 – Screen readability
*Summary (should):* A range of high-contrast text colors and typefaces should be selectable to optimize readability for the intended operational environment.

- **IMPL-5.19.5.5.6-01** `[CSS][JS]` `[Header]` `prefs.js` exposes a theme selector including `theme-high-contrast` (`--contrast-*` tokens of §2.4) and an alternative font-family, applied via `data-theme` on `<html>`.

#### 5.19.5.5.7 – Color usage
*Summary (shall):* (a) Color shall be used carefully and consistently throughout PED design, consistent with 5.17.25; (b) dual coding (shape or labeling) shall be used together with color.

- **IMPL-5.19.5.5.7-01** `[CSS]` `[Global]` Only the semantic `--color-*` tokens defined in §2.4 are permitted for status coding, applied via shared classes (a).
- **IMPL-5.19.5.5.7-02** `[HTML][CSS]` `[Global]` Every color-coded indication is always accompanied by an icon/shape and text (dual coding); accessibility testing (T) verifies the absence of color-only indicators (b).

