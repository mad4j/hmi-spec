# Technical Specification – Icon Requirements for a Web HMI (Derived from MIL-STD-1472H)

> **Source document:** [`MIL-STD-1472H-web-HMI-requirements.md`](MIL-STD-1472H-web-HMI-requirements.md) (extract of MIL-STD-1472H, *Human Engineering*, 15 September 2020). Original paragraph numbers are kept for traceability.
> **Companion document:** [`MIL-STD-1472H-web-HMI-implementation-requirements.md`](MIL-STD-1472H-web-HMI-implementation-requirements.md) (HTML/CSS/JS tokens such as `--px-per-mm`, `--touch-target-min`, `--viewing-distance-mm` are reused here).
> **Purpose:** collect, analyze and consolidate every requirement of the standard that applies to **icons and pictorial symbols** (toolbar icons, menu icons, status/alert symbols, wait indicators, markers) of a touch-capable web HMI, and complete them with explicit, verifiable design values.

---

## 1. Scope and method

### 1.1 Terminology

MIL-STD-1472H does not define "icon" separately; it uses *icon*, *symbol*, *pictorial symbol*, *symbology* and *graphic* for the same family of visual elements. In this document **icon** means any pictorial symbol displayed on screen that represents a command, object, state, alert or process (including status lamps and markers). Non-pictorial coding (plain color areas, text only) is outside the scope except where it must accompany an icon.

### 1.2 How the requirements were identified

1. All paragraphs of the extract containing the words *icon, symbol, symbology, pictorial, graphic menu, toolbar, tooltip, shape* were selected.
2. Paragraphs on **coding** (color, flash, size, brightness, shape), **contrast**, **touch targets**, **warnings/cautions** and **status feedback** were added because they directly constrain how an icon is drawn, sized, colored, animated or paired with text.
3. Requirements were consolidated by topic, de-duplicated (e.g., 5.1.2.1.6.8 and 5.17.9.9) and given an identifier `ICN-<topic>-<nn>`.

### 1.3 Conventions

| Column | Meaning |
|---|---|
| **ID** | `ICN-<topic>-<nn>`; stable identifier for tests and reviews. |
| **Level** | **shall / should / may** exactly as in the standard for *Standard* items. |
| **Basis** | **S** = stated in MIL-STD-1472H (paragraph number given in *Source*). **D** = *derived*: an engineering interpretation or numeric design value needed to make an S requirement verifiable on the web; it is **not** a statement of the standard and its keyword is a project decision. |
| **Source** | Paragraph(s) of MIL-STD-1472H (as numbered in the extract). |

The words *shall / should / may* keep the standard's meaning: **shall** = mandatory, **should** = recommended, **may** = permitted.

### 1.4 Key finding

The standard contains **no icon-specific design rules** (grid, stroke, style, file format, icon library). Its requirements concern what an icon must *communicate* (meaning, labeling, consistency, cultural suitability), how it must be *perceived* (size, contrast, color, redundancy) and how it must *behave* (priority, flashing, wait indication). Section 3 reflects this; items that need numeric values are made explicit in section 4.

---

## 2. Summary of the most critical rules

| # | Rule | Source |
|---|---|---|
| 1 | Every icon that represents a command has a text label (tooltip and, on touch, a fixed visible label or an equivalent always-available label). | 5.1.3.12.8.3, 5.17.3.1.3.5, 5.17.3.1.3.6, 5.7.2.2.3 |
| 2 | Icons are analogs of what they represent or are in general use and well known; non-standard icons are avoided. | 5.17.31.2, 5.17.3.1.3.6, 5.17.3.2.17.1 |
| 3 | Icons are used consistently within the application and across displays. | 5.17.24.5, 5.17.3.2.17.1, 5.1.2.2.1.5.2 |
| 4 | Color is never the only code: a second code (shape, label, position) is always present. | 5.1.3.1.16, 5.17.25.11, 5.19.5.5.7.b, 5.7.3.5.2.2 |
| 5 | Symbols used for critical conditions are used for no other purpose. | 5.17.31.3 |
| 6 | Pictorial symbols subtend ≥ 2.9 mrad (≥ 4.5 mrad recommended) at the greatest viewing distance; touch actuation area ≥ 15 × 15 mm. | 5.17.18.2, 5.1.3.1.17 |
| 7 | Colored symbols differ from the background by ≥ 100 ΔE; colors in a set differ by ≥ 20 ΔE; filled symbols are preferred to outlined ones. | 5.17.25.16.1, 5.17.25.16.2, 5.17.25.15 |
| 8 | Wait states longer than 1 s show a message or icon; longer than 10 s a progress indicator. | 5.17.9.9, 5.17.9.9.1, 5.17.9.9.2 |
| 9 | Warning = flashing red, 3–5 Hz, 50 % duty; caution = yellow, ≤ 2 Hz if flashing; read text never flashes. | 5.7.3.5.1, 5.7.3.5.2, 5.7.3.5.2.1, 5.17.27.5 |

---

## 3. Requirements

### 3.1 Selection and use of icons (USE)

| ID | Requirement | Level | Basis | Source |
|---|---|---|---|---|
| ICN-USE-01 | Menus may use icons to represent control options. | may | S | 5.17.3.2.17, 5.17.3.2.17.2 |
| ICN-USE-02 | Graphic (iconic) menus may be used when users have different linguistic backgrounds, or by casual users as a supplement to other types of interactive control. | may | S | 5.1.3.12.8.1, 5.1.3.12.8.2 |
| ICN-USE-03 | Icon-based interaction shall be a *supplement*: every function reachable through an icon shall also be reachable through a textual/menu path (no function available only through an unlabeled icon). | shall | D | 5.1.3.12.8.1, 5.1.3.12.8.3, 5.17.3.1.5.1 |
| ICN-USE-04 | Symbol coding may be used to enhance information assimilation from data displays. | may | S | 5.17.31.1 |
| ICN-USE-05 | Shape coding may be used for search and identification tasks; the shapes selected should be based on established standards or conventional meanings. | may / should | S | 5.17.32 |
| ICN-USE-06 | Coding by icon shall be meaningful, shall not reduce legibility, and shall not adversely affect user or system performance. | shall | S | 5.17.24.2, 5.17.24.4 |
| ICN-USE-07 | Coding (including icon coding) shall be used to discriminate views/modes, identify critical information, unusual values, changed items, high-priority messages, special areas, entry errors and criticality of command entry. | shall | S | 5.17.24.1 |
| ICN-USE-08 | Commonly used coding conventions should be used. | should | S | 5.17.24.3 |
| ICN-USE-09 | Clutter shall be minimized: only essential elements are displayed; no more than three size levels and no more than two brightness levels are used for coding. | should / shall | S | 5.19.5.5.1, 5.17.28, 5.17.26.2 |

### 3.2 Meaning and recognizability (SEM)

| ID | Requirement | Level | Basis | Source |
|---|---|---|---|---|
| ICN-SEM-01 | Symbols shall be analogs of the event or system element they represent, **or** shall be in general use and well known to the expected users. | shall | S | 5.17.31.2 |
| ICN-SEM-02 | Non-standard icons should be avoided. If one is unavoidable it shall provide a label via tooltip and should include a fixed-text label. | should / shall | S | 5.17.3.1.3.6 |
| ICN-SEM-03 | Icons should be consistent with icons currently used in other systems and familiar to the user. | should | S | 5.17.3.2.17.1 |
| ICN-SEM-04 | The user shall not need a code sheet, manual or memorization to interpret an icon; the meaning shall be learnable from the icon plus its label. | shall | D | 5.1.2.1.6.14, 5.1.3.12.1.11, 5.17.9.14 |
| ICN-SEM-05 | Icons shall be selected from an established set (platform or industry conventions) rather than invented, and any deviation shall be justified and documented in the icon register (see ICN-CON-04). | should | D | 5.19.5.1.6.1, 5.19.5.1.6.2, 5.17.31.2 |
| ICN-SEM-06 | For multilingual or non-reading populations, commonly understood icons should be considered, supplemented by text labels in the relevant languages. | should | S | 5.7.2.2.3 |
| ICN-SEM-07 | A symbol that marks a word shall be separated from the beginning of the word by one space. | shall | S | 5.17.31.4 |
| ICN-SEM-08 | Ambiguity shall be avoided: two different functions shall not share the same icon, and one function shall not use different icons in different places. | shall | D | 5.1.2.1.1, 5.1.2.3.1, 5.17.24.5 |
| ICN-SEM-09 | Icon-only abbreviations/acronyms embedded in the icon should be avoided unless well known to all potential users. | should | D | 5.1.2.1.6.15, 5.17.9.14.2 |

### 3.3 Text labels and tooltips (LBL)

| ID | Requirement | Level | Basis | Source |
|---|---|---|---|---|
| ICN-LBL-01 | Where icons represent control actions in menus, textual labels shall be displayed, or made available for display, with each icon. | shall | S | 5.1.3.12.8.3 |
| ICN-LBL-02 | Toolbars with icons shall provide labels via a tooltip (hover to show label). | shall | S | 5.17.3.1.3.5 |
| ICN-LBL-03 | A tooltip should be displayed over an item as the user hovers the pointer without clicking it. | should | S | 5.17.3.2.16 |
| ICN-LBL-04 | Because hover does not exist on touch input, the label shall be made available by a touch-compatible alternative (visible fixed-text label, long-press, or focus) in addition to the hover tooltip; an icon whose only label is a hover tooltip is non-conformant on a touch device. | shall | D | 5.1.3.12.8.3, 5.17.3.1.3.6, 5.1.3.1.17 |
| ICN-LBL-05 | Fixed-text labels should accompany non-standard icons permanently. | should | S | 5.17.3.1.3.6 |
| ICN-LBL-06 | The label text shall be explicit, in plain language, short and in the user's language; it shall not require translation by a code sheet. | shall | S / D | 5.1.2.1.6.14, 5.19.5.4.5.c, 5.7.2.2.2 |
| ICN-LBL-07 | An alert/warning symbol shall be accompanied by text that describes the hazard or condition; for formal warnings: the hazard, how to avoid it, and the consequences. | shall | S | 5.7.2.1.2, 5.7.3.2 |
| ICN-LBL-08 | Warning-type messages shall use the signal word DANGER, WARNING, CAUTION or NOTICE according to severity, together with the corresponding symbol. | shall | S | 5.7.2.5, 5.7.2.5.1–5.7.2.5.4 |
| ICN-LBL-09 | The accessible name of every icon (text alternative for assistive technology) shall equal or be consistent with its visible label; decorative icons shall be hidden from assistive technology. | shall | D | 5.1.3.12.8.3, 4.5.2 |

### 3.4 Consistency (CON)

| ID | Requirement | Level | Basis | Source |
|---|---|---|---|---|
| ICN-CON-01 | All coding, including icon coding, shall be consistent throughout the system. | shall | S | 5.17.24.5 |
| ICN-CON-02 | Icons shall be used consistently within the application. | shall | S | 5.17.3.2.17.1, 5.17.3.2.17.2 |
| ICN-CON-03 | Nomenclature and symbology shall be common to all displays and display formats. | shall | S | 5.1.2.2.1.5.2 |
| ICN-CON-04 | An **icon register** shall list each icon with: identifier, meaning, label (per language), allowed contexts, size variants, color/state variants and source (library or custom). It is the single reference for tests. | shall | D | 5.17.24.5, 5.1.2.2.1.5.2 |
| ICN-CON-05 | For users working on several devices, labels, color schemes and dialog appearance (including icons) should be the same across devices. | should | S | 5.19.5.1.2.a |
| ICN-CON-06 | For several platforms, established conventions of each platform should be followed while keeping appearance and function consistent. | should | S | 5.19.5.1.6.1, 5.19.5.1.6.4 |
| ICN-CON-07 | Control-to-display relationships shall be apparent through proximity, grouping, coding, framing and labeling; an icon shall be placed close to the label or object it refers to. | shall | S / D | 5.1.2.1.1, 5.1.2.1.3 |
| ICN-CON-08 | Icon variants of the same family (stroke weight, corner style, fill style, optical size, perspective) shall be uniform. | should | D | 5.17.24.4, 5.17.24.5 |

### 3.5 Size, geometry and touch targets (SIZ)

| ID | Requirement | Level | Basis | Source |
|---|---|---|---|---|
| ICN-SIZ-01 | The height of pictorial symbols shall subtend not less than **2.9 mrad (10 arcmin)** of visual angle from the greatest anticipated viewing distance, and should subtend not less than **4.5 mrad (15 arcmin)**. | shall / should | S | 5.17.18.2 |
| ICN-SIZ-02 | Text or graphics embedded in a symbol (e.g., a letter or number inside an icon) shall subtend not less than **2.9 mrad** from the greatest anticipated viewing distance. | shall | S | 5.17.18.6 |
| ICN-SIZ-03 | When accurate color perception is required, the major dimension of an isolated large symbol shall subtend ≥ **8.7 mrad (30 arcmin)** and should subtend ≥ **13.1 mrad (45 arcmin)**. | shall / should | S | 5.17.25.13 |
| ICN-SIZ-04 | When accurate color perception is required, the height of small symbols and characters should subtend ≥ **5.8 mrad (20 arcmin)**. | should | S | 5.17.25.14 |
| ICN-SIZ-05 | Symbol/text characters of visual warning and caution signals shall have a height between **8.7 and 17.4 mrad (30–60 arcmin)** from the longest anticipated viewing distance, using the larger size where conditions may be adverse. | shall | S | 5.7.3.6 |
| ICN-SIZ-06 | Size coding of icons: at most **three** size levels; the larger size shall be ≥ **150 %** of the smaller in its major dimension. | shall | S | 5.17.28 |
| ICN-SIZ-07 | The touch actuation area of an interactive icon (button) shall be ≥ **15 × 15 mm** (preferred 16 × 16 mm for keyboard keys) and not larger than **38 × 38 mm**; gloved use adds 5 mm to each dimension of the actuation area. The visible glyph may be smaller than the actuation area, which shall remain fully active. | shall | S / D | 5.1.3.1.17 |
| ICN-SIZ-08 | Separation between adjacent interactive icons shall be ≥ **3 mm** (≥ **5 mm** with the "first contact" strategy) and ≤ **6 mm**. | shall | S | 5.1.3.1.17 |
| ICN-SIZ-09 | Complex shapes shall remain legible at the smallest displayed size; details that do not meet ICN-SIZ-01 at that size shall be removed (simplified variant) rather than shrunk. | shall | D | 5.17.18.6, 5.17.24.4 |
| ICN-SIZ-10 | Icon sizes shall scale with the display (use physical units or the `--px-per-mm` / `--viewing-distance-mm` configuration) so that the angular sizes above are met on every supported resolution. | shall | D | 5.17.18.2, 5.2.2.10.2 |
| ICN-SIZ-11 | Content density shall be minimized; icons shall not be crowded in a bar. | should | S | 5.19.5.5.4, 5.17.3.1.3.3 |

### 3.6 Color, contrast and redundant coding (COL)

| ID | Requirement | Level | Basis | Source |
|---|---|---|---|---|
| ICN-COL-01 | If color coding is used it shall be redundant to another form of coding (shape, label, position, pattern). | shall | S | 5.1.3.1.16, 5.17.25.11, 5.19.5.5.7.b |
| ICN-COL-02 | Colors used for function shall accommodate users with color-deficient vision; non-confusable colors should be used where possible. | shall / should | S | 4.14, 5.17.25.10 |
| ICN-COL-03 | Colored symbols shall differ from their background by ≥ **100 ΔE** (CIE L\*u\*v\*, formula 5.17.25.16.1 in the extract). | shall | S | 5.17.25.16.1 |
| ICN-COL-04 | Colors within a set shall differ from each other by ≥ **20 ΔE** (CIE L\*u\*v\*). | shall | S | 5.17.25.16.2 |
| ICN-COL-05 | Color-filled symbols shall be used instead of outlined symbols to enhance detectability and discriminability. | shall | S | 5.17.25.15 |
| ICN-COL-06 | Text embedded in or next to an icon: character-to-background contrast ratio ≥ **6:1** (≥ 10:1 preferred); where the more specific text rule is applied: ≥ 4.5:1 for text ≤ 14 pt and ≥ 3:1 for larger text. The stricter value (6:1) governs unless the program documents an approved deviation. | shall | S / D | 5.2.2.7, 5.17.25.16.3 |
| ICN-COL-07 | Dark/black symbols and text shall be on a light background in normal lighting; light symbols on a dark background in dark conditions. A theme (day/night) switch shall keep all icons conformant in both themes. | shall | S / D | 5.17.25.16.3, 5.17.25.16.4, 5.2.2.6 |
| ICN-COL-08 | A maximum of **11 nameable colors** shall be used to distinguish categories of information. | shall | S | 5.17.25.5 |
| ICN-COL-09 | Color meaning shall follow common associations (see section 5): warm colors (red/orange) for action or response required; cool colors (blue/green) for infrequent information or background status. | shall | S | 5.17.25.8, 5.17.25.8.1, 5.17.25.8.2 |
| ICN-COL-10 | Colors used to group icons with no meaning beyond grouping should be discriminable. | should | S | 5.17.25.6 |
| ICN-COL-11 | Color shall be used consistently across displays and, where appropriate, across other systems used by the same users. | shall | S | 5.17.25.3 |
| ICN-COL-12 | Color shall not be relied on to attract attention outside the optimum visual field (e.g., a small colored icon in the periphery must be supported by position/flash/size). | shall | S | 5.17.25.2 |
| ICN-COL-13 | User color customization of icons is allowed only for information that is not safety/mission significant (alerts, warnings and status icons shall not be user-recolorable). | shall | S | 5.17.25.4 |
| ICN-COL-14 | Brightness coding of icons: at most two levels separated by at least 2:1, used only to distinguish an item from adjacent items. | shall / should | S | 5.17.26.1, 5.17.26.2 |
| ICN-COL-15 | Brightness inversion (reverse video) used for alerts shall be reserved for that purpose. | shall | S | 5.17.26.3 |
| ICN-COL-16 | Saturation may indicate intensity (e.g., more saturated red for greater danger). | may | S | 5.17.25.7 |
| ICN-COL-17 | Selected items shall be clearly indicated by color, background, text change or other visual mechanism (selected-state variant of the icon). | shall | S | 5.19.5.5.2, 5.17.9.11 |

### 3.7 Status, alert and priority icons (ALR)

| ID | Requirement | Level | Basis | Source |
|---|---|---|---|---|
| ICN-ALR-01 | Symbols used to signal critical conditions shall be used **only** for that purpose (never as decoration or for ordinary commands). | shall | S | 5.17.31.3 |
| ICN-ALR-02 | Danger-signal symbols shall have a specific meaning in the operational area and shall be conspicuously different from the surrounding content. | shall | S | 5.7.3.2.2.1, 5.7.3.2.2.2 |
| ICN-ALR-03 | Visual signal priority shall be established with discriminating characteristics: flashing, color, shape, symbols, color contrast, size, luminance contrast and location. | shall | S | 5.7.3.5 |
| ICN-ALR-04 | Warning signals shall be flashing red at **3–5 Hz**, **50 % duty cycle**; all warning signals shall be synchronized. | shall | S | 5.7.3.5.1, 5.7.3.5.1.2 |
| ICN-ALR-05 | Caution signals shall be **yellow**; if they flash, the rate shall be **≤ 2 Hz** with about **70 % ON**. | shall | S | 5.7.3.5.2, 5.7.3.5.2.1 |
| ICN-ALR-06 | Caution signals shall employ **at least two** discriminating characteristics (e.g., yellow + triangular shape + label). | shall | S | 5.7.3.5.2.2 |
| ICN-ALR-07 | Warning signals shall be coded so they are easily distinguished from caution signals (distinct shape *and* color *and* label). | shall | S | 5.7.3.5.1.3 |
| ICN-ALR-08 | Advisory/notice symbols use cyan, dark (navy) blue or white; blue also means safe/guarded threat and green normal/safe (Table XL, "alarm, alert, threat meaning"). | shall | S | 5.17.25.8, Table XL |
| ICN-ALR-09 | Additional warnings shall be indicated by redundant coding. | shall | S | 5.7.3.9 |
| ICN-ALR-10 | Warning/caution icons shall be co-located with the information needed to respond; warnings and cautions shall be grouped separately in a list, and the list shall be sortable by priority, chronology or recency. | shall | S | 5.7.3.7, 5.7.3.7.1, 5.7.3.7.2 |
| ICN-ALR-11 | Master warning/caution/advisory symbols for an entire subsystem shall be set apart from symbols for individual component status. | shall | S | 5.7.3.8 |
| ICN-ALR-12 | Visual signals shall be integrated with signals in other modalities (e.g., audio). Icon alert state shall not be the only means of notifying a critical condition. | shall | S / D | 5.7.3.4, 5.17.7.2.2 |
| ICN-ALR-13 | The design shall minimize false and nuisance alerts: an alert icon shall be displayed only when the condition exists. | should | S | 5.7.3.10 |
| ICN-ALR-14 | An icon showing **incomplete, missing, unreliable or invalid data**, or use of backup data, shall be provided; color coding should show the data status. | shall / should | S | 5.12.3.4.15.1 |
| ICN-ALR-15 | The currently selected operating mode shall be indicated clearly at all times (an icon may be used only if paired with the mode name). | shall | S / D | 4.13.4, 5.1.2.1.6.2 |
| ICN-ALR-16 | An icon shall indicate which station currently has control when remote/local or normal/backup control exists (where applicable). | shall | S | 4.6.2 |
| ICN-ALR-17 | Text that must be read shall not flash; an adjacent flashing symbol, flashing background or alternating intensity may be used for emphasis. | shall / may | S | 5.17.27.5, 5.7.3.5.1.1 |

### 3.8 Animation and flashing (ANI)

| ID | Requirement | Level | Basis | Source |
|---|---|---|---|---|
| ICN-ANI-01 | Flash coding should be used only for mission-critical events. | should | S | 5.17.27.1 |
| ICN-ANI-02 | Flash: duty cycle ON ≥ OFF (50 % preferred). | shall | S | 5.17.27.2 |
| ICN-ANI-03 | At most **two** flash rates; they shall differ by ≥ **2.0 Hz**; the higher ≤ **5.0 Hz**, the slower ≥ **0.8 Hz**; the higher rate shall mean the more critical information. | shall | S | 5.17.27.3, 5.17.27.3.1–5.17.27.3.4 |
| ICN-ANI-04 | Items flashing at the same rate shall be synchronized (one common timer/animation clock). | shall | S | 5.17.27.4, 5.7.3.5.1.2 |
| ICN-ANI-05 | Only a small area of the display should flash at any time. | should | S | 5.17.27.7 |
| ICN-ANI-06 | A means of acknowledging the event / suppressing the flash shall be provided. | shall | S | 5.17.27.6 |
| ICN-ANI-07 | Flashing icons shall respect the user/system setting "reduced motion" by switching to a static high-salience variant (e.g., thicker frame + label) **without loss of priority coding**; flashing shall be limited to the rates above. | should | D | 5.17.24.4, 5.19.5.1.3 |
| ICN-ANI-08 | Animated icons shall not block the user and shall stop when the condition ends. | shall | D | 5.1.2.1.6.13, 5.7.3.10 |

> **Note (outside the standard):** flashing at 3–5 Hz can overlap the frequency range that general web accessibility guidance associates with photosensitivity risk. Where both apply, limit the flashing area to a small element, and provide the ICN-ANI-07 fallback. The standard-based flash rates remain the reference for this specification.

### 3.9 Wait, process and feedback icons (PRC)

| ID | Requirement | Level | Basis | Source |
|---|---|---|---|---|
| ICN-PRC-01 | When the user must wait, an appropriate message **or icon** shall be displayed until interaction is possible again. | shall | S | 5.1.2.1.6.8, 5.17.9.9 |
| ICN-PRC-02 | If the delay is expected to exceed **1 s**, a status message shall accompany the wait icon. | shall | S | 5.1.2.1.6.8.1, 5.17.9.9.1, 5.17.9.5.3 |
| ICN-PRC-03 | If the delay exceeds **10 s**, a progress indicator (or countdown of remaining time) shall be provided. A spinner without remaining-time or progress information does not satisfy this. | shall | S | 5.1.2.1.6.8.2, 5.17.9.9.2 |
| ICN-PRC-04 | The wait icon shall be accompanied by a way to abort a time-consuming process. | shall | S | 5.1.2.1.6.7, 5.17.9.8 |
| ICN-PRC-05 | Process outcome (completed or aborted) shall be positively indicated; the indication shall not be only an icon without text when the outcome requires user action. | shall | S / D | 5.17.9.12, 5.17.9.13 |
| ICN-PRC-06 | When a command is time-consuming or resource-intensive, a warning shall be provided **before** it is invoked. | shall | S | 5.1.2.1.6.6, 5.17.9.7 |
| ICN-PRC-07 | When input is locked out, an alert shall indicate it; when input is available again a signal shall be presented (e.g., the cursor/pointer icon returns to its normal shape). | shall | S | 5.1.3.12.1.3.4, 5.1.3.12.1.3.5 |
| ICN-PRC-08 | Feedback shall indicate unambiguously that the control was actuated, that the desired response is reached, and when it is complete (e.g., pressed state, then confirmation icon). | shall | S | 5.1.1.8.2, 5.1.3.12.1.9.3 |
| ICN-PRC-09 | If a user input is rejected, the reason and the corrective action shall be given in text; an error icon alone is insufficient. | shall | S | 5.17.9.10, 5.1.2.1.6.11 |
| ICN-PRC-10 | Feedback within **0.2 s** for error indication and within the limits of Table V for other responses. | shall | S | Table V, 5.17.9.5 |
| ICN-PRC-11 | If feedback is not presented visually, an alternative form (e.g., sound) shall be provided; conversely, icon feedback alone shall not be the only channel for critical feedback. | shall | S | 5.17.9.4, 5.1.2.1.6.3 |

### 3.10 Toolbars, menus and navigation (NAV)

| ID | Requirement | Level | Basis | Source |
|---|---|---|---|---|
| ICN-NAV-01 | Toolbars may be used to present command actions graphically. | may | S | 5.17.3.1.3.1 |
| ICN-NAV-02 | Toolbars should be used in primary windows and should contain a **small number** of icons as immediate commands. | should | S | 5.17.3.1.3.2 |
| ICN-NAV-03 | Toolbars should **not** be used in secondary windows, with text-only commands, with crowded icons, or for non-immediate commands (e.g., configuring a filter). | should not | S | 5.17.3.1.3.3 |
| ICN-NAV-04 | Fixed toolbar icons should be limited to commands used most frequently by a large percentage of users; alternatively users may configure the toolbar. | should / may | S | 5.17.3.1.3.4 |
| ICN-NAV-05 | Toolbar icons shall provide labels via tooltips (see ICN-LBL-02, ICN-LBL-04). | shall | S | 5.17.3.1.3.5 |
| ICN-NAV-06 | A submenu indicator (e.g., a right-pointing triangle) should be used to show the presence of a submenu. | should | S | 5.17.3.1.2.4 |
| ICN-NAV-07 | Unavailable options shall be removed or greyed out; the "greyed" state of an icon (grey = inactive/unavailable) shall be visually distinct, and shall also be exposed programmatically. | shall | S / D | 5.17.3.2.3, Table XL |
| ICN-NAV-08 | A direct function call shall be provided for frequently used options; the shortcut shall be shown beside the option (icon + label + shortcut). | shall | S | 5.17.3.2.9, 5.17.3.2.11 |
| ICN-NAV-09 | Menu options in text shall be presented on separate lines; an icon shall not replace the text label of an option in a text menu. | should | S / D | 5.17.3.2.8, 5.1.3.12.8.3 |
| ICN-NAV-10 | "Back", "Home" and equivalent basic navigation controls shall always be available and their icons shall be standard and labeled. | shall | S / D | 5.19.5.5.3, 5.17.3.2.5 |
| ICN-NAV-11 | Scrolling markers shall be provided when content cannot be displayed in one screen. | shall | S | 5.19.5.5.5.3, 5.2.2.10.4.2 |
| ICN-NAV-12 | Critical commands shall be isolated from frequent commands and require extended attention (confirmation); their icons shall not be easily activated by accident. | shall | S | 5.1.3.12.1.5, 5.1.3.12.1.6, 5.19.1.9.1 |
| ICN-NAV-13 | The current position in a sequence or hierarchy shall be indicated at all times. | shall | S | 5.1.3.12.1.10.2 |

### 3.11 Pointer, connection and device-status icons (STA)

| ID | Requirement | Level | Basis | Source |
|---|---|---|---|---|
| ICN-STA-01 | When precise positioning on a graphic display is required, the current position shall be shown by a distinctive cursor symbol that indicates a point (e.g., crosshairs). | shall | S | 5.17.5.3 |
| ICN-STA-02 | An indication of connection status (strength or reliability) should be provided. | should | S | 5.19.2.5.2 |
| ICN-STA-03 | An indication of audible-alert status (e.g., an icon showing silent mode) should be provided. | should | S | 5.19.1.11.3 |
| ICN-STA-04 | System status information on operational modes and availability shall always be available (an icon plus text). | shall | S | 5.1.2.1.6.2, 5.17.9.2 |
| ICN-STA-05 | When a status icon has more than two states (e.g., connection strength), states shall be discriminable by shape/level as well as color. | shall | D | 5.1.3.1.16, 5.17.25.11 |
| ICN-STA-06 | Graphical symbols on charts/maps shall be selectable as attributes (plotting symbol) by the user when graphic data entry is required, and a reference level/legend shall be displayed when comparison is needed. | shall | S | 5.17.21.1.1, 5.17.21.2.3 |
| ICN-STA-07 | A legend explaining map/chart symbols shall be available. | should | D | 5.17.31.2, 5.17.22.4.4 |

### 3.12 Cultural and linguistic suitability (LOC)

| ID | Requirement | Level | Basis | Source |
|---|---|---|---|---|
| ICN-LOC-01 | Icon design shall be consistent with applicable cultural and ethnic variables to ensure comprehension and to avoid potential offense (mandatory when the user group includes foreign users). | shall | S | 5.1.3.12.8.2 |
| ICN-LOC-02 | Icons shall not contain text embedded as image; embedded text, where unavoidable, shall be localizable. | shall | D | 5.1.3.12.8.2, 5.17.18.6 |
| ICN-LOC-03 | Icons with directional meaning (arrows, "back", progress) shall be mirrored for right-to-left languages where the standard convention requires it; icons based on culture-specific gestures, animals, hand signs, mail boxes, etc., shall be reviewed for the target population. | shall | D | 5.1.3.12.8.2 |
| ICN-LOC-04 | Label text shall be provided for each supported language and fit the layout (no truncation of the label that hides its meaning). | shall | D | 5.7.2.2.3, 5.19.5.4.5.c |
| ICN-LOC-05 | A comprehension test with representative users should be executed for any new or ambiguous icon. | should | D | 5.17.31.2, 5.1.3.12.8.2 |

### 3.13 Mobile and responsive use (MOB)

| ID | Requirement | Level | Basis | Source |
|---|---|---|---|---|
| ICN-MOB-01 | When adapting an existing desktop design to a small screen, the design shall be **adapted**, not copied: icons shall be re-prioritized and re-sized for the smaller display. | shall | S | 5.19.5.1.1, 5.2.2.10.2 |
| ICN-MOB-02 | Textual information should be minimized; icons with labels may substitute long text if ICN-LBL-04 is met. | should | S / D | 5.19.5.1.8, 5.19.5.5.4.c |
| ICN-MOB-03 | Critical information (and its icon) should be reachable within **two key actions** at most. | should | S | 5.19.5.4.1 |
| ICN-MOB-04 | Icon appearance and meaning shall remain the same at different orientations; user-selectable auto-rotation shall not change the meaning or the position of safety-critical icons. | shall | S / D | 5.19.5.4.4, 5.17.24.5 |
| ICN-MOB-05 | Display brightness/contrast and text size should be user-adjustable with minimal steps; icons shall remain conformant at every setting (ICN-SIZ, ICN-COL). | should | S | 5.19.5.1.3, 5.2.2.7.c |
| ICN-MOB-06 | Accidental activation shall be avoided; destructive or critical icon buttons shall require a distinct sequence of actions. | shall | S | 5.19.1.9.1, 5.1.3.12.1.6 |

---

## 4. Numeric design values

### 4.1 Visual-angle thresholds converted to on-screen size

Height *h* of a symbol subtending a visual angle θ at viewing distance *d*: `h = 2 · d · tan(θ/2) ≈ d · θ` (θ in radians).

The following table assumes an **example viewing distance d = 500 mm** and the default `--px-per-mm = 3.78` (96 dpi reference) of the companion document. **Both values shall be replaced by the program's real values** (`--viewing-distance-mm`, `--px-per-mm`); for larger distances all sizes grow proportionally.

| Requirement | Angle | h at 500 mm | h in CSS px (3.78 px/mm) | Source |
|---|---|---|---|---|
| Minimum pictorial symbol / embedded text (shall) | 2.9 mrad (10′) | 1.45 mm | ≈ 5.5 px | 5.17.18.2, 5.17.18.6 |
| Recommended pictorial symbol (should) | 4.5 mrad (15′) | 2.25 mm | ≈ 8.5 px | 5.17.18.2 |
| Small color-critical symbol (should) | 5.8 mrad (20′) | 2.90 mm | ≈ 11 px | 5.17.25.14 |
| Isolated large color-critical symbol (shall) | 8.7 mrad (30′) | 4.35 mm | ≈ 16.4 px | 5.17.25.13 |
| Isolated large color-critical symbol (should) | 13.1 mrad (45′) | 6.55 mm | ≈ 24.8 px | 5.17.25.13 |
| Warning/caution symbol, range | 8.7–17.4 mrad (30–60′) | 4.35–8.7 mm | ≈ 16.4–32.9 px | 5.7.3.6 |

> **Design consequence:** the perceptual thresholds are small compared with the touch rules. For *interactive* icons the **actuation area of 15 × 15 mm (≈ 57 × 57 CSS px at 3.78 px/mm)** is the governing size; the visible glyph is normally 6–10 mm (≈ 24–38 px) inside it, which exceeds every angular threshold above at 500 mm. Status and warning icons that are not interactive are governed by the angular thresholds.

### 4.2 Touch-target values for interactive icons (Table "figure 3", 5.1.3.1.17)

| Parameter | Minimum | Maximum | Notes |
|---|---|---|---|
| Actuation area (icon buttons) | 15 × 15 mm | 38 × 38 mm | Gloved: + 5 mm on each dimension |
| Actuation area (keyboard keys) | – | – | 16 × 16 mm preferred |
| Separation (icon buttons) | 3 mm (last contact) / 5 mm (first contact) | 6 mm | – |
| Separation (keyboard keys) | 0 | 6 mm | 2 mm preferred |

### 4.3 Contrast and color-difference values

| Item | Value | Source |
|---|---|---|
| Colored symbol vs background | ΔE(Y u′ v′) ≥ **100** | 5.17.25.16.1 |
| Colors within a set | ΔE(L\*u\*v\*) ≥ **20** | 5.17.25.16.2 |
| Characters vs background | ≥ **6:1** (10:1 preferred) | 5.2.2.7 |
| Text ≤ 14 pt / > 14 pt on its background | > 4.5:1 / ≥ 3:1 | 5.17.25.16.3 |
| Nameable colors for categories | ≤ **11** | 5.17.25.5 |
| Brightness levels | ≤ 2, ratio ≥ 2:1 | 5.17.26.2 |
| Size levels | ≤ 3, larger ≥ 150 % of smaller | 5.17.28 |

> **Verification note:** ΔE(Y u′ v′) and ΔE(L\*u\*v\*) are computed from the *actual rendered colors* (sRGB → XYZ → Yu′v′/L\*u\*v\*) with the formulas reproduced in the extract (5.17.25.16.1, 5.17.25.16.2) and the display white as reference. A WCAG-style contrast ratio does not replace the ΔE test; both shall be reported.

### 4.4 Timing and flashing values

| Item | Value | Source |
|---|---|---|
| Warning flash | red, 3–5 Hz, 50 % duty | 5.7.3.5.1 |
| Caution flash (if used) | yellow, ≤ 2 Hz, ≈ 70 % ON | 5.7.3.5.2, 5.7.3.5.2.1 |
| General flash rates | ≤ 2 rates, Δ ≥ 2.0 Hz, 0.8 Hz ≤ rate ≤ 5.0 Hz | 5.17.27.3 |
| Wait icon + status message | delay > 1 s | 5.17.9.9.1 |
| Progress indicator / countdown | delay > 10 s | 5.17.9.9.2, 5.1.2.1.6.8.2 |
| Error feedback | ≤ 0.2 s | Table V |

---

## 5. Color semantics for icons (Table XL of the standard, alarm / equipment / general meanings)

| Color | Meaning to use for icons | Typical icon use | Note |
|---|---|---|---|
| **Red** | Critical consequences, danger, emergency, alarm, failure, stop, OFF | Warning, fault, stop, delete-confirm | Warning signal color (flashing 3–5 Hz) |
| **Orange** | Alarm/alert/hazard, high threat | Alert (if the program needs a level between red and yellow) | Not defined for caution; caution is yellow |
| **Yellow** | Warning, caution, abnormal state, delay, check or recheck | Caution, degraded state | Caution signal color (mandatory) |
| **Green** | Normal, safe, ready, ON, in tolerance, acceptable | OK, running, connected | Never as sole indicator |
| **Blue / Cyan** | Advisory, guarded threat, non-critical, cool | Information, help | Cool colors for infrequent / background information |
| **White** | Advisory, action in progress, empty | Notice, in-progress | – |
| **Magenta** | Alarm state, radiation hazard | Only for those meanings | – |
| **Gray** | Inactive or unavailable options or actions | Disabled icon | Disabled icons: 5.17.3.2.3 |

Additional rules: warm colors for action/response required, cool colors for background/infrequent status (5.17.25.8.1, 5.17.25.8.2); every color meaning above is supplemented by a second code (ICN-COL-01).

---

## 6. Suggested implementation guidance (non-normative)

The standard does not prescribe technology. For consistency with the companion document, the following are the recommended ways to meet the requirements above on HTML/CSS/JS:

| Topic | Guidance | Related IDs |
|---|---|---|
| Format | Inline **SVG** (or an SVG sprite) filled with `fill="currentColor"`; avoid raster icons and icon fonts that fail when fonts are not loaded. Filled glyphs by default. | ICN-COL-05, ICN-SIZ-10 |
| Size tokens | CSS custom properties derived from `--px-per-mm` and `--viewing-distance-mm`: `--icon-min`, `--icon-rec`, `--icon-status`, `--icon-warning-min/max`; touch actuation area with `--touch-target-min`. | ICN-SIZ-01…10 |
| Interactive icon | `<button type="button" class="icon-btn">` with `<svg aria-hidden="true">` and a text label in a `<span class="icon-label">` (visible or visually hidden with a visible alternative on long-press/focus); `title`/tooltip only as an addition. | ICN-LBL-01…05, ICN-LBL-09 |
| Non-interactive status icon | `<svg role="img" aria-label="…">` with adjacent text; pure decoration `aria-hidden="true"`. | ICN-LBL-09, ICN-ALR-03 |
| Disabled | `disabled` / `aria-disabled="true"` plus grey token and a different shape treatment (e.g., hatching); do not depend on color only. | ICN-NAV-07, ICN-COL-01 |
| Alert priority | `data-priority="warning|caution|advisory|normal"` selects color, shape, label and flash class from one theme file; flash via a shared CSS animation clock so that all warnings are synchronized. | ICN-ALR-03…07, ICN-ANI-04 |
| Reduced motion | `@media (prefers-reduced-motion: reduce)` replaces animation by a static emphasized variant. | ICN-ANI-07 |
| Themes | Day/night themes redefine icon color tokens; tests execute for both. | ICN-COL-07 |
| Wait indicator | `<progress>`/`role="progressbar"` with `aria-valuenow` for > 10 s, `role="status"` text for > 1 s, abort button always present. | ICN-PRC-01…04 |
| Register | A machine-readable icon register (JSON) is the source for the build, the localization of labels and the tests. | ICN-CON-04 |

---

## 7. Verification checklist

| # | Check | Method | IDs |
|---|---|---|---|
| 1 | Every icon exists in the icon register with meaning, label per language and contexts. | Inspection | ICN-CON-04, ICN-SEM-08 |
| 2 | Every icon command has a visible or touch-available label; hover-only labels are absent. | Inspection / touch test | ICN-LBL-01…05 |
| 3 | No function is reachable only through an unlabeled icon. | Inspection | ICN-USE-03 |
| 4 | Angular size of each icon class is computed with real viewing distance and pixel density and is ≥ thresholds. | Analysis | ICN-SIZ-01…05 |
| 5 | Actuation area ≥ 15 × 15 mm and spacing ≥ 3 mm (5 mm for first contact) at all resolutions (800×600 and above). | Measurement on the device | ICN-SIZ-07, 08, 10 |
| 6 | ΔE tests for each icon/background and color-set pair, both themes. | Analysis (script) | ICN-COL-03, 04, 07 |
| 7 | Grayscale / color-vision-deficiency simulation: every icon is still distinguishable. | Test | ICN-COL-01, 02, STA-05 |
| 8 | Icons for warning/caution/advisory/normal differ by color **and** shape **and** label. | Inspection | ICN-ALR-06, 07 |
| 9 | Flash: frequency, duty cycle, synchronization measured; text does not flash; acknowledge control present. | Instrumented test | ICN-ALR-04, 05, ICN-ANI-02…06 |
| 10 | Wait states: simulate 0.5 s, 2 s and 15 s operations; verify icon/message/progress/abort. | Test | ICN-PRC-01…04 |
| 11 | Critical-condition symbols appear nowhere else in the UI. | Inspection (search in register) | ICN-ALR-01 |
| 12 | Cultural/linguistic review and comprehension test with representative users. | Review / usability test | ICN-LOC-01…05 |
| 13 | Small screen/orientation: icon sets are adapted, still conformant, critical items ≤ 2 actions. | Test | ICN-MOB-01…06 |

---

## 8. Traceability matrix (source paragraph → icon requirement)

| Source paragraph | Icon requirement IDs |
|---|---|
| 4.6.2 | ICN-ALR-16 |
| 4.13.4 | ICN-ALR-15 |
| 4.14 | ICN-COL-02 |
| 5.1.1.8.2 | ICN-PRC-08 |
| 5.1.2.1.1, 5.1.2.1.3 | ICN-CON-07, ICN-SEM-08 |
| 5.1.2.1.6.8 (.1, .2), 5.17.9.9 (.1, .2) | ICN-PRC-01, 02, 03 |
| 5.1.2.2.1.5.2 | ICN-CON-03 |
| 5.1.3.1.16 | ICN-COL-01, ICN-STA-05 |
| 5.1.3.1.17 | ICN-SIZ-07, ICN-SIZ-08 |
| 5.1.3.12.1.3.4, 5.1.3.12.1.3.5 | ICN-PRC-07 |
| 5.1.3.12.8.1, 5.1.3.12.8.2 | ICN-USE-02, ICN-LOC-01 |
| 5.1.3.12.8.3 | ICN-LBL-01 |
| 5.2.2.7 | ICN-COL-06 |
| 5.7.2.2.3 | ICN-SEM-06, ICN-LOC-04 |
| 5.7.2.5 | ICN-LBL-08 |
| 5.7.3.2.2.1, 5.7.3.2.2.2 | ICN-ALR-02 |
| 5.7.3.5 – 5.7.3.5.2.2 | ICN-ALR-03…07 |
| 5.7.3.6 | ICN-SIZ-05 |
| 5.7.3.7, 5.7.3.8, 5.7.3.9, 5.7.3.10 | ICN-ALR-09…13 |
| 5.12.3.4.15.1 | ICN-ALR-14 |
| 5.17.3.1.2.4 | ICN-NAV-06 |
| 5.17.3.1.3.1 – 5.17.3.1.3.6 | ICN-NAV-01…05, ICN-LBL-02, ICN-SEM-02 |
| 5.17.3.2.3 | ICN-NAV-07 |
| 5.17.3.2.16 | ICN-LBL-03 |
| 5.17.3.2.17 – 5.17.3.2.17.2 | ICN-USE-01, ICN-SEM-03, ICN-CON-02 |
| 5.17.5.3 | ICN-STA-01 |
| 5.17.18.2, 5.17.18.6 | ICN-SIZ-01, ICN-SIZ-02 |
| 5.17.24.1 – 5.17.24.6 | ICN-USE-06…08, ICN-CON-01 |
| 5.17.25.1 – 5.17.25.16.4 | ICN-COL-01…13, ICN-COL-17 |
| 5.17.26 | ICN-COL-14, ICN-COL-15 |
| 5.17.27 | ICN-ANI-01…06, ICN-ALR-17 |
| 5.17.28 | ICN-SIZ-06 |
| 5.17.31, 5.17.32 | ICN-SEM-01, ICN-SEM-07, ICN-USE-04, 05, ICN-ALR-01 |
| 5.19.1.11.3, 5.19.2.5.2 | ICN-STA-03, ICN-STA-02 |
| 5.19.5.1.1 – 5.19.5.1.6.4 | ICN-MOB-01, ICN-CON-05, ICN-CON-06, ICN-SEM-05 |
| 5.19.5.4.1, 5.19.5.4.4, 5.19.5.5.2, 5.19.5.5.7 | ICN-MOB-03, ICN-MOB-04, ICN-COL-17, ICN-COL-01 |

---

## 9. Limitations and open points

1. **No icon grid or style rules.** The standard gives no stroke, grid, corner or file-format requirements; ICN-CON-08 and section 6 are project-level choices.
2. **Viewing distance and pixel density are not in the standard.** Section 4.1 uses 500 mm / 3.78 px·mm⁻¹ only as an example; the program shall declare real values.
3. **Contrast thresholds overlap.** 5.2.2.7 (6:1) and 5.17.25.16.3 (4.5:1 / 3:1) are both present in the extract; this document applies the stricter value for text associated with icons (ICN-COL-06). The program may record a documented deviation.
4. **Military environment wording.** Terms such as "mission-critical", "tactically significant" and "foreign military personnel" shall be interpreted for the application domain and user roles.
5. **No requirement about the specific design of the individual icons** (e.g., what a "save" icon looks like): conformance is obtained by using established conventions (ICN-SEM-01, ICN-SEM-05) and by user comprehension testing (ICN-LOC-05).
