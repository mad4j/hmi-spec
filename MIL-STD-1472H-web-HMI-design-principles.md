# Web HMI Design Principles Derived from MIL-STD-1472H

> **Origin:** distilled from [`MIL-STD-1472H-web-HMI-requirements.md`](MIL-STD-1472H-web-HMI-requirements.md) (extract of MIL-STD-1472H, *Human Engineering*, 15 September 2020).
> **Purpose:** summarize the extract into **20 high-level design principles** for a web interface, covering design and presentation, usability in operational contexts, control, and state management.
> **Level:** technology-independent. For the HTML / CSS / JavaScript requirements that realize these principles see [`MIL-STD-1472H-web-HMI-implementation-requirements.md`](MIL-STD-1472H-web-HMI-implementation-requirements.md); for icons see [`MIL-STD-1472H-web-HMI-icon-requirements.md`](MIL-STD-1472H-web-HMI-icon-requirements.md).

## 1. How to read this document

- Each principle has a **statement** (the principle), an **In practice** list (what it means for a web UI) and a **Source** line with the original paragraph numbers of the extract, so every principle can be traced back to the standard.
- Numeric values (times, sizes, ratios, limits) are those of the standard; a range such as `5.17.10.1–.18` means paragraphs 5.17.10.1 to 5.17.10.18.
- Principles are grouped in five families. The order does not express priority, except that safety-related principles (P13, P19) prevail over convenience-related ones (e.g., P10) when they conflict.
- Military wording of the source ("operator", "mission-critical") should be read as the user and the critical functions of the specific web application.

## 2. Overview

| # | Principle | Family |
|---|---|---|
| P1 | Design for the user's work and keep it simple | A. Design and presentation |
| P2 | Be consistent and predictable | A. Design and presentation |
| P3 | Use plain, unambiguous language and labels | A. Design and presentation |
| P4 | Structure, group and orient | A. Design and presentation |
| P5 | Present information by importance, in directly usable form | A. Design and presentation |
| P6 | Code information meaningfully, consistently and redundantly | A. Design and presentation |
| P7 | Stay legible under any lighting and let the user adjust it | A. Design and presentation |
| P8 | Design for the operational context: stress, limited attention, urgency | B. Usability in operational contexts |
| P9 | Be touch-first and device-adaptive | B. Usability in operational contexts |
| P10 | Provide efficient dialogs for novices and experts | B. Usability in operational contexts |
| P11 | Provide built-in guidance and help | B. Usability in operational contexts |
| P12 | Keep the user in command: override, abort, undo, handover | C. Control and safety |
| P13 | Safeguard critical and destructive actions; fail safe | C. Control and safety |
| P14 | Prevent, detect and recover from errors | C. Control and safety |
| P15 | Apply human-centered automation | C. Control and safety |
| P16 | Make the system state always visible | D. State management and feedback |
| P17 | Give immediate, unambiguous feedback within bounded response times | D. State management and feedback |
| P18 | Model states explicitly and never lose the user's work | D. State management and feedback |
| P19 | Alert in a prioritized, redundant and trustworthy way | E. Alerts and access |
| P20 | Grant secure, role-aware and transparent access | E. Alerts and access |

## 3. Principles

### A. Design and presentation

#### P1 – Design for the user's work and keep it simple

**Statement.** The interface shall be the simplest design that satisfies the function, compatible with the work and the cognitive processes of its users, and shall minimize conditions that degrade performance or provoke human error.

**In practice**
- Show only the data and functions needed for the current task; remove clutter and secondary information.
- Make the relationship between controls and the displays they affect immediate and explicit.
- Let users recognize instead of remember: no mnemonics, codes or long sequences to memorize.
- Design for the lowest anticipated skill level, and give experienced users shortcuts (see P10).
- Use progressive disclosure: details are retrieved only when the user asks for them.

**Source:** 4.5.2, 4.7.1, 4.9.1, 5.17.1.1–.4, 5.1.3.12.1.4, 5.1.3.12.1.11, 5.19.5.4.5, 5.19.5.5.1.

#### P2 – Be consistent and predictable

**Statement.** The same thing shall look, be located and behave the same way everywhere – within the application, across related systems and devices, and according to platform conventions.

**In practice**
- Recurring controls, data fields, labels, status areas, prompts and advisories keep the same relative position on every screen.
- One term, one meaning, one abbreviation – in the UI, in messages and in help.
- The same format is used to enter and to display the same data (a date entered as `mm/dd/yyyy` is shown as `mm/dd/yyyy`).
- A control or shortcut keeps its function; the display reacts to controls in a way that matches user expectations.
- Follow platform and industry conventions instead of inventing behaviors; keep appearance and interaction consistent across devices.

**Source:** 5.1.1.3.4, 5.1.1.4.1, 5.1.2.2.1.5, 5.1.2.2.1.5.2–.4, 5.1.2.3.2, 5.1.3.11.4.3, 5.17.3.2.4, 5.17.12.9, 5.17.15.3.1–.5, 5.17.24.5, 5.19.5.1.2, 5.19.5.1.6.1–.4.

#### P3 – Use plain, unambiguous language and labels

**Statement.** Text, labels and icons shall be understandable by the intended users without translation, code sheets or guessing.

**In practice**
- Use the users' own vocabulary and application terms; avoid unfamiliar abbreviations and make the definition of any abbreviation available in the system.
- Label every element unless its purpose is obvious; labels are unambiguously tied to their element, consistent, and visually distinct from data and from critical-message coding.
- Present data in directly usable form (no mental conversion), always with units of measure and in units familiar to the user.
- Icons supplement text: familiar, meaningful, culturally appropriate, with a text label or tooltip.
- Keep text short and direct to minimize reading.

**Source:** 5.1.2.1.6.14–.15, 5.1.3.11.1.8, 5.1.3.12.8.2–.3, 5.17.3.1.3.5–.6, 5.17.4.10, 5.17.13.10, 5.17.14.2.1–.7, 5.17.14.3.5, 5.17.15.2, 5.17.20.1, 5.19.5.1.8.

#### P4 – Structure, group and orient

**Statement.** Content and controls shall be grouped by function, sequence, importance and frequency, and the user shall always know where they are and how to move.

**In practice**
- Group related items; order them by sequence of use (left-to-right or top-to-bottom) and place the most important and most frequent ones where they are easiest to reach.
- Give every page, panel or frame a unique, meaningful title; give each information source its own area; limit the number of panels per page (not more than seven) and the amount of scrolling (three screens or fewer, one scroll dimension).
- Always show the current position: position in the data space, step in a sequence, "Page 2 of 5", scroll markers.
- Always provide basic navigation (Back, Home); keep hierarchies shallow (not more than four levels for a process, three for submenus) and keep menu levels visible during selection.
- Give each dialog a clear beginning, middle and end, focused on one task at a time.

**Source:** 5.1.2.2.1, 5.1.2.2.1.1–.3, 5.1.3.12.1.10, 5.12.3.4.8, 5.17.3.1.2.6, 5.17.3.2.2.4, 5.17.3.2.14, 5.17.8.1–.5, 5.17.15.5–.8, 5.19.5.4.8–.9, 5.19.5.5.3–.5.

#### P5 – Present information by importance, in directly usable form

**Statement.** Information shall be limited to what the task requires, with critical information salient and never hidden, and represented in the form that best supports the user's decision.

**In practice**
- Critical information is separate and salient from non-critical information and is never obscured by pagination or scrolling.
- Display only the precision needed; give each datum its context.
- Choose the representation by task: digital for an exact value, graphic for comparison, hybrid for both; always show units.
- Charts: labeled axes, a single scale per axis, consistent scales across comparable charts, reference/critical levels shown, numeric values on request.
- Tables and lists: recognizable order, consistent column/row layout, repeated headers when split, hierarchical partitioning of long lists; chunk long codes into groups.
- Real-time values update at a readable rate (not more than once per second to be read reliably; three to four times per second for rate of change); changed data is temporarily highlighted.

**Source:** 5.17.14.1.1–.5, 5.17.14.3.1–.5, 5.17.15.1, 5.17.17.1–.4, 5.17.18.10.1–.5, 5.17.20.3.1–.14, 5.17.20.4.1–.7, 5.17.21.2.1–.6, 5.17.22.1.1.1–.2, 5.12.3.4.13.1, 5.12.3.4.15.5.

#### P6 – Code information meaningfully, consistently and redundantly

**Statement.** Color, size, shape, symbol, position, brightness and flashing shall carry meaning, be used consistently, never impair legibility, and never be the only carrier of information.

**In practice**
- Use common conventions (e.g., red for danger/stop, yellow for caution, green for normal) and keep them identical across the whole application.
- Never rely on color alone: add shape, symbol, label or position so that color-deficient users and monochrome rendering are supported; prefer filled to outlined symbols.
- Limit the number of codes: not more than 11 nameable colors, three size levels, two brightness levels.
- Reserve strong attention-getters (flashing, inverse video, special symbols) for critical conditions and use them sparingly; text that must be read does not flash; provide acknowledgement or suppression.
- Allow color customization only for non-critical information.

**Source:** 4.14, 5.1.3.1.16, 5.17.24.1–.6, 5.17.25.1–.11, 5.17.25.15, 5.17.26.1–.3, 5.17.27.1–.7, 5.17.28, 5.17.31.1–.4, 5.19.5.5.7.

#### P7 – Stay legible under any lighting and let the user adjust it

**Statement.** Text and graphics shall remain readable over the whole range of expected ambient conditions, and the user shall be able to tune the display to the environment.

**In practice**
- High contrast between characters and background: 6:1 or greater (10:1 preferred).
- Adequate character size, defined by visual angle at the greatest viewing distance (not less than 2.9 mrad, 4.5 mrad preferred), in a common sans-serif typeface in which similar characters (l/1, Z/2) are distinguishable.
- User-adjustable brightness, contrast, text size and polarity: light text on dark background in dark environments, dark on light otherwise; a range of high-contrast color/typeface choices.
- Small screens: adapt, do not copy, the layout; subheadings for skimming; avoid ragged variable-length lines.

**Source:** 5.2.1.2.3, 5.2.1.3, 5.2.2.6, 5.2.2.7, 5.2.2.10.2–.4, 5.17.18.2, 5.17.18.4–.7, 5.17.25.16.3–.4, 5.19.5.1.3, 5.19.5.5.6.

### B. Usability in operational contexts

#### P8 – Design for the operational context: stress, limited attention, urgency

**Statement.** The interface shall remain usable under real conditions of use – high workload, stress, interruptions, divided attention, changing environment – and shall minimize the time needed to detect, decide and act.

**In practice**
- Critical information and time-critical functions are reachable in two actions or fewer; emergency displays and controls are visible and reachable without delay.
- Support limited and divided attention: glanceable displays, visual/audible/tactile cues that attract attention without distracting from other tasks.
- Do not exceed the user's ability to discriminate detail or manipulate controls in the real conditions of use.
- Minimize the chance of human error especially in alert, emergency and non-routine conditions; do not rely on human timing for safety-critical timing.
- Optimize speed and recovery: fast loading, easy stop/start/resume; task times no worse than the predecessor system.
- Evaluate latency and usability in a representative environment.

**Source:** 4.9.1, 5.1.2.1.5, 5.1.2.1.6.5, 5.1.2.2.3.11, 5.1.3.1.4, 5.7.19.7.1, 5.19.5.1.3–.5, 5.19.5.1.7, 5.19.5.4.1, 5.19.5.4.3.

#### P9 – Be touch-first and device-adaptive

**Statement.** The interface shall be operable by fingertip on any supported screen size, with alternative inputs where touch is not sufficient.

**In practice**
- Size and space targets for fingers: actuation area not smaller than 15 × 15 mm (16 × 16 mm for keyboards) and separation of at least 3 mm (2 mm for keyboards); any fingertip contact can actuate.
- Acknowledge each touch positively and quickly (display response not more than 100 ms).
- Touch is not the sole input for mission- or safety-critical interfaces, nor for sustained typing; provide keyboard/shortcut alternatives; hover or context menus are never the only way to do something.
- Require an explicit actuation separate from pointer position.
- Adapt layouts to screen size and orientation (user-selectable rotation, one-dimensional scrolling), keep behavior consistent across devices.
- Minimize typing: select instead of type, prefill, indicate the expected input format.

**Source:** 5.1.3.1.1.1–.3, 5.1.3.1.3–.9, 5.1.3.1.14–.17, 5.17.3.1.5.1–.2, 5.17.5.9, 5.19.3.3–.4, 5.19.5.1.1–.2, 5.19.5.1.6.1–.4, 5.19.5.4.4, 5.19.5.4.7, 5.19.5.5.4.

#### P10 – Provide efficient dialogs for novices and experts

**Statement.** The dialog style shall fit the task and the user's skill, minimize input effort and memory load, and leave the pace to the user.

**In practice**
- Choose the dialog type (menus, forms, question-and-answer, commands) for the task and the users; let experienced users bypass steps through shortcuts and direct function calls, shown next to the menu entries.
- Show the alternatives before selection and the current value of any parameter being changed.
- Defaults are shown automatically, accepted with one action and replaceable per transaction; never ask for data the system already has.
- Forms: related fields grouped; required fields distinct from optional; format, length and units indicated; cursor in the first field; predictable field order; any item can be changed before submission; omissions reported.
- The user paces data entry and completes each entry with an explicit action; entries are validated for format, legality, range and completeness before processing.

**Source:** 5.1.3.12.1.1–.2, 5.1.3.12.1.7–.9, 5.1.3.12.1.12, 5.17.2.2.1–.4, 5.17.3.2.1, 5.17.3.2.9–.11, 5.17.4.1–.10, 5.17.6.1–.27, 5.17.20.2.1–.6, 5.19.5.4.2, 5.19.5.4.7.

#### P11 – Provide built-in guidance and help

**Statement.** Help and prompts shall be built in, always reachable with one standard action, specific to the context and never in the way.

**In practice**
- Context-sensitive, self-contained, multi-level help (summary first, drill-down on request), searchable, with the same terminology as the UI.
- Help covers allowable options, system capabilities, procedures, ranges of values and abbreviations.
- Prompts explain commands, formats, sequences and ask for missing data, in a standard area, in explicit and clear terms that need no memorization.
- Tooltips identify icons and controls.
- System-initiated help (for novice or infrequent users) is specific to the current task, does not obscure controls or displays, and can be enabled/disabled easily.

**Source:** 5.17.12.1–.10, 5.17.13.1–.9, 5.17.14.2.7, 5.17.2.1.7, 5.17.3.1.3.5, 5.17.3.2.16.

### C. Control and safety

#### P12 – Keep the user in command: override, abort, undo, handover

**Statement.** Whatever the level of automation, the user shall remain in command, able to stop, reverse, override and take over.

**In practice**
- Every process can be stopped with a single action without losing prior work; time-consuming processes offer an abort.
- Multi-step processes allow returning to a previous level (multi-level undo) at any point.
- The user can override, shut down or neutralize automated functions, and can deliberately override out-of-tolerance conditions.
- Where control can be shared or transferred (normal/backup, local/remote, user to user), show which station has control and allow takeover according to a defined protocol; critical functions have a backup means of control.
- Non-critical audio and system-initiated aids can be turned off by the user.

**Source:** 4.6.2, 4.13.2–.3, 5.1.2.1.6.7, 5.1.3.12.1.3.6, 5.12.3.2.1, 5.12.3.2.10.1–.3, 5.12.3.4.14.6, 5.17.4.2, 5.17.10.10, 5.7.19.2.1, 5.3.1.8, 5.17.12.10.3.

#### P13 – Safeguard critical and destructive actions; fail safe

**Statement.** Actions that are destructive, irreversible or hazardous shall be hard to trigger by accident, shall be verified before execution, and failures shall lead to a safe state.

**In practice**
- Destructive or irreversible actions require extended user attention: explicit confirmation that shows the consequences, or two or more unique actions for hazardous functions; critical touch actions require an additional confirmatory action.
- Prevent accidental activation by placement, separation and guarding; dedicate controls to critical or emergency functions; protection must not prevent operation in the time required.
- Show critical entries for verification before commit; show the old value before a change or deletion; permanent deletion needs an affirmative response.
- On failure, revert to a known safe state, report the failure and the system status; safety-critical timing is computer-controlled and not user-modifiable.
- Spelling errors and common slips never produce valid commands or start transactions.

**Source:** 4.6.1, 5.1.1.7.1–.3, 5.1.1.8.3, 5.1.1.9.2, 5.1.3.1.7, 5.1.3.11.1.11, 5.1.3.12.1.5–.6, 5.17.2.1.11, 5.17.5.13–.14, 5.17.10.6, 5.17.10.11, 5.17.13.3–.4, 5.7.19.2.2, 5.7.19.3.1–.2, 5.7.19.7.1–.2, 5.19.1.9.1.

#### P14 – Prevent, detect and recover from errors

**Statement.** The interface shall prevent errors where possible, detect them early, explain them constructively and make recovery easy and lossless.

**In practice**
- Validate at logical breaks (end of field) rather than per keystroke; show the error within 0.2 s.
- Error messages: application terms (not system internals), specific, neutral and constructive in tone, state how to recover, appear next to the cause without hiding what the user needs, persist until corrected or dismissed, offer more detail on request, and change on repetition so the user knows the retry was processed.
- Rejected input is explained with reason and corrective action; missing data is prompted.
- Allow partial correction and easy editing; acknowledge each correction.
- Automatic correction only when the solution is unique, apparent to the user and can be disabled; always display corrections for confirmation; suggest the closest valid entry.
- Never discard the user's entries because of an error.

**Source:** 5.1.2.1.6.11, 5.1.2.1.7.8, 5.1.2.1.7.11.2, 5.17.9.10, 5.17.10.1–.18, 5.17.13.5, 5.17.6.22, 5.7.19.5.4.1–.2, 5.19.5.2.

#### P15 – Apply human-centered automation

**Statement.** Automation shall be introduced only where it improves performance, safety or workload, and shall stay predictable, transparent and subordinate to the user.

**In practice**
- Automate for a reason (performance, safety, workload), not because it is technically possible.
- Make the purpose and effect of automation predictable and consistent; avoid automation surprise.
- State clearly who – user or system – performs each task, and signal every change of mode or level of automation, in particular reversions caused by failure; indicate when the user must return to manual control and what to do.
- Keep the user informed and engaged: current state, capability, projected future states, evidence that monitoring is still active, means to check set-up and inputs.
- Few modes, easy to switch, frequently used ones more accessible; hazardous modes are flagged and protected from inadvertent activation.
- For high-risk decisions automation stops at recommending. Decision aids assist rather than replace: they show rationale, certainty and limits, give short explanations with detail on request, log their data, and never force ongoing tasks to be cancelled.

**Source:** 4.13.1–.4, 5.12.3.1.2.1–.2, 5.12.3.2.1–.10, 5.12.3.3.2, 5.12.3.4.2.1–.3, 5.12.3.4.5, 5.12.3.4.12.1–.7, 5.12.3.4.13.4, 5.12.3.4.16.3–.22.

### D. State management and feedback

#### P16 – Make the system state always visible

**Statement.** At any moment, without having to ask, the user shall be able to tell what the system is doing, in which mode it is, who is operating in which role, and whether the data can be trusted.

**In practice**
- The current operating mode is displayed at all times; availability, connection status (strength/reliability) and known periods of unavailability or expected delay are shown.
- The active account, role, control station and concurrent sessions are visible; security classification or required notices are shown prominently.
- Real versus simulated (training) data or functions, and live versus frozen displays, are clearly distinguished and labeled.
- Data quality is explicit: incomplete, missing, unreliable, invalid or backup data is marked; data changed by an update is highlighted; a quiet monitoring system still shows that it is running.
- System status is available automatically or on request, in a standard location.

**Source:** 4.6.2, 4.13.4, 5.1.2.1.6.2, 5.1.2.1.6.9, 5.12.3.2.3, 5.12.3.4.13.4, 5.12.3.4.15.1–.2, 5.16.1.3.2, 5.16.1.4, 5.16.2.14, 5.16.5.1, 5.16.6.2–.3, 5.17.9.2, 5.17.22.1.2.4, 5.19.2.5.2, 5.7.19.5.2.

#### P17 – Give immediate, unambiguous feedback within bounded response times

**Statement.** Every user input shall produce a consistent, perceptible response, and delays shall be bounded and explained.

**In practice**
- Acknowledge every action: highlight the selected option; confirm acceptance, progress and completion of a control action; report the outcome of every process and the next action required; use an alternative modality when no visual feedback is given.
- Response-time budgets (Table V of the standard): about 0.1 s for a click/tap, 0.2 s for typed echo, pointing and error messages, 0.5 s for local updates, 1 s for a page turn, 2 s for simple inquiries and host updates, 10 s for file updates and complex inquiries; never slower than the predecessor system.
- Delays over 1 s display a "processing" message; delays over 10 s display a progress indicator with the remaining time or fraction when possible; safety-critical processing of 2 s or more requires a status indicator.
- Warn before time-consuming or resource-intensive commands and offer to abort them; if input is locked out, tell the user and signal when input is accepted again.
- When lag between action and effect is unavoidable, immediately show the process and the direction of change.
- Confirmation of an input never removes displayed data.

**Source:** 5.1.1.8.1–.2, 5.1.2.1.6.1–.13, 5.1.2.3.3–.4, 5.1.3.1.3–.4, 5.1.3.12.1.3.3–.5, 5.17.4.3–.6, 5.17.9.1–.13, 5.7.19.5.1, 5.7.19.5.3, Appendix A (Table V).

#### P18 – Model states explicitly and never lose the user's work

**Statement.** Every element and process shall have a defined, visible state; transitions shall be clear and consistent; changes of state shall never destroy user work.

**In practice**
- Elements have distinguishable states: available, unavailable (greyed out or removed), selected, processing/locked, frozen, completed, aborted; the current selection is clearly marked; non-entry areas differ from entry areas.
- A process shows its position in the sequence and ends with an explicit outcome and the next required step.
- Input lockouts are indicated and ended with a visible signal; a process can be interrupted and resumed from the point of interruption; a freeze mode has a single-action resume (at the freeze point or at the live point).
- Inactivity logoff, logoff, session expiry, navigation and aborts do not cause data loss: pending transactions are checked and the user is warned or asked to confirm.
- Users can save and retrieve their work, settings and personalization within the permitted areas.

**Source:** 5.1.3.11.4.4–.5, 5.1.3.12.1.3.3–.6, 5.1.3.12.1.10.2, 5.17.3.2.3, 5.17.6.23–.25, 5.17.8.13, 5.17.9.9, 5.17.9.12–.13, 5.17.13.4, 5.17.21.1.5, 5.17.22.1.2.1–.4, 5.17.22.3.4.4, 5.16.4.1–.2, 5.19.5.5.2.

### E. Alerts and access

#### P19 – Alert in a prioritized, redundant and trustworthy way

**Statement.** Alerts shall draw attention in proportion to priority, shall be supported by more than one cue, and shall be rare enough to be trusted.

**In practice**
- Three levels: warning (immediate action), caution (attention, not necessarily immediate), advisory (information); the higher level always takes precedence; simultaneous messages are shown in priority order and no caution is presented while a warning is active.
- Alert only when needed: none during normal operation; minimize false and nuisance alerts; routine alerts are coded differently from safety-critical ones.
- Group warnings and cautions together with the information and controls needed to respond; allow sorting by priority or time.
- Use at least two discriminating cues (color, shape, symbol, position, flashing); audio only supplements the visual alert, and every non-verbal aural alert has a visual alert that names the condition.
- Alerts persist until the condition is restored or acknowledged; safety-critical alerts cannot be cleared without corrective action; messages that need acknowledgement appear in their own dialog.
- User controls: non-critical audio can be silenced with a visible indication, volume cannot be set inaudible or excessive; alarm settings are visible and alarm status is available at shift handover.
- Warning text describes the hazard, how to avoid it and the consequence, in short direct sentences.

**Source:** 5.3.1.3, 5.3.1.8–.9, 5.3.2.1.5, 5.3.2.5–.7, 5.3.5.10.1, 5.3.5.10.4, 5.3.5.11, 5.3.9.1–.6, 5.7.1.2, 5.7.1.7.1–.3, 5.7.2.1.2, 5.7.2.2.1–.2, 5.7.3.1–.10, 5.7.19.6.1–.3, 5.17.7.2.2, 5.17.15.4, 5.19.4.5.

#### P20 – Grant secure, role-aware and transparent access

**Statement.** Access shall be role-based, simple for legitimate users, explicit about its status, and never at the expense of data or situation awareness.

**In practice**
- Role-based access: users see only the functions and content their role allows (unauthorized areas are hidden); users with several roles have a linked profile and see which role is active.
- Logon is a separate step completed before any operational option; identification is as simple as security allows; multifactor authentication with the viable options explained; password not echoed, criteria and live feedback while creating one; password managers supported.
- Every access outcome gives its status, the specific reason for failure and a corrective action; the number of remaining attempts is shown before lockout; a locked-out user can request a reset.
- Sessions: automatic logoff after inactivity with no data loss; logoff warns about pending data loss and asks confirmation; concurrent sessions are visible and limited; notices and classification banners are displayed until acknowledged.
- Real data is protected from simulated data; intrusion attempts are signaled.
- Administrators can see roles, permissions, logon history and active sessions, create/edit/unlock accounts, and get feedback on account setup, disabling and purging.

**Source:** 5.16.1.1–.15, 5.16.2.1–.14, 5.16.3.1–.6, 5.16.4.1–.2, 5.16.5.1–.2, 5.16.6.1–.3, 5.16.7.1–.6, 5.17.8.7, 5.17.8.10–.11.

## 4. Coverage of the extract

| Section of the extract | Principles |
|---|---|
| 1. General design principles | P1, P2, P8, P12, P13 |
| 2. Feedback, system status and response time | P16, P17, P18 |
| 3. Cybersecurity | P16, P20 |
| 4. Controls, pointing and touch input | P4, P9, P13 |
| 5. Interactive control and dialog design | P10, P12, P13, P18 |
| 6. Dialog types (command, Q&A, query, menus, forms) | P4, P9, P10, P11, P13 |
| 7. Error management, user guidance, help and prompts | P11, P13, P14 |
| 8. Notifications, messaging, alerts and audio | P19 |
| 9. Windows, portals and page structure | P4, P20 |
| 10. Information content, format and presentation | P3, P5 |
| 11. Coding of information | P6 |
| 12. Graphics, dynamic displays, geospatial, printing, transmission | P5, P18 |
| 13. Display legibility, contrast and small-screen adaptation | P7 |
| 14. Automation and supervisory control | P12, P15, P16 |
| 15. Software safety | P8, P13, P14, P17, P19 |
| 16. Use on mobile and handheld devices | P1, P7, P8, P9 |
