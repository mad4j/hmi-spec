# MIL-STD-1472H – Requirements Applicable to a Web HMI (Extract)

> **Source:** MIL-STD-1472H, *Department of Defense Design Criteria Standard – Human Engineering*, 15 September 2020 (included in this repository as `MIL-STD-1472H.pdf`).
> **Purpose:** English-language extract containing only the requirements that can be applied to the design of a **web-based Human-Machine Interface (HMI)**.

## How to read this document

- Requirement text is reproduced from the standard (which is written in English) with its **original paragraph number** (e.g., **5.17.4.2**) so that each item can be traced back to the PDF. Printed page numbers in the standard are offset from PDF page numbers by 17 (PDF page = printed page + 17).
- The normative language of the standard is preserved: **shall** = mandatory; **should** = recommended; **may** = permitted.
- Sub-items (a., b., c., …) of a requirement are shown as nested bullets.
- A few requirements contain tables or formulas in the source; they are reproduced in the same section (or in the Appendix).
- Cross-references such as "see 5.3.10" or "table V" point to paragraphs of the original standard; where the referenced table is needed to apply the requirement it is reproduced here.
- Where the source standard uses military-environment wording (e.g., "operator", "mission-critical", "system administrator", "ISSO"), it has been kept unchanged and should be interpreted for the specific web application and its user roles.
- Where a requirement appears in two places in the standard (e.g., 5.1.3.12 and 5.17), the 5.17 version is used and only the additional/unique requirements of the duplicate are listed.

## Applicability criteria used for the selection

A requirement was **included** when it can be implemented or verified in the software user interface of a web application (browser-based UI, including when used on touch-enabled or mobile devices): dialogs, menus, forms, feedback, error handling, help, notifications, alerts, portals/portlets, information presentation, coding (including color), graphics and geospatial displays, cybersecurity interactions (login/logoff, roles, passwords), automation/supervisory displays and software safety.

A requirement was **excluded** when it concerns exclusively physical hardware, the physical environment or specific platforms, for example: mechanical controls, switches, knobs and valves; keyboard, mouse, trackball, joystick and stylus hardware; display luminance/reflection/viewing-geometry of the physical screen; workstation, workspace, anthropometry, lighting, noise, vibration; ground vehicles, ship bridges, aircraft/aircrew systems, weapons and optics; CBRNE/PPE, night-vision and blackout provisions; maintainability; physical warning labels and placards; handheld device casing, power and mounting.

---

## 1. General design principles

Overarching criteria that apply to any HMI, including a web HMI.

- **4.5.2** *Application.* Criteria identified in this standard shall be applied to the design of hardware, software, systems, equipment, and facilities in which there is a human interface or interaction including operation, maintenance, training, and support activities.
- **4.6.1** *Fail-safe design.* A fail-safe design shall be provided in those areas where failure can cause catastrophe through damage to equipment, injury to personnel, or inadvertent operation of critical equipment.
- **4.6.2** *Remote and local control.* For critical control and monitoring functions, there shall be normal and backup control functions so that the function can be executed even if normal control equipment fails or the operator that normally executes the function is incapacitated. The backup function shall be enabled to take control at any time and vice versa according to a defined control protocol (see 6.2). There shall be indication of the station that has control at both the normal and backup station.
- **4.7.1** *Equipment simplicity.* The equipment shall represent the simplest design consistent with functional requirements and expected operational conditions.
- **4.9.1** *System and personnel safety factors.* Design shall reflect applicable system and personnel safety factors. This includes minimizing potential human error in the operation and maintenance of the system, particularly under the conditions of alert, battle stress, or other emergency or non-routine conditions.
- **4.13.1** *Automation of functions.* Functions shall be automated only to attain greater overall effectiveness, efficiency, reliability, simplicity, economy, and system safety rather than relying on human performance alone.
- **4.13.2** *Human involvement.* Irrespective of the level of automation, system and task design shall ensure that the human user is in command, involved in ongoing operations, and appropriately informed to maintain awareness of the situation and other status of automated functions.
- **4.13.3** *Automated function characteristics.* When used, automated functions shall minimize automation surprise; offer the user an appropriate range of options; monitor user actions to minimize, resist, and tolerate errors; and be capable of being overridden by the user.
- **4.13.4** *Indication of operating mode.* A clear indication of the currently selected operating mode shall be provided to the user at all times.
- **4.14** *Functional use of color.* Where not in conflict with color codes specified herein, colors used for functional purposes (e.g., visual displays, controls, workspaces, and equipment connections) shall accommodate users with color-deficient vision.
- **4.16** *System integration.* The design shall be compatible with all systems the user is expected to operate, maintain, or support.
- **5.17.1.1** *Functional interface.* Computer programs and equipment interfaces shall provide a functional interface between the system for which they are designed and users of that system.
- **5.17.1.2** *Compatible with personnel.* Interfaces shall be compatible with the work performed by, and the cognitive processes of, personnel.
- **5.17.1.3** *Human performance.* The interface should minimize conditions that can degrade human performance or contribute to human error.
- **5.17.1.4** *Display content.* Equipment interfaces provide a functional interface between the system for which they are designed and users of that system. This interface should optimize compatibility with personnel and should minimize conditions that can degrade human performance or contribute to human error.


## 2. Feedback, system status and response time

System feedback and timing criteria for user interaction with the web interface. Response-time targets are those of Table V (Appendix A).

- **5.1.1.8** *Feedback.* There shall be no discernible time lag between a change in a system condition being controlled or monitored and its indication on a display.
- **5.1.1.8.1** *Unavoidable time lag.* If a time lag between control actuation and ultimate system state is unavoidable, the system shall provide immediate feedback to the user of the process and direction of parameter change.
- **5.1.1.8.2** *Feedback indication.* Feedback shall indicate (without ambiguity, uncertainty, or error) to the user that the control is properly actuated, that the desired response is achieved, and when the desired response is complete.
- **5.1.1.8.3** *Critical control functions.* Critical control functions, such as those entered by keyboard, shall provide feedback to the user prior to entry to ensure that the keyed entry is errorless and is the one that the user desires to enter.
- **5.1.2.1.1** *Relationship.* The relationships of a control to its associated display and the display to the control shall be immediately apparent and unambiguous to the user.
- **5.1.2.1.3** *Design.* Control/display relationships shall be apparent through proximity, similarity of groupings, coding, framing, labeling, and similar techniques.
- **5.1.2.1.4** *Complexity and precision.* The complexity and precision required for manipulating controls and monitoring displays shall be consistent with the precision required of the system.
- **5.1.2.1.5** *User ability.* Control/display complexity and precision shall not exceed the user’s ability to discriminate display detail or manipulate controls (in terms of manual dexterity, coordination, or reaction time) under the dynamic conditions and environment in which human performance is expected to occur.
- **5.1.2.1.6.1** *Use.* Feedback shall be provided that presents status information, confirmation, and verification of input throughout system interaction.
- **5.1.2.1.6.2** *System status.* Users shall always be provided with system status information regarding operational modes and availability, either automatically or by request as needed.
- **5.1.2.1.6.3** *Computer response.* Every input by a user shall produce a consistent, perceptible response output from the computer. In applications where the system intentionally produces no visual feedback as an indicator of invalid user input, an alternative form of feedback (e.g., different audio sound) shall be produced to ensure the invalid action is recognized by the user.
- **5.1.2.1.6.4** *System response time.* Maximum system response times for real-time systems (e.g., fire control systems or command and control systems) shall not exceed the values of table V. For teleoperations (e.g., remotely controlled systems, such as unmanned systems), the operationally acceptable response times should be specified by the acquisition program (see 6.2).
- **5.1.2.1.6.4.1** *Non-real-time systems.* Non-real-time systems may permit relaxed response times.
- **5.1.2.1.6.4.2** *Existing or predecessor systems.* System response times for real-time and non-real-time systems shall not exceed the response time of the equivalent existing or predecessor system.
- **5.1.2.1.6.4.3** *Response time greater than 1 second.* If computer response time will exceed 1 second, the user shall be given a message indicating that the system is processing. For remotely handled automated systems, see 5.12.
- **5.1.2.1.6.5** *Task performance time.* The time required to accurately complete a standard time-sensitive action or sequence of actions (including system response time) shall not exceed the time to complete the same action(s) on the equivalent existing or predecessor system.
- **5.1.2.1.6.6** *Time-consuming processes.* The system shall give warning information when a command is invoked that will be time-consuming or resource-intensive to process.
- **5.1.2.1.6.7** *Aborting time-consuming processes.* Users shall be provided with an option to abort time-consuming processes.
- **5.1.2.1.6.8** *Stand-by.* When system functioning requires the user to stand by, a message or appropriate icon shall be displayed until user interaction is possible again.
- **5.1.2.1.6.8.1** *Delays in excess of 1 second.* Where the delay exceeds 1 second, the user shall be informed.
- **5.1.2.1.6.8.2** *Delays in excess of 10 seconds.* For delays exceeding 10 seconds, a countdown display shall show the delay time remaining.
- **5.1.2.1.6.9** *Startup.* When the system is not immediately available after system startup, the system shall provide feedback to the user that indicates the average system response time or known periods of unavailability, and the keyboard and pointing device shall be disabled until startup is complete.
- **5.1.2.1.6.10** *Input confirmation.* Confirmation of inputs shall not cause displayed data to be removed.
- **5.1.2.1.6.11** *User input rejection.* If the system rejects a user input, feedback shall be provided to indicate the reason for rejection and the required corrective action.
- **5.1.2.1.6.12** *Highlighted option selection.* Any displayed message or datum selected as an option or input to the system shall be highlighted to indicate acknowledgment by the system.
- **5.1.2.1.6.13** *Process outcome.* When a control process or sequence is completed or aborted by the system, positive indication shall be presented to the user concerning the outcome of the process and the requirements for subsequent user action.
- **5.1.2.1.6.14** *Feedback message content.* Users shall not be required to translate feedback messages by use of reference system or code sheets.
- **5.1.2.1.6.15** *Abbreviations.* Abbreviations should be avoided in feedback messages unless they are well known by all potential users.
- **5.1.2.3.1** *Lack of ambiguity.* Display indicators shall clearly and unambiguously direct and guide the appropriate control response.
- **5.1.2.3.2** *Response to control movements.* The response of a display to control movements shall be consistent, predictable, and compatible with the user’s expectations.
- **5.1.2.3.3** *Display response time.* There shall be no discernible time lag between a change in a real-time system condition being controlled or monitored and its indication on a display (see 5.1.2.1.6.4).
- **5.1.2.3.4** *Time lag.* The time lag between system response to a control input and display presentation of that response shall be minimized, consistent with safe and effective system operation.
- **5.17.9.1** *Feedback.* Feedback that presents status information, confirmation, and verification of input throughout system interactions shall be provided.
- **5.17.9.2** *System status.* System status information regarding operational modes and availability shall be accessible to users, either automatically or by request as needed.
- **5.17.9.3** *Computer responses.* Every input by a user shall produce a consistent perceptible response output from the computer.
- **5.17.9.4** *Alternative form.* If the feedback is not presented visually, an alternative form of feedback (e.g., sound) shall be provided.
- **5.17.9.5** *System response time.* Maximum system response times for critical systems (e.g., fire control systems, command and control systems) shall not exceed the values provided in table V.
- **5.17.9.5.1** *Non-critical systems.* Non-critical systems may permit relaxed response times.
- **5.17.9.5.2** *Existing or predecessor systems.* System response times for critical and non-critical systems shall not exceed the response time of equivalent existing or predecessor systems.
- **5.17.9.5.3** *System processing indication.* When computer response times will exceed 1 second, the system shall provide a message indicating that the system is processing. For remotely handled automated systems, see 5.12.
- **5.17.9.6** *Task performance time.* The time required to accurately complete a standard time-sensitive action or sequence of actions (including system response times) shall not exceed the time to complete the same action(s) on equivalent existing or predecessor systems.
- **5.17.9.7** *Time-consuming processes.* The system shall provide warning information when a command is invoked that will be time-consuming or resource-intensive to process.
- **5.17.9.8** *Aborting time-consuming processes.* The system shall provide users an option to abort time-consuming processes.
- **5.17.9.9** *System processing.* When system processing requires a user to wait before taking further action, an appropriate message or icon shall be displayed until user interaction is again possible.
- **5.17.9.9.1** *Status message.* Where the delay is expected to exceed 1 second, the system shall provide a status message.
- **5.17.9.9.2** *Progress indicator.* For delays exceeding 10 seconds, the display shall provide a progress indicator.
- **5.17.9.10** *User input rejection.* If the system rejects a user input, feedback shall be provided to indicate the reason for rejection and the required corrective action.
- **5.17.9.11** *Highlighted option selection.* Any items as an option or input to the system shall be highlighted to indicate acknowledgment by the system.
- **5.17.9.12** *Process outcome.* When a control process or sequence of related actions is either completed or aborted by the system, the system shall provide an indication of the outcome to the user.
- **5.17.9.13** *Subsequent actions.* Following an aborted action, the system shall provide any requirements for subsequent user action.
- **5.17.9.14** *Feedback message content.* Feedback messages shall be explicit and informative.
- **5.17.9.14.1** *Understandable.* Feedback shall not require a reference system or codebook to be understood.
- **5.17.9.14.2** *Non-standard abbreviations.* Non-standard abbreviations should be avoided in feedback messages.


## 3. Cybersecurity: access control, authentication, logon/logoff, data protection

Human-engineering requirements for access control and user identification/authentication (apply to the web application's login, session and administration screens). *SA = system administrator; ISSO = Information Systems Security Officer; IS = information system.*

- **5.16** *Cybersecurity.* Cybersecurity considerations in human engineering are based around the DoDI 8510.01 process and include security controls based on CNSSI 1253 and NIST SP 800-53. The applicable human interaction elements contributing to security controls will be identified, analyzed, selected, and implemented through the risk management framework process. This section focuses on the human performance aspects of cyber threats in terms of access control, user identification, and authentication, which were derived in alignment with NIST SP 800-53. Related requirements may be provided for the IS, the system administrator (SA), or the user. Access control refers to selective restriction of permissions within an IS and the rules that govern the access. User identification and authentication is the process of verifying the identity of a user, process, or device through the use of unique identifiers (e.g., passwords, tokens, biometrics) as a prerequisite for granting access to resources in an IS. Human engineering implications related to other aspects of cybersecurity may be addressed in future versions of MIL-STD-1472. When National Institute of Standards and Technology requirements conflict with human-system integration requirements, it is up to the procuring activity to perform tradeoffs and assume the risk associated with the chosen course of action. It is further understood that certain tactical systems and environments will vary (e.g., there may not be a system-level Information Systems Security Officer [ISSO] present) and those factors will drive the requirements.
- **5.16.1.1** *User access.* Role-based access control shall be used to manage user access.
- **5.16.1.2** *Access notifications.* The SA and the ISSO should be provided with a notification when access authorizations are inconsistent with access restrictions.
- **5.16.1.3.1** *Linked user profiles.* If a user has more than one role within a given system, their user profile shall be linked to their multiple roles and permissions.
- **5.16.1.3.2** *User role login.* The user should be informed under which role they are logged in.
- **5.16.1.4** *Visible active accounts.* For the duration of a session, the user shall be able to see which system account is currently active.
- **5.16.1.5** *Access to roles and permissions.* The SA and ISSO shall have access to the roles and permissions matrices within the system.
- **5.16.1.6** *Separation of duties.* The SA and the ISSO should be provided with the roles and associated tasks to help maintain awareness and ensure separation and segregation of organizational duties.
- **5.16.1.7** *Automated restrictions.* Invalid logins shall trigger automated access restrictions per the system’s cybersecurity plan.
- **5.16.1.8** *Automated notifications.* The SA should be provided with an automated notification when a user’s access has been restricted by the system.
- **5.16.1.9** *Monitoring logs.* The SA shall have the capability to determine who is logged into the system in real time or in the past (via system logs).
- **5.16.1.10** *Unlock an account.* The SA shall have the capability to unlock an account. The SA should have the capability to unlock an account remotely.
- **5.16.1.11** *Create user profiles.* The SA shall have the capability to create user profiles.
- **5.16.1.12** *Access user profiles.* The SA shall have the capability to access user profiles.
- **5.16.1.13** *Edit user profiles.* The SA and the ISSO should have the capability to edit user profiles.
- **5.16.1.14** *Account setup feedback.* The SA shall be informed when the account setup process is completed.
- **5.16.1.15** *Disabled account feedback.* The SA shall be given feedback when the account has been disabled.
- **5.16.1.16** *Purged account feedback.* The SA shall be given feedback when the account has been purged.
- **5.16.2.1** *User identification.* User identification procedures shall be as simple as possible, consistent with common data protection practices.
- **5.16.2.2** *Notification message or banner.* Where required by policy and not precluded by tactical demands, the system shall support the display of notification messages or banners. These typically display privacy and security notices consistent with applicable federal laws, executive orders, directives, policies, regulations, standards, and guidance on the screen until the user makes explicit acknowledgement.
- **5.16.2.3** *Multifactor authentication.* Multifactor authentication for network and local access to accounts shall be implemented. Factors shall include at least two of the following:
  - a. A physical object in the user’s possession, such as a common access card (CAC) with a token.
  - b. Something known only to the user, such as a password or personal identification number (PIN).
  - c. Biometrics, like a fingerprint or other physical characteristics of the user that can be used by the system as a form of individual identification and access control.
- **5.16.2.4** *Viable multifactor authentication options.* The user shall be informed of the viable multifactor authentication options (e.g., CAC, password) to login to the system.
- **5.16.2.5** *Status feedback.* The user shall be provided feedback that indicates the status (e.g., accept or reject) of authentication data inputs.
- **5.16.2.6** *Password not echoed on display.* If using the username/password factor, the password should not be echoed on the display (e.g., an asterisk [*] is displayed in lieu of each character).
- **5.16.2.7** *Error message.* An error message shall be provided to the user when a user/device identifier (e.g., internet protocol [IP] address, device-unique tokens) has been selected that is not viable, and provide instruction to the user that a new identifier must be selected.
- **5.16.2.8** *User and device identifier notification.* The user shall be notified when a user and device identifier assignment has been successfully completed.
- **5.16.2.9** *User status.* The status of a requested action shall be displayed.
- **5.16.2.10** *Reason for failure feedback.* A specific reason(s) for a failed action shall be displayed.
- **5.16.2.11** *Corrective action suggestion.* A corrective action shall be suggested upon denial of access.
- **5.16.2.12** *Maximum number of concurrent sessions.* The IS maximum number of concurrent sessions for each user account should be enforced with denial of access for a request above the maximum.
- **5.16.2.13** *Concurrent session control.* The SA and the ISSO should be provided with information on all active concurrent sessions for each account.
- **5.16.2.14** *Multiple concurrent sessions.* If more than one current session per account is permitted, the user shall be informed of all of his or her active concurrent sessions.
- **5.16.3.1** *Logon procedures.* In applications where users must logon to the system, logon shall be a separate procedure that must be completed before a user is allowed to select among any operational options.
- **5.16.3.2** *Automatic logon display.* Appropriate prompts for logon shall be automatically displayed on the user’s terminal with no special action required other than turning on the terminal.
- **5.16.3.3** *Logon attempts notification.* The capability to display the logon history of accounts shall be available to the SA and ISSO.
- **5.16.3.4** *Logon metadata.* The name, account, role, date, and time for logon attempts shall be captured.
- **5.16.3.5** *Invalid logon attempts.* If the system design enforces a limit on the number of invalid login attempts, then the user shall be informed of the number of login attempts remaining before locking out the user.
- **5.16.3.6** *Logon reset request.* If the user is locked out of the system, the user shall be able to request the SA reset the number of logon attempts.
- **5.16.4.1** *Automatic logoff.* Where required by policy and not precluded by tactical demands, after a predefined period of user inactivity, the user shall be automatically logged off with no loss of data.
- **5.16.4.2** *Logoff procedures.* When a user signals for system logoff, application exit, or shutdown, the system shall:
  - a. Check pending transactions to determine if data loss seems probable and, if so, inform user that continuing will result in a potential data loss.
  - b. Prompt for confirmation before the logoff command is executed.
- **5.16.5.1** *Displayed security classification.* When displayed data are classified for security purposes, a prominent indication of security classification level shall be provided.
- **5.16.5.2** *Warnings.* System logic should be provided that will generate messages and alarm signals in order to warn users of attempted intrusion by unauthorized users.
- **5.16.6.1** *Simulated data.* When simulated data and system functions are provided (perhaps for user training), real data shall be protected.
- **5.16.6.2** *Real system use.* Real system use shall be clearly distinguished from all simulated operations.
- **5.16.6.3** *Clear indication.* In applications where either real or simulated data can be displayed, a clear indication of simulated data shall be included.
- **5.16.7.1** *Password criteria.* The criteria for an acceptable password (e.g., minimum number and types of characters) shall be displayed to the user while generating a password.
- **5.16.7.2** *Dynamic feedback.* The user shall be provided with dynamic feedback on which password complexity criteria have not been met while generating a password.
- **5.16.7.5** *Changing passwords.* Where passwords are required by policy to be changed at periodic intervals, the IS shall support the enforcement of the password change policy. Within the mandatory periodic interval, users shall be allowed to change passwords whenever they choose.
- **5.16.7.6** *Password managers.* When appropriate with respect to system’s operations, the system should support the use of password managers for users to input their credentials.
- **5.19.1.9.2** *Cybersecurity.* During use, PEDs should have security measures in place to avoid tampering and adhere to the appropriate DoD cybersecurity requirements and 5.16.


## 4. Controls, pointing and touch input

Requirements for on-screen controls and for touchscreen use (many web HMIs are operated on touch-enabled devices). Mechanical, keyboard-hardware, mouse-hardware, trackball, joystick and light-pen requirements are not applicable to a web HMI and are excluded.

- **5.1.1.3.1** *Grouping.* Controls that are operated in a task-driven sequence or that are operated together shall be grouped together along with their associated displays.
- **5.1.1.3.1.1** *Multiple steps selected by a single control.* When several steps of a sequence are selected by one control, the steps shall be arranged by order of occurrence to minimize control movements and prevent cycling through unnecessary steps.
- **5.1.1.3.1.2** *Cycling through ON/OFF position.* Unnecessary cycling through the control’s ON/OFF position shall be avoided.
- **5.1.1.3.2** *Sequential operation.* Where sequential operations follow a fixed pattern, controls shall be arranged to facilitate operation (e.g., a left-to-right/top-to-bottom pattern, as on a printed page).
- **5.1.1.3.4** *Consistency.* The arrangement of functionally similar or identical primary controls shall be consistent from panel to panel throughout the system, equipment, vehicle, and other systems expected to be operated by the user (e.g., a movement of a control to the right or left shall result in a corresponding movement of a displayed element to the right or left).
- **5.1.1.4.1** *Consistent application.* Where coding is used to differentiate among controls, application of the code shall be uniform throughout the system and other systems expected to be operated by the user.
- **5.1.1.7.1** *Location and design.* Controls shall be designed and located so that they are not susceptible to being moved accidentally or inadvertently, particularly critical controls where such operation might cause equipment damage, personnel injury, system performance degradation, or system shutdown of mission-critical equipment where a reboot period is necessary to restart the equipment.
- **5.1.1.7.3** *Rapid operation.* Any method of protecting a control from inadvertent operation shall not preclude operation within the time required.
- **5.1.1.9.2** *Consistency of use.* A control used for a critical or emergency use function shall be dedicated to that function only.
- **5.1.2.2.1** *Functional grouping.* Functionally related controls and displays shall be located close to each other and arranged in functional groups (e.g., power, status, and test).
- **5.1.2.2.1.1** *Sequence.* Functional groups of controls and displays shall be located to provide for left-to-right (preferred) or top-to-bottom order of use, or both.
- **5.1.2.2.1.3** *Access.* Provided that the integrity of grouping by function and sequence is not compromised, the more frequently used groups and the most important groups shall be located in areas of easiest access.
- **5.1.2.2.1.5** *Consistency.* The location of recurring functional groups and individual items shall be consistent from panel to panel.
- **5.1.2.2.1.5.2** *Display commonality.* When multiple displays and multiple display formats are used, nomenclature and symbology shall be common on all displays, as appropriate.
- **5.1.2.2.1.5.3** *Text or readout fields.* Text or readout fields common to all displays (e.g., system advisories) shall be in a standard location on all display panels and formats.
- **5.1.2.2.1.5.4** *Commonality and consistency across systems.* Where possible, criteria for consistency and display commonality shall be applied across other systems expected to be operated by the user.
- **5.1.2.2.3** *Arrangement within groups.* Controls and displays within functional groups shall be located according to operational sequence or function, or both.
- **5.1.2.2.3.1** *No specific sequence.* If the controls and displays within a functional group are not used in any specific operational sequence, they shall be arranged either in accordance with their importance or their frequency of use, with the most important or frequently used controls in the most accessible locations.
- **5.1.2.2.3.11** *Emergency use.* Emergency displays and controls shall be located where they can be seen and reached without delay. For example, warning lights should be within a 30-degree cone about the user’s normal line of sight (the median direction of gaze when viewing a display surface) (see figure 2).
- **5.1.3.1.1.1** *High frequency use.* A touchscreen should not be the sole input means if the interface will be used to enter large amounts of data frequently.
- **5.1.3.1.1.2** *Moving or vibration environments.* A touchscreen should not be the sole input means if system movement or vibration might degrade user performance below the level required for mission accomplishment.
- **5.1.3.1.1.3** *Mission-critical and safety-critical applications.* A touchscreen shall not be the sole input device for mission critical or safety critical interfaces (such as flight control information).
- **5.1.3.1.3** *Positive indication.* A positive indication of touchscreen actuation shall be provided to acknowledge the system response to the control action.
- **5.1.3.1.4** *Display response time (latency).* Display response time should not be more than 100 milliseconds (ms). A shorter display response time may be dictated by operational considerations. Any latency should be evaluated in a representative environment to ensure that it meets human performance requirements.
- **5.1.3.1.5** *Impact on visual display.* Characteristics of touch-interactive devices shall not degrade visual display quality in a manner that impairs user performance.
- **5.1.3.1.6** *Spatial resolution.* Touch-interactive devices shall provide sufficient spatial resolution for anticipated task performance.
- **5.1.3.1.7** *Critical tasks.* Where a touchscreen control is used for a critical task, system response shall require an additional confirmatory action to ensure that the control actuation is intended.
- **5.1.3.1.7.1** *Confirmatory action impractical.* Where confirmatory action is impractical for critical tasks, multiple touch actuations shall be incorporated to minimize the possibility of accidental actuation.
- **5.1.3.1.8** *Repeat function delay.* An initial delay of 500 to 750 ms shall be provided when a repeat delay is provided.
- **5.1.3.1.9** *Sensitivity.* Fingertip contact with the touchscreen shall be able to actuate the control or input function.
- **5.1.3.1.14** *Sustained interaction.* Touchscreens shall not be used for frequent actions over an extended duration of time (e.g., typing on a virtual keyboard or continuous target selection). This requirement does not apply to small hand-held devices.
- **5.1.3.1.15** *Intermittent interaction.* Touchscreens may be used to complete intermittent actions such as gross cursor navigation and communication panel selection.
- **5.1.3.1.16** *Target shape and color.* If color coding is used, it shall be redundant to another form of coding.
- **5.19.3.3** *Data input.* PED data input should be via virtual or physical keyboard or keypad. Unless specifically required for the intended purpose, handwriting recognition as data input should be avoided.
- **5.19.3.4** *Direct input pointers.* Direct input pointers (e.g., touch-screen, light pens) shall be used in preference to indirect input pointers (e.g., trackball, mouse).

- **5.1.3.1.17** *Dimensions, resistance, and separation.* The dimensions, resistance, and separation of responsive areas of touch screens shall conform to figure 3 (reproduced below). *(Dimensions are physical sizes on screen, to be translated to CSS pixels using the actual device pixel density; the force/resistance column of figure 3 is a hardware characteristic and is omitted.)*

  | | A (actuation area)¹ | S (separation)² |
  |---|---|---|
  | Alphanumeric/numeric keyboards – minimum | – | 0 |
  | Alphanumeric/numeric keyboards – preferred | 16 × 16 mm (0.6 × 0.6 in) | 2 mm (0.08 in) |
  | Alphanumeric/numeric keyboards – maximum | – | 6 mm (0.25 in) |
  | Other applications – minimum | 15 × 15 mm (0.6 × 0.6 in) | 3 mm (0.12 in) |
  | Other applications – maximum | 38 × 38 mm (1.5 × 1.5 in) | 6 mm (0.25 in) |

  1. Unless otherwise noted, the dimensions apply to ungloved finger touch; for standard flame-resistant anti-flash gloves add 5 mm (0.2 in) to each dimension of the actuation area (A).
  2. For a "first contact" actuation strategy, separation between targets shall be not less than 5 mm (0.2 in). For a "last contact" strategy, separation may be less than 5 mm, but not less than 3 mm (0.12 in) for applications other than alphanumeric/numeric keyboards.


## 5. Interactive control and dialog design (general)

General criteria for interactive dialogs. Criteria that duplicate section 5.17 (menus, forms, command language, question-and-answer, query) are given once, in sections 6 and 7.

- **5.1.3.12.1.1** *Control actions.* Control actions shall be minimized, consistent, make minimal memory demands of the user, and be sufficiently flexible to adapt to different user needs.
- **5.1.3.12.1.2** *Dialog types.* Dialog types shall be compatible with anticipated task requirements and user skills.
- **5.1.3.12.1.3** *Response time compatibility with user.* System response times shall be consistent with operational requirements and shall accommodate dialog type and requirements for user training.
- **5.1.3.12.1.3.1** *Compatibility with system response times.* Required user response times shall be compatible with required system response time.
- **5.1.3.12.1.3.2** *Consistent with user tasking.* Required user response times shall be within the limits imposed by total user tasking expected in the operational environment (see 5.1.2.1.6.4).
- **5.1.3.12.1.3.3** *Response time-induced input device lockout.* If computer processing time requires delay of concurrent user inputs and no keyboard buffer is available, input device lockout shall occur until the computer can accept the next transaction.
- **5.1.3.12.1.3.4** *Lockout alert.* An alert shall be displayed to indicate to the user that lockout has occurred.
- **5.1.3.12.1.3.5** *Input device restoration.* When the computer is ready to continue following a response time induced input device lockout, a signal to indicate such shall be presented (e.g., cursor changes back to normal shape).
- **5.1.3.12.1.3.6** *Interrupt to end input device lockout.* When input device lockout has occurred, the user shall be provided with a capability to abort a transaction that has resulted in an extended lockout. Any such capability shall act like an UNDO command that stops ongoing processing and does not RESET the computer, thereby losing prior processing.
- **5.1.3.12.1.4** *Simplicity.* Control and display relationships shall be straightforward and explicit.
- **5.1.3.12.1.5** *Control actions.* Control actions shall be simple and direct, whereas potentially destructive control actions shall require extended user attention such that they are not easily acted on (e.g., “Are you sure?” queries).
- **5.1.3.12.1.6** *Accidental actuation.* Provision shall be made to prevent accidental actuation of potentially destructive control actions, such as accidental erasure or memory dump.
- **5.1.3.12.1.7** *Compatibility with user skill.* Controls shall be compatible with the lowest anticipated user skill levels. Experienced users shall have options that shortcut intervening steps necessary for inexperienced users.
- **5.1.3.12.1.8** *Availability of information.* Information necessary to select or enter a specific control action shall be available to the user when selection of that control action is appropriate.
- **5.1.3.12.1.9** *Concurrent display.* Concurrent displays shall meet the criteria in 5.1.3.12.1.9.1 through 5.1.3.12.1.9.3.
- **5.1.3.12.1.9.1** *Control actions.* Control actions to be selected from a discrete set of alternatives shall have those alternatives displayed prior to the time of selection.
- **5.1.3.12.1.9.2** *Current value.* The current value of any parameter or variable with which the user is interacting shall be displayed.
- **5.1.3.12.1.9.3** *User control inputs.* User control inputs shall result in a positive feedback response displayed to indicate performance of requested actions.
- **5.1.3.12.1.10** *Hierarchical process.* The number of hierarchical levels used to control a process or sequence shall be minimized. No more than four hierarchical levels should be used.
- **5.1.3.12.1.10.1** *Display and input formats.* Display and input formats should be similar within hierarchical levels.
- **5.1.3.12.1.10.2** *Current position.* The system shall always indicate the current positions within the sequence.
- **5.1.3.12.1.11** *User memorization.* The system shall not require the user to learn mnemonics, codes, special or long sequences, or special instructions.
- **5.1.3.12.1.12** *Dialog type.* The choice of dialog type (e.g., form filling, menus, or command language) for interactive control shall be compatible with user characteristics and task requirements.
- **5.1.3.12.1.13** *Number system.* Number systems shall meet the criteria in 5.1.3.12.1.13.1 through 5.1.3.12.1.13.2.
- **5.1.3.12.1.13.1** *Decimal system.* When numeric data are displayed or required for control input, such data shall be in the decimal, rather than binary, octal, hexadecimal, or other number system unless it is operationally imperative to use an alternate number system.
- **5.1.3.12.1.13.2** *Alternate number system.* When numeric data is not displayed in the decimal format due to operational requirements, the alternate number system used shall be clear to the user.
- **5.1.3.12.1.14** *Data manipulation.* The system shall enable the user to manipulate data without specialized knowledge of the internal storage and retrieval mechanisms of the system.
- **5.1.3.12.1.15** *Computer processing constraints.* The sequence of transaction selection shall be dictated by user choices and not by internal computer processing constraints.
- **5.1.3.12.1.16** *Feedback for correct input.* Control feedback responses to correct user input shall consist of changes in state or value of those display elements that are being controlled and shall be presented in an expected and logically natural form.
- **5.1.3.12.1.19** *Control input data display.* The presence and location of control input data entered by the user shall be clearly and appropriately indicated. Data displayed shall not mislead the user with regard to nomenclature, units of measure, sequence of task steps, or time phasing.
- **5.1.3.12.2.2.3** *Menu selection by pointing.* If menu selection is accomplished by pointing, dual actions shall be provided. The first action shall designate the selected option. The selection for processing shall be followed by a separate action to enter the selection for processing.
- **5.1.3.12.2.2.5** *Stack menu selections.* Users shall be provided the capability to stack menu selections (i.e., to make several menu selections without having each menu displayed).
- **5.1.3.12.2.9** *Direction function call.* If several levels of hierarchical menus are provided, a direct function call capability shall be provided such that the experienced user does not have to step through multiple menu levels.
- **5.1.3.12.2.14** *Return to next higher level.* When using hierarchical menus, the user shall be able to return to the next higher level by using single key action until the initial, top-level menu or display is reached. No more than three levels should be used in a hierarchical menu structure.
- **5.1.3.12.2.15** *Return to top level.* A function shall be provided to directly recall the initial, top-level menu or display without stepping through the menu or display hierarchy.
- **5.1.3.12.3.8.2** *Automatic movement.* The cursor shall automatically move to the next field when the end of the field is reached.
- **5.1.3.12.3.14** *User input delayed.* If user input will be delayed, the user shall have the ability to designate the field to indicate that the missing item is delayed, not overlooked.
- **5.1.3.12.5.5** *Abbreviations.* The user shall be permitted to enter the full command name or an abbreviation for any command of more than five characters.
- **5.1.3.12.6.3** *Multiple questions.* Users shall not be required to answer several questions at once.
- **5.1.3.12.8** *Graphic interaction.* Graphic interaction design shall meet the criteria in 5.1.3.12.8.1 through 5.1.3.12.8.3.
- **5.1.3.12.8.1** *Use.* Graphic interaction as a dialog may be considered for use by casual users to provide graphic aids as a supplement to other types of interactive control.
- **5.1.3.12.8.2** *Iconic menus.* When system users have different linguistic backgrounds, graphic menus that display icons to represent the control options may be used. Where the system is intended for use by foreign military personnel, icon design shall be consistent with applicable cultural and ethnic variables to ensure comprehension and to avoid potential offense.
- **5.1.3.12.8.3** *Supplementary textual labels.* Where icons are used to represent control actions in menus, textual labels shall be displayed, or made available for display, with each icon to help ensure that its intended meaning will be understood.


## 6. Dialog types: command language, question-and-answer, query, menus, forms

Command dialogs, visually displayed menus, form-filling dialogs, data entry and cursor/pointer behavior.

- **5.1.3.11.1.6** *Entry of data sets.* Where repetitive entry of data sets is required, data validation for each set shall be completed before another transaction can begin (see 5.1.2.1.7.13).
- **5.1.3.11.1.8** *Abbreviations, acronyms, mnemonics, and codes.* When abbreviations, acronyms, mnemonics, or codes are used to shorten data entry, they shall be distinctive and have an intuitive relationship or association to normal language or specific job-related terminology.
- **5.1.3.11.1.8.1** *Length.* An abbreviation shall be no longer than is necessary to ensure a clear and unambiguous meaning.
- **5.1.3.11.1.8.2** *Reference.* An easily accessible reference of abbreviation meanings shall be made available to the operator.
- **5.1.3.11.1.8.3** *Consistency.* Abbreviations, acronyms, mnemonics, and codes shall be consistent within and between systems and applications that are anticipated to be used by the same user population.
- **5.1.3.11.1.11** *Change of data.* Where a user requests a change (or deletion) of a data item that is not currently being displayed, the option of displaying the old value before confirming the change shall be presented.
- **5.1.3.11.1.12** *Single method of data entry.* Data entry methods and data displays shall not require the user to shift between entry methods.
- **5.1.3.11.1.13** *Data entry display.* Where data entry on an electronic display is permitted only in prescribed areas, a clear visual definition of the entry fields shall be provided.
- **5.1.3.11.3.3** *Length.* The length of individual data items shall be no more than necessary to accomplish the task.
- **5.1.3.11.3.4** *Justification.* When entering tabular data, the user shall not be required to right- or left-justify tabular data entries. The system shall automatically justify columnar data with respect to decimal point, left margin, or right margin, depending on the type of data.
- **5.1.3.11.3.6** *Minimization of keying.* The amount of keying required shall be no more than necessary to accomplish the task.
- **5.1.3.11.3.7** *Minimization of shift keying.* The use of key shifting functions shall be minimized during data entry transactions.
- **5.1.3.11.3.8** *Data change.* In keyed data entry, means shall be provided to allow users to change previous entries by either overtyping the existing text or inserting new text at any position within the data field.
- **5.1.3.11.4.2** *Standardization.* Fixed function keys shall be common throughout the system.
- **5.1.3.11.4.3** *Functional consistency.* Once a key has been assigned a given function, it shall not be reassigned to a different function for a given user.
- **5.1.3.11.4.4** *Availability.* Fixed function keys shall be selected to control functions that are continuously available (i.e., lockout of fixed function keys shall be minimized).
- **5.1.3.11.4.4.1** *Function keys not currently used.* At any step in a transaction sequence, function keys not used for current inputs shall be temporarily disabled under computer control.
- **5.1.3.11.4.5** *Non-active keys.* Non-active fixed function keys shall be replaced by a blank key or grayed-out.
- **5.1.3.11.4.6** *Grouping.* Fixed function keys shall be logically grouped and placed in distinctive locations on the keyboard.
- **5.1.3.11.4.7** *Actuation.* Except when used to toggle between two opposing states, a fixed function key shall require only a single actuation to accomplish its function.
- **5.1.3.11.4.8** *Feedback.* When fixed function key activation does not result in an immediately observable natural response, the user shall be given an indication of system acknowledgment.
- **5.1.3.11.4.9** *Fixed function key labels.* Fixed function key assignments shall always be displayed.
- **5.1.3.11.4.9.1** *Direct marking.* Fixed function key assignments shall be displayed by direct marking.
- **5.1.3.11.4.9.2** *Abbreviations.* Where abbreviations are necessary for fixed function keys, standard abbreviations shall be used.
- **5.1.3.11.4.10** *Prolonged function key pressing.* Except for the DELETE key, prolonged pressing of function keys shall not result in a repeat of the function.
- **5.1.3.11.5.2** *Critical action.* A single entry on a variable function key shall not produce a critical action.
- **5.1.3.11.5.3** *Critical action confirmation.* Critical actions shall require confirmation when initiated with a variable action key.
- **5.1.3.11.5.4** *Status display.* When the effect of a function key varies, the status of the key shall be displayed.
- **5.1.3.11.5.5** *Reprogrammable or inactive default functions.* When keys with labeled default functions are reprogrammed or turned off, a visual warning shall alert the user that the standard function is not currently accessible via that key.
- **5.1.3.11.5.8** *Easy return to base-level functions.* Where the functions assigned to a set of function keys change as a result of user selection, the user shall be given an easy means to return to the initial, base-level functions.
- **5.17.2.1.1** *Use.* Command language may be used for tasks that involve a wide range of user inputs and where user familiarity with command language can be used to take advantage of the flexibility and speed of this control technique.
- **5.17.2.1.2** *User viewpoint.* Command language commands shall be logically related to the user’s expectation of what the commands will initiate.
- **5.17.2.1.3** *Naming distinctiveness.* Command names shall be distinct from one another.
- **5.17.2.1.4** *Punctuation and special characters.* The command language dialog shall minimize the use of punctuation and special characters.
- **5.17.2.1.5** *Standardization.* All commands and corresponding abbreviations shall be identical across systems and platforms.
- **5.17.2.1.6** *Standard location.* Commands shall be entered and displayed in a standard location on the software user interface.
- **5.17.2.1.7** *Command prompts.* The user shall be provided a means, such as prompts or tooltips, to determine required parameters or available options for command entry.
- **5.17.2.1.8** *Complexity.* The command language shall accommodate the skill levels of users ranging from novice to experienced.
- **5.17.2.1.9** *User definition of macro commands.* The command dialog shall not allow a user to name macros with existing command names (“reserved keywords”).
- **5.17.2.1.10** *Standard techniques for command editing.* Command and data entries shall be edited using the same techniques.
- **5.17.2.1.11** *Destructive commands.* If a command entry has destructive consequences, the system shall display the consequences of the command so that the user may review and cancel or confirm before it is executed.
- **5.17.2.2.1** *Use.* Question-and-answer dialogs may be used for routine data entry tasks where data items are known, their ordering can be constrained, and where users will have little or no training.
- **5.17.2.2.2** *Questions displayed separately.* For multiple questions in a dialog box, each question shall be listed separately.
- **5.17.2.2.3** *Distinct response area.* When responses are required for a question, each question shall have a separate and distinct response area.
- **5.17.2.2.4** *Recapitulating prior answers.* When a series of system-posed questions are interrelated, answers to previous questions shall be displayed if they provide context to help a user answer the current question.
- **5.17.2.2.5** *Source document capability.* When questions prompt entry of data from a source document, the question sequence shall match the data sequence in the source document.
- **5.17.2.3.1** *Natural organization of data.* Query languages should reflect a data structure or organization perceived by users to be natural. For example, if a user would have reason to suppose that all data about a particular topic would be stored in one place, then the query language should permit such data to be retrieved by a single query, even though various pieces of data may be stored in different computer files.
- **5.17.2.3.2** *Coherent representation of data organization.* A single representation of the data organization for use in query formulation shall be established (i.e., the user does not need to know if different queries will access different databases via different routes).
- **5.17.2.3.3** *Task-oriented wording.* Users shall be able to specify the data being requested in a query without being required to specify where the data are stored.
- **5.17.2.3.4** *Logic to link queries.* The query language should be designed to include logic elements that permit users to link (e.g., “and”, “or”) sequential queries as a single entry.
- **5.17.2.3.5** *Confirming large-scale data retrieval.* If a user’s query request will retrieve a large amount of data, the user shall be provided the options to continue, cancel, or refine the query during processing.
- **5.17.2.3.6** *Estimate of remaining time.* When processing takes a long time, the user shall be provided with an indication of the status that includes an estimate of the proportion remaining in terms of either time or amount.
- **5.17.3.1.1** *Drop-down menu bars.* Drop-down menus contain a list of contextual commands that are presented in a menu bar along the top edge of a window. The menu bar shall contain a set of menu category labels (e.g., Edit) from which drop-down menus are accessed.
- **5.17.3.1.1.1** *Use.* Drop-down menus and the associated menu bars should be used when there are more than three commands and categories.
- **5.17.3.1.1.2** *Primary windows.* Drop-down menus and menu bars should be used when commands appear in primary windows (i.e., popup dialogs).
- **5.17.3.1.2** *Submenus.* Submenus shall consist of a group of options related to a higher-level toolbar menu item.
- **5.17.3.1.2.1** *Additional menu options.* Submenus should allow additional menu options to be viewed without increasing the length of the overall menu.
- **5.17.3.1.2.2** *Shorten length.* Submenus may be used to shorten the length of a long menu when a higher-level menu item may be used to describe several related functionalities.
- **5.17.3.1.2.3** *Frequently needed functions.* Submenus should not be used for functions that are frequently needed and accessed.
- **5.17.3.1.2.4** *Indication.* An indicator (e.g., a right-pointing triangle) should be used to display the presence of a submenu on a higher-level menu.
- **5.17.3.1.2.5** *Presentation of options.* When the higher level option is selected, the options shall be presented without further input from the user.
- **5.17.3.1.2.6** *Number of levels.* Submenus should be limited to three levels (i.e., Main>Sub1>Sub2) to ensure discoverability of nested items.
- **5.17.3.1.3** *Menu bar.* Users should interact with menu bars by left-clicking the menu category within the menu bar to open the drop-down menu command list.
- **5.17.3.1.3.1** *Toolbar menu.* Toolbars may be used to graphically present command actions for easy and efficient access.
- **5.17.3.1.3.2** *Use in primary windows.* Toolbars should be used in primary windows to provide a small number of icons as immediate commands.
- **5.17.3.1.3.3** *Use in secondary windows.* Toolbars should not be used in secondary windows, with text-only commands, with crowded icons, and with non-immediate commands (e.g., configuring a filter).
- **5.17.3.1.3.4** *Toolbar options.* Toolbar options that are fixed should be limited to those commands used most frequently by a large percentage of users. Alternatively, a means may be provided for users who wish to configure them to have access to frequently accessed options.
- **5.17.3.1.3.5** *Toolbar labels.* Toolbars with icons shall provide labels via a tooltip (hover over to show label).
- **5.17.3.1.3.6** *Non-standard icons.* Non-standard icons should be avoided but if present, shall provide labels via a tooltip (hover over to show label) and should include fixed-text labels.
- **5.17.3.1.3.7** *Selection.* Toolbar menus should be left-clicked for devices like mice and trackballs to make a selection.
- **5.17.3.1.4** *Ribbon menus.* Ribbon menus separate dedicated task commands within a window. In these menus, a list of task-related commands appears under an active tab. Ribbon menus may be used when there are multiple menu categories with items under each category.
- **5.17.3.1.5** *Right-click and context menus.* Right-click menus may be used to provide contextually relevant options and actions pertaining only to the selected item or pane.
- **5.17.3.1.5.1** *Method of accomplishment.* Right-click menus shall not provide the only method of accomplishing any command.
- **5.17.3.1.5.2** *Accessibility.* Right-click and context menus shall not include options that cannot be accessed elsewhere by a means that is apparent.
- **5.17.3.1.5.3** *Availability.* Right-click menus should be available to initiate basic and frequently used commands.
- **5.17.3.1.5.4** *Not substitute.* Right-click menus shall not be used as a substitute for primary menus (e.g., drop-down menus).
- **5.17.3.2.1** *Interactive menu use.* Interactive menu selection shall be used for tasks that involve little or no entry of arbitrary data, users that may have relatively little training, or a command set that is too large to commit to memory.
- **5.17.3.2.2** *Selection method.* Selection of devices shall be in accordance with the criteria in 5.17.3.2.2.1 through 5.17.3.2.2.4.
- **5.17.3.2.2.1** *Mouse.* A mouse or other pointing input device (including touch technology) shall be an available method for menu selection (see 5.1.3.3).
- **5.17.3.2.2.2** *Pointing.* If menu selection is accomplished by pointing, a selection action shall be made to initiate the command (i.e., left click of mouse or tap for touch input).
- **5.17.3.2.2.3** *Submenus.* Each submenu shall have a title that clearly identifies the purpose of that menu.
- **5.17.3.2.2.4** *Multiple pages.* Menus shall not consist of multiple pages of options.
- **5.17.3.2.3** *Active option presentation.* Menu options that are not available shall either not be presented or be greyed out to make it clear that they are not available.
- **5.17.3.2.4** *Format consistency.* Menus shall be presented in a consistent format throughout the system and across other related systems the user is expected to operate.
- **5.17.3.2.5** *Accessible.* Menus shall be readily accessible at all times.
- **5.17.3.2.6** *Option sequence.* Menu selections shall be listed in a logical order, such as alphabetically or by frequency of use.
- **5.17.3.2.7** *Simple menus.* If the number of selections can fit on one page in no more than two columns, a single menu may be used. If the selection options exceed two columns, a hierarchical menu structure should be employed.
- **5.17.3.2.8** *Option presentation.* Textual menu options should be presented on separate lines to ensure that the items are discriminable and selectable.
- **5.17.3.2.9** *Direct function call.* A direct function call capability shall be provided for frequently used options.
- **5.17.3.2.10** *Shortcut commands.* Shortcut commands (e.g., “control C” for copy) should be provided for frequently used actions.
- **5.17.3.2.11** *Option coding.* When selections may be made by keyboard shortcuts, the shortcuts associated with each alternative option shall be included next to the menu option.
- **5.17.3.2.12** *Keyboard shortcuts.* If menu selections are made by keyboard entry of alphanumeric codes, the options shall be coded by letters that are associated with the name of the option or standard shortcuts rather than by more arbitrary codes.
- **5.17.3.2.13** *Duplication.* Keyed codes shall not duplicate any other user function codes.
- **5.17.3.2.14** *Position in structure.* When menu selection traverses multiple levels, all of the levels shall remain visible until the selection is made.
- **5.17.3.2.15** *Menu hierarchy.* A menu tree showing the menu hierarchy shall be included in the user manual or online Help section.
- **5.17.3.2.16** *Tooltips.* A tooltip should be displayed over an item as the user hovers the pointer without clicking on the item.
- **5.17.3.2.17** *Icons.* Menus may use icons to represent the control options.
- **5.17.3.2.17.1** *Currently being used.* When possible, icons should be consistent with icons that are currently being used in other systems, familiar to the user, and used consistently within an application.
- **5.17.3.2.17.2** *Graphic menu use.* Graphic menus may use icons to represent control options and be consistent within an application.
- **5.17.4.1** *Data entry.* Data entry functions shall be designed to establish consistency of data entry transactions, minimize input actions, minimize memory load on the user, ensure compatibility of data entry with data display, and provide the user control over data entry.
- **5.17.4.2** *User pacing.* The pace of data entry shall be controlled by the user, rather than by the system.
- **5.17.4.3** *Positive feedback.* The system shall provide feedback to the user regarding the acceptance or rejection of an entered datum.
- **5.17.4.4** *Response time.* Feedback response times shall be in accordance with 5.17.9.5.
- **5.17.4.5** *Processing delay.* Where system overload or other system conditions results in a processing delay, the system shall acknowledge the data entry and provide an indication of the delay to the user.
- **5.17.4.6** *Completion time.* The system should provide some indication of the completion time or of the fraction of the process completed.
- **5.17.4.7** *Explicit action.* Each data entry shall require an explicit completion action, such as pressing an ENTER key or other key (e.g., TAB key).
- **5.17.4.8** *Validation.* Data entries shall be validated by the system for correct format, legal value, and range of values prior to processing.
- **5.17.4.9** *Software-available data.* The user shall not be required to enter data already available to the software.
- **5.17.4.10** *Input units.* Data shall be entered in units that are familiar to the user.
- **5.17.5.2** *Distinctive cursor.* The cursor shall have differing visual attributes that distinguish various usages, such as selecting an object or editing text.
- **5.17.5.3** *Distinctive cursor position.* The current cursor position on graphic or image processing application displays that require precise positioning shall be indicated by displaying a distinctive cursor symbol that indicates a point (e.g., a plus-sign or crosshairs whose intersection can mark a position with reasonable precision).
- **5.17.5.4** *Obscuration.* The cursor shall not obscure displayed entities.
- **5.17.5.6** *Cursor movement using arrow keys.* If the cursor is moved by pressing a key, releasing the key shall cause the cursor to stop moving.
- **5.17.5.7** *Precise positioning.* Where data entry requires exact placement of graphic elements, users shall be provided the capability for expansion of the critical display area (e.g., zooming and panning) to make the positioning task easier and more precise.
- **5.17.5.8** *Home position.* The home position for the cursor shall be consistent across similar types of displays.
- **5.17.5.9** *Explicit actuation.* A separate explicit action distinct from cursor position, (i.e., pressing a button or toggling) shall be required for a control selection.
- **5.17.5.10** *Incremental cursor positioning.* Where cursor positioning is incremented in discrete steps, the step size of cursor movement shall be consistent horizontally (in both right and left directions), and vertically (in both up and down directions).
- **5.17.5.11.1** *Position designation.* When position designation is required in a task emphasizing keyed data entry, cursor control shall be available by the keyboard, preferably using the arrow keys.
- **5.17.5.11.2** *Means of control.* If keys other than the arrow keys are used for movement (as with an abbreviated keyboard), the display should clearly indicate the means of control.
- **5.17.5.12.1** *User expectations.* The response of a cursor to user input shall be consistent and compatible with the user’s expectations.
- **5.17.5.12.2** *Move in corresponding direction.* For cursor control by key action, the cursor shall move in the corresponding direction of the arrow key that was pressed (i.e., left arrow to left, right arrow to right, up arrow up, down arrow down).
- **5.17.5.13** *Explicit delete action.* Data deletion or cancellation shall require an explicit action, such as pressing a DELETE key.
- **5.17.5.14** *Permanent deletion.* Permanent deletion (in the absence of an “undo” function) of more than one character shall not be allowed without an affirmative response to a validation query.
- **5.17.6.1** *Use.* Form filling interactive control may be used where some flexibility in data entry is needed.
- **5.17.6.2** *Grouping.* Displayed forms shall be organized to group related items together.
- **5.17.6.3** *Format and content consistency.* The format and content of displayed forms shall duplicate in every major parameter the (paper) form it is intended to represent.
- **5.17.6.4** *Advancing through form fields.* When using TAB or ENTER keys to advance through form fields, the system shall require a response for every data entry field; advancing through a field (i.e., leaving a blank) for which no entry is desired shall require an explicit action (TAB or ENTER keystrokes).
- **5.17.6.5** *Separation.* Fields or groups of fields shall be separated by spaces, lines, or other delineation cues.
- **5.17.6.6** *Distinctiveness of fields.* Required fields shall be distinguished from optional fields.
- **5.17.6.7** *Field labels.* Field labels shall be distinctively presented such that they can be distinguished from data entry space.
- **5.17.6.8** *Additional cueing.* Labels for data entry fields shall incorporate additional cueing when the data format could logically be made in more than one format (e.g., “DATE (MM/DD/YYYY): _ _ / _ _ / _ _ _ _”).
- **5.17.6.9** *Informative labels.* Descriptive wording shall be employed when labeling data fields.
- **5.17.6.10** *Arbitrary codes.* Use of arbitrary codes shall be avoided.
- **5.17.6.11** *Top-aligned labels.* Top-aligned labels shall be used when labels are long or the form requires horizontal space for grouping related input fields.
- **5.17.6.12** *Vertical space limited.* Top-aligned labels shall not be used if vertical space is limited.
- **5.17.6.13** *Right-aligned labels.* Right-aligned labels shall be used for forms that require users to quickly scan labels and may be used when vertical space is limited.
- **5.17.6.14** *Left-aligned labels.* Left-aligned labels shall be used when a form requires users to carefully consider labels.
- **5.17.6.15** *Limited vertical space.* Left-aligned labels shall not be used when labels are so long that they do not allow for data entry in the same vertical space.
- **5.17.6.16** *Cursor default position.* When a form is displayed, a displayed cursor shall be positioned at the first data entry field by default.
- **5.17.6.16.1** *Cursor advancement.* The cursor shall be advanced to the next data entry field by the TAB key or the ENTER key when the user has completed entry of the current field.
- **5.17.6.16.2** *Exceptions.* Exceptions to using the ENTER key for advancement occur in cases where some fields require or allow multiple lines to be input; in which case, only the TAB key shall be used to advance the cursor.
- **5.17.6.17** *Entry length indication.* The maximum acceptable length for variable length fields shall be displayed.
- **5.17.6.18** *Maximum characters inputted.* An indication shall be provided to the user if the maximum number of characters has been inputted.
- **5.17.6.19** *Overwriting.* Characters other than those indicating format that must be overwritten shall not be entered into unfilled data fields by default.
- **5.17.6.20** *Dimensional units.* When a consistent dimensional unit is intended as the unit of entry in a given field, the dimensional unit shall be provided by the computer to the right of the field.
- **5.17.6.21** *Variable dimensional units.* When the dimensional unit may vary for a given field, the user shall be informed and a separate field with options shall be provided to the right of the numeric field for the entry of units from a drop down list.
- **5.17.6.22** *User omissions.* The user shall be informed when required data entries have not been entered.
- **5.17.6.23** *Non-entry areas.* Non-entry (protected) areas of the display shall be displayed in a way that makes them distinguishable from areas where data may be entered.
- **5.17.6.24** *Inaccessible by cursor.* Non-entry areas shall be inaccessible via the cursor.
- **5.17.6.25** *Flexible data entry.* When multiple data items are entered, the user shall be allowed to re-enter, change, or cancel any item before submitting.
- **5.17.6.26** *Logical order.* Where no source document is involved, forms shall be designed so that data items are ordered in a logical sequence of input.
- **5.17.6.27** *Dialog boxes for control entry.* Dialog boxes may be used as an aid for composing complex control entries or for selecting options. For example, for a print request, a displayed form might help a user invoke the various format controls that are available.


## 7. Error management, user guidance, help and prompts

Error prevention, detection, messaging and recovery, plus help and prompting.

- **5.1.2.1.7.8** *Diagnostic information.* Error messages shall explicitly provide as much diagnostic information and remedial direction as can be inferred reliably from the error condition.
- **5.1.2.1.7.11.2** *Computer-corrected commands.* Computer-corrected commands, values, and spellings shall be displayed and highlighted for user confirmation.
- **5.17.10.1** *Error correction.* An easy means for correcting erroneous user entries shall be provided.
- **5.17.10.2** *Partial correction.* The system shall permit partial correction of entries.
- **5.17.10.3** *Early detection.* A capability to detect and correct errors after keying in but before entering into the system shall be provided.
- **5.17.10.4** *Logical data breaks.* Error checking shall occur at logical data entry breaks, such as at the end of data fields rather than characters to minimize disruption.
- **5.17.10.5** *Internal software checks.* Software shall provide checks of user entries for validity of item, sequence of entry, completeness of entry, and range of values.
- **5.17.10.6** *Critical entries.* The system shall require the user to acknowledge entries that have irreversible or potentially destructive consequences prior to their being implemented by the system.
- **5.17.10.7.1** *Describe error.* Error messages shall describe the error simply and provide diagnostic information in terms of the application rather than system function (e.g., “too many characters” vs. “stack overflow”).
- **5.17.10.7.2** *Instruct user.* Error messages shall instruct the user how to recover from the error or escape from the error situation.
- **5.17.10.7.3** *Constructive and neutral tone.* Error messages shall be constructive and neutral in tone, avoiding phrases that suggest a judgment of the user’s behavior.
- **5.17.10.7.4** *Consistent with user understanding.* The error message shall be written in a manner consistent with the user’s understanding and expected level of knowledge of the system.
- **5.17.10.7.5** *Correction entry and confirmation.* When the user enters correction of an error, such corrections shall be implemented by an explicit action by the user (e.g., actuation of an ENTER key).
- **5.17.10.7.6** *Error correction acknowledgement.* The system shall acknowledge all error corrections by the user either by accepting the corrected entry or by providing a separate error message for an erroneous entry.
- **5.17.10.7.7** *Errors in elements not normally visible.* References to errors in elements of the system not normally visible to users shall not be included unless the user has explicitly requested detailed diagnostic information for programmer-level troubleshooting.
- **5.17.10.8** *Error description.* If a simple error message is presented, users shall be provided access to a more detailed description of the error.
- **5.17.10.9** *Message timing.* Error messages shall be provided within 0.2 second of the time in which an error is detected.
- **5.17.10.10** *Error recovery and process change (multi-level “undo”).* The user shall be able to stop a control process at any point in a sequence and easily return to previous levels in multi-step processes in order to nullify an error or effect a desired change.
- **5.17.10.11** *Spelling errors.* Spelling and other common errors shall not produce valid system commands or initiate transactions.
- **5.17.10.12** *Recognize common misspellings.* The system shall recognize common misspellings of commands and inform users of unrecognized entries, provide a similar correct entry, and display the recommendation for revision and confirmation by the user.
- **5.17.10.13** *Errors in stacked commands.* To prompt for corrections of an error in stacked commands, the system shall display the stacked command sequence with the error(s) highlighted. Where possible, a procedure shall be provided to correct the error and salvage the stack.
- **5.17.10.13.1** *Error correction procedure.* When possible, a procedure shall be provided to correct the error and salvage the stack.
- **5.17.10.13.2** *Partial execution.* If the error was not detected until after some of the commands in the stack have executed, the system shall provide a message to inform the user which commands were successfully executed.
- **5.17.10.14** *Display of erroneous entries.* An error message shall be displayed continuously until either the error is corrected or the user dismisses the error message.
- **5.17.10.15** *Automatic error correction.* Automatic correction of user errors by the system should be provided if:
  - a. The error being corrected has a unique solution.
  - b. The correction of the error is apparent to the user.
  - c. The automatic correction functionality can be disabled upon user request.
- **5.17.10.16** *Location of error messages.* Error messages shall appear as near as possible to the user entry that caused the message but not obscure any control or display area the user requires to perform the task.
- **5.17.10.17** *Obscure information.* If an error message obscures task-relevant information, a means for the user to move the message shall be provided.
- **5.17.10.18** *Multiple error messages.* If a user repeats an entry error, the second error message shall be revised to include a noticeable change so that the user may be certain that the computer has processed the attempted correction.
- **5.17.12** *Help.* In addition to the requirements provided herein, detailed guidance and best practices may be found in ANSI\HFES 200.3.
- **5.17.12.1** *Access.* The system shall provide built-in guidance by allowing users to easily access built-in technical help.
- **5.17.12.2** *Explicit error management aids.* Help shall be available through explicit error management aids (labels, prompts, advisory messages) and implicit aids (cueing).
- **5.17.12.3** *Standard action to request help.* A simple standard action shall always be available for the user to access help.
- **5.17.12.4** *Built-in help.* At a minimum, built-in help shall include definitions of allowable options, system capabilities, procedures, and ranges of values.
- **5.17.12.5** *Context sensitive help.* Context-sensitive help shall be specific to the context from which it was accessed. After initial access, the system shall provide the user the ability to query the help documentation.
- **5.17.12.6** *Minimize cross-referencing.* Individual built-in help displays should contain enough information to respond to a user’s query even if this capability produces redundancy in the built-in help system as a whole.
- **5.17.12.7** *Definitions.* The help section should contain a list of abbreviations and acronyms that are used in the main application.
- **5.17.12.8** *Multilevel help.* If an initial Help display provides only summary information, more detailed explanations shall be available via user query or drill-down.
- **5.17.12.9** *Consistent help terminology.* Online documentation, offline documentation, and help instructions shall use identical terminology to describe or define identical concepts or processes.
- **5.17.12.10** *System-initiated help.* For systems expected to have novice or infrequent users, a system-initiated Help function may be used. When provided, such functionality shall have the following characteristics.
- **5.17.12.10.1** *Specific.* System-initiated help shall only provide help specific to the user’s current task or context.
- **5.17.12.10.2** *Non-intrusive.* System-initiated help shall not interfere with or obscure any control or display area the user requires to perform the current task.
- **5.17.12.10.3** *Easy to enable and disable.* A relatively novice user shall be able to disable and enable system-initiated help functionality with minimal effort.
- **5.17.13** *Prompts.* Prompts shall be in accordance with the criteria in 5.17.13.1 through 5.17.13.10.
- **5.17.13.1** *Use.* Prompts and help instructions shall be used to explain commands, error messages, system capabilities, display formats, procedures, and sequences as well as to provide data.
- **5.17.13.2** *Prompts for special modes.* When operating in special modes, the system shall display the mode designation and file(s) being processed.
- **5.17.13.3** *User confirmation.* Before processing any user requests that would result in extensive or final changes to existing data, the system shall require user confirmation.
- **5.17.13.4** *Confirm abort operations.* When data entries or changes will be nullified by an abort action, the system shall require user confirmation.
- **5.17.13.5** *Prompt for missing data.* When missing data are detected, the system shall prompt the user to supply the missing data.
- **5.17.13.6** *Standard display.* Prompting messages shall be displayed in a standardized area of the displays.
- **5.17.13.7** *Explicit prompts.* Prompts and help instructions for system-controlled dialog shall be explicit.
- **5.17.13.8** *Memorization.* Prompts shall be presented in such a way that the user is not required to memorize lengthy sequences or refer to secondary written procedural references in order to respond.
- **5.17.13.9** *Prompt clarity.* Prompts shall be clear and understandable.
- **5.17.13.10** *Coding schemes.* Prompts shall not require reference to coding schemes or conventions that may be unfamiliar to occasional users.


## 8. Notifications, messaging, alerts and audio

Notification, chat/messaging, alerting and warning criteria, including audio cues and speech where the web HMI uses them. Warning-label, placard and physical-hazard marking criteria are excluded except where they translate directly to on-screen warning messages.

- **5.3.1.3** *Signal meaning.* Each audio signal shall have only one meaning. The ear acts as an effective detector of periodic signals in noise. Even when it is considerably weaker than the background noise, if the signal is a sinusoid (pure tone) or a combination of sinusoids (complex tone), the ear can detect it. The ear also efficiently detects periodic modulation in the very low frequency range and responds to variations in intensity or frequency.
- **5.3.1.8** *Manual overrides.* Non-critical audio signals shall be capable of being turned off at the discretion of the user.
- **5.3.1.9** *Visual indication.* Where the user has the capability to turn off non-critical audio signals, a visual indication that the signal has been turned off shall be provided to the user.
- **5.3.2.1.5** *Persistence.* Audio warning signals shall persist until restoration of normal conditions or warning is acknowledged.
- **5.3.2.2.1** *Distinct from warning signals.* Caution signals shall be readily distinguishable from warning signals.
- **5.3.2.5** *Prioritization.* When there is a possibility of simultaneous presentation of automatically initiated messages, a message priority system shall be established such that the most critical message overrides for initial presentation any messages of lower priority.
- **5.3.2.6** *Top-priority messages.* Following initial presentation of the top-priority message, other messages shall be presented in priority order, except that no caution messages shall be presented until all warning messages are terminated.
- **5.3.2.7** *Relation to visual displays.* When used in conjunction with visual displays, audio warning devices shall be supplementary or supportive and used to alert and direct user attention to the appropriate visual display.
- **5.3.3.2.1** *Manually initiated signals.* Manually initiated signals shall be manually terminated.
- **5.3.5.10.1** *Manual shutoff.* When a manual shutoff is used, a visual indication that the warning has been turned off shall be provided.
- **5.3.5.10.4** *Redundant visual warning.* All nonverbal aural annunciations shall be accompanied by a visual annunciation that defines the condition. In a cockpit, this may be an illuminated display. In the case of a warning horn on a backing vehicle, the vehicle’s backward motion provides redundancy.
- **5.3.5.11** *Volume control.* Control movements shall be restricted to prevent reducing the volume to an inaudible level, or increasing it to an unacceptably high level. Volume control shall be in accordance with the criteria in 5.3.5.11.1 through 5.3.5.11.5.
- **5.3.5.11.1** *Automatic or manual.* The volume (loudness) of an audio warning signal shall be designed to be controlled by the user, the sensing mechanism, or both depending on the operational situation and personnel safety factors.
- **5.3.9.1** *Supportive function.* Audio signals used in conjunction with visual displays shall be supplementary to the visual signals.
- **5.3.9.2** *Alert and direct user’s attention.* Audio signals shall be used to alert and direct the user’s attention to the appropriate visual display.
- **5.3.9.3** *Signal characteristics.* Signals may be one-time or intermittent.
- **5.3.9.4** *Intermittent signals.* Intermittent signals shall be automatically terminated when no longer applicable or by user control.
- **5.3.9.5** *Alarm settings.* When alarm signals are established on the basis of user-defined logic, users shall be permitted to obtain status information concerning current alarm settings in terms of dimensions (variables) covered and values (categories) established as critical.
- **5.3.9.6** *Alarm status information.* Alarm status information shall be provided in monitoring situations where responsibility may be shifted from one user to another, as in changes of shift.
- **5.3.10.5** *Message priority control.* Where simultaneous messages could occur, they shall be prioritized so that the initial presentation of the most critical message receives transmission priority and overrides lower priority messages.
- **5.3.10.7** *Message cancel capability.* A manual cancellation capability shall be provided for all speech displays after the initial presentation.
- **5.3.10.8** *Say again capability.* Message repetition shall be provided on user’s command.
- **5.3.13.3** *Adapt to the user and environment.* Speech recognition systems shall adapt to variability in acoustic environments, speaker styles, and user errors.
- **5.3.13.4** *Minimal training.* Speech recognition systems shall require minimal training.
- **5.3.13.5** *Feedback.* Speech recognition systems shall provide feedback to the user so the user knows the system has understood them.
- **5.3.13.6** *Input vocabulary.* Input vocabulary shall be minimized consistent with system needs and selected to provide phonetically distinct elements to eliminate misinterpretation.
- **5.3.13.7** *Interword delays.* Speech recognition systems shall not require interword delays or exaggeration in speech.
- **5.3.13.8** *Prompting.* Voice prompting from the computer shall be provided where there is an advantage to freeing the user from reading a display.
- **5.3.13.9** *Lack of user response.* Lack of user response to the prompt shall result in a repetition of the prompt.
- **5.3.13.10** *Correction capability.* A capability shall be provided to reject unintended and involuntary sounds such as sneezes, coughs, throat clearing, or non-command words.
- **5.3.14.1** *Speech recognition devices.* Speech recognition devices shall not be used as the sole control device.
- **5.3.14.2** *Alternative control device.* An alternative control device shall be provided in case of speech recognition device degradation or failure.
- **5.7.1.2** *Effective alerting system.* An effective alerting system should satisfy the following criteria:
  - a. Not give audible and visible caution and warning alerts when all systems are operating normally.
  - b. Reduce information assimilation and memory demands.
  - c. Minimize the time required for personnel to detect and assess failure conditions and to initiate corrective actions.
  - d. Provide visual alerts that can be detected and understood easily under all environmental conditions in which the system is expected to operate.
  - e. Be standardized to maximize recognizability.
  - f. Provide for alerting system growth capability in a form that does not necessitate additional system components.
- **5.7.1.7** *Signal precedence.* Each of the following signals shall take precedence over those below them.
- **5.7.1.7.1** *Warning (alarm).* A signal that alerts the user to a dangerous condition requiring immediate action.
- **5.7.1.7.2** *Caution (alert).* A signal that alerts the user to an impending dangerous condition requiring attention, but not necessarily immediate action.
- **5.7.1.7.3** *Advisory.* A signal to indicate safe or normal configuration, condition of performance, operation of essential equipment, or to attract attention and impart information for routine action purposes including a signal to indicate a change in system status that, while important, does not require immediate action on the part of the user.
- **5.7.2.1.2** *Warning content.* Warning labels and placards shall explicitly describe the hazard, how to avoid the hazard, and the consequences of not avoiding the hazard.
- **5.7.2.2** *Readability.* Messages on warning labels and placards shall be based on the literacy and reading skills of the target population.
- **5.7.2.2.1** *Target population.* Messages shall be written so that the target population can read, understand, and comply with the message.
- **5.7.2.2.2** *Sentence style.* Sentences shall be short, direct, and in active voice to increase readability.
- **5.7.2.2.3** *Multi-lingual or non-reading populations.* For multi-lingual or non-reading populations, consider commonly understood icons. These should be supplemented by written labeling in languages relevant to the user population in order to augment or clarify the meaning of icons.
- **5.7.2.5** *Signal word.* Warning placards or labels shall display the appropriate signal word (DANGER, WARNING, CAUTION, or NOTICE) formatted in accordance with ANSI Z535.2 or ANSI Z535.4, as applicable.
- **5.7.2.5.1** *DANGER.* DANGER shall be used when failure to avoid the hazard will almost certainly result in serious injury or death. DANGER is reserved for the most serious hazards only.
- **5.7.2.5.2** *WARNING.* WARNING shall be used when failure to avoid the hazard may result in serious injury or death.
- **5.7.2.5.3** *CAUTION.* CAUTION shall be used when failure to avoid the hazard may result in minor or moderate injury.
- **5.7.2.5.4** *NOTICE.* NOTICE should be used when failure to obey precautions may result in system damage or to indicate important information not directly associated with a hazard or hazardous situation.
- **5.7.3.1** *General.* Visual displays shall be used to provide the user with a clear indication of warning for off-normal equipment or system conditions.
- **5.7.3.2** *Warning and caution.* A warning and caution display should indicate the triggering condition. The intent of such a display is to provide the user with a greater probability of detection of the off-normal event than normal observation would provide in the absence of the display.
- **5.7.3.2.1** *Use.* Visual danger signals (i.e., warnings and cautions) shall be used to alert the user that a specific condition exists by providing information about the nature and priority of the condition.
- **5.7.3.2.2** *Characteristics.* Danger signal displays shall be illuminated to be visible under all anticipated lighting conditions.
- **5.7.3.2.2.1** *Different from general lighting.* Danger signal displays shall be conspicuously different from general area lighting.
- **5.7.3.2.2.2** *Specific meaning.* Danger signal displays shall have specific meaning within the operational area in which they are used.
- **5.7.3.4** *Signal integration.* Visual warnings, cautions, and advisories shall be integrated with those presented in other sensory modalities (e.g., auditory, tactile).
- **5.7.3.5** *Priority coding.* To establish the priority of visual signals, discriminatory characteristics, such as flashing, color, shape, symbols, color contrast, size, luminance contrast, and location, shall be used.
- **5.7.3.5.1** *Warning signals.* Visual warning signals shall be presented using flashing red with flash frequency between 3 and 5 hertz with a 50 percent duty cycle.
- **5.7.3.5.1.1** *Flashing text.* If warning text that must be read flashes, the text should alternate intensities with a lower intensity that remains legible.
- **5.7.3.5.1.2** *Synchronized flash rate.* The flash rate for all such warning signals shall be synchronized.
- **5.7.3.5.1.3** *Use with caution signals.* If used in conjunction with caution signals, warning signals shall be coded to be easily distinguished from caution signals.
- **5.7.3.5.2** *Caution signals.* If cautions take the form of flashing text, the text shall flash at a rate not greater than 2 hertz with an ON/OFF interval of approximately 70 percent on to ensure text legibility when required.
- **5.7.3.5.2.1** *Color.* Visual caution signals shall be yellow.
- **5.7.3.5.2.2** *Rapid identification.* A minimum of two discriminatory characteristics shall be employed to ensure rapid identification and interpretation of caution signals.
- **5.7.3.6** *Text height.* Text for visual warning and caution signals shall be presented using characters between 8.7 and 17.4 milliradians (30 and 60 minutes of subtended arc) as measured from the longest anticipated viewing distance with the larger size used where conditions may be adverse.
- **5.7.3.7** *Co-location.* Warning and caution signals and the information required to respond to them shall be grouped in a single location.
- **5.7.3.7.1** *Grouped separately.* When textual information about warning conditions is listed in a single location, warnings and caution information shall be grouped separately.
- **5.7.3.7.2** *Sorting options.* The user shall have the option to sort warning and caution messages by priority, chronological, or recency order.
- **5.7.3.8** *Master warning, caution, and advisory lights.* Master warning, caution, and advisory signals shall be set apart from signals that show the status of the subsystem components when used to indicate the condition of an entire subsystem, except as required for maintenance-only displays. For aviation systems, use MIL-STD-411.
- **5.7.3.9** *Additional warnings.* Additional warnings shall be indicated by redundant coding.
- **5.7.3.10** *False and nuisance alerts.* The system should be designed to minimize the effects of undetected, false, or nuisance alerts or any condition that would cause a user to perceive an alert signal to be energized when it is not.
- **5.17.7.1** *Information presentation using speech output.* Where possible, speech outputs should be used in applications that are expected to be used in conjunction with another activity that requires focused visual attention or hands-free response.
- **5.17.7.1.1** *Speech output design.* Design of speech displays shall conform to the criteria in 5.3.10.
- **5.17.7.1.3** *Use.* Speech recognition should be used when:
  - a. The consequences of recognition errors are low.
  - b. Identifying and correcting errors would be easy.
  - c. Use is expected to be infrequent.
  - d. The device has an alternative method of input so that speech recognition can be turned off when it is not desired.
- **5.17.7.2.1** *Common method use.* Visual displays of communication (e.g., chat) shall be provided under the following conditions:
  - a. The message is very complex, long, or may need to be referred to at a later time.
  - b. When the message does not necessarily call for immediate action (e.g., can be addressed after receiver has finished current task).
  - c. When the auditory system of the receiver is overburdened.
  - d. When the environment is too noisy to ensure reliable receipt of an aural message.
  - e. When the receiver will remain in a position where they can continue to watch the visual displays.
  - f. When joint communication is required (particularly with non-native English speakers).
- **5.17.7.2.2** *Multimodal communication.* Visual displays of communication (e.g., chat) shall not be the only means of real-time communication of critical information if high workload situations are to be expected. See 5.3.1 for situations in which auditory information should be provided to the user.
- **5.17.7.2.3** *Notifications.* Audio notifications should be programmable and available for instances such as arrival of new message, message from certain individuals, and messages that contain certain key words.
- **5.17.7.2.4.1** *Saving text.* Message text should automatically be saved for long-term storage. Additionally, a mechanism for short-term saving of text should be implemented for use during a session.
- **5.17.7.2.4.2** *Receipt after viewing.* A confirmation that the communicated message has been seen should be sent to the sender during real-time, two-way discussions.
- **5.17.7.2.4.3** *Promoting typing efficiency.* Features such a voice-to-text, auto-fill, hotkeys, and preformatted messages should be used to increase chat efficiency by reducing typing time.
- **5.17.7.2.4.4** *Text-to-speech.* The contents of the message should be able to be read via a text-to-speech system if the user is not able to view the window.
- **5.17.7.2.4.5** *Chat integration.* Chat windows should be integrated into primary displays that require attention to help keep user focus on the displays.
- **5.17.7.2.4.6** *Standardized lexicon.* A standardized lexicon should be provided to ensure that information in the message can be understood across staffs.
- **5.17.7.2.5** *Audio displays.* Audio displays may be used as part of the information presentation, where:
  - a. The common mode of visual display is restricted by overburdening or user mobility needs and it is desirable to cue, alert, or warn the user.
  - b. The user will be provided feedback after control actuation, data entry, or completion of timing cycles and sequences.
- **5.17.7.2.6** *Supportive function.* Audio signals used in conjunction with visual displays shall be supplementary to the visual signals.
- **5.17.7.2.7** *Direct user.* Audio signals shall be used to alert users to the appropriate visual display.
- **5.17.7.2.8** *Signal characteristics.* Signals may be one-time or intermittent.
- **5.17.7.2.9** *Intermittent signals.* Intermittent signals shall be automatically terminated when no longer applicable or terminated by user control. For more information on signal characteristics, see 5.3.4.
- **5.17.7.2.10** *Alarm settings.* Alarm settings shall be in accordance with the criteria in 5.17.7.2.10.1 and 5.17.7.2.10.2.
- **5.17.7.2.10.1** *Customized settings.* When alarm settings are user-customized, users shall be permitted to view status information regarding current settings established as critical.
- **5.17.7.2.10.2** *Alarm status.* Alarm status information shall be provided where responsibility may be shifted from one user to another (e.g., as in changes of shift).
- **5.19.1.11.2** *Disabling audible alerts.* Users shall have the ability to disable or silence audible alerts and signals with respect to proximity to hostile environments.
- **5.19.1.11.3** *Audible alert status.* The PED should provide an indication of alert status, such as an icon indicating silent mode.
- **5.19.4.5** *Alerts and notifications.* PEDs shall have the capability to set alert type (e.g., audible, visual, tactile).


## 9. Windows, portals and page structure

Requirements written specifically for web portals and portlets (5.17.8) and for displays, titles and frames.

- **5.12.3.4.8** *Data space.* Interfaces and navigation aids shall provide information to enable the user to know where they are in the data space.
- **5.17.8.1** *Information from diverse sources.* Web portals shall bring information together from diverse sources in a single location.
- **5.17.8.2** *Dedicated area.* Each information source shall get its own dedicated area on the page for displaying information in a portlet.
- **5.17.8.2.1** *Title.* The title of a portlet should be a short descriptive phrase that clearly indicates the subject of the portlet (see figure 95).
- **5.17.8.2.1.1** *Multiple screens.* If the portlet presents multiple screens of content, a name that indicates the particular screen should be included with the portlet title or appear as a heading in the content area of the portlet.
- **5.17.8.2.1.2** *Screen name part of title.* When a screen name is part of the title, the format should include the portlet name followed by the screen name using the format “Portlet Name – Screen Name”.
- **5.17.8.2.1.3** *Screen name as part of portlet title.* When a screen name is included in the portlet title, it should not be duplicated as a heading in the content area of the portlet.
- **5.17.8.2.2** *Title bar.* The title bar of a portlet shall include action buttons (i.e., decorations) that allow the users to perform the following functions:
  - a. Detach – Allows the user to open the portlet in a separate browser window.
  - b. Edit – Allows the user to open a page for changing the appearance or content of the portlet.
  - c. Help – Allows the user to open a page with information about how to use the portlet and its functions.
  - d. Maximize – Allows the user to maximize the portlet to the full size of the browser window.
  - e. Open and close – Allows the user to toggle the portlet between displaying the full page and displaying the title bar only.
  - f. Properties – Allows the user to open a page with information about the portlet (e.g., version, author).
  - g. Refresh – Allows the user to update the contents of the portlet when the content may change over time.
  - h. Remove – Allows the user to remove the portlet from a page.
  - i. Restore down – Allows the user to return a maximized portlet to its original size.
- **5.17.8.3** *Action buttons.* Portlet action buttons shall be presented in the same order in each portlet in a portal.
- **5.17.8.4** *Number of portlets.* A page should not contain more than seven portlets.
- **5.17.8.5** *Vertical height.* The portlets should fit within three or fewer screens in vertical height in order to minimize the amount of scrolling required to view them.
- **5.17.8.6** *Support for multiple tasks.* A portlet that supports tasks within a page should be available in multiple pages in which users might be required to support those same tasks.
- **5.17.8.7** *Portlets for specific roles.* The portlets that are common to specific roles should be access-controlled via privilege levels. For users who do not have permission to see certain portlets or groups of tasks associated with a particular role, the tab or other control that provides access to that content should be hidden and unavailable for retrieval by users lacking the relevant permissions.
- **5.17.8.8** *Fixed-width portlets.* A portlet may be a fixed-width, or it may adjust to fit the width of the column where it is placed. A fixed-width portlet should be used in layouts with fixed-width columns. A variable-width portlet may be used in layouts with either fixed- or variable-width columns and self-adjust to fit the fixed width when used in a fixed-width portlet.
- **5.17.8.9** *Variable-width portlets.* Variable-width portlets shall display all content correctly regardless of portlet size.
- **5.17.8.10** *Access control.* Portals should support public key infrastructure (PKI)-based access so users can log in once and automatically be authenticated to all components and applications to which they have access.
- **5.17.8.11** *Public pages.* A portal shall include a public page that users can access prior to logging on to the portal.
- **5.17.8.12** *Public page content.* The public page should provide organizational information (e.g., mission overview, points of contact), information about how to register for a certificate or obtain an account, and either a means for direct log-in or a link to the log-in screen for the portal.
- **5.17.8.13** *Personalization.* Users should be allowed to make changes to unrestricted portal pages or to add or delete default and customize pages when permitted by the portal owner.
- **5.17.8.14** *Content areas.* Those content areas, which can be changed, shall be predefined and selectable.
- **5.17.15.4** *Critical messages in dialog boxes.* Messages that are critical or that require user acknowledgment (e.g., error messages) shall appear in their own dialog boxes.
- **5.17.15.5** *Page numbering.* Each page of a multiple-page display should be labeled to identify the currently displayed page and the total number of pages, if applicable (e.g., “Page 2 of 5”).
- **5.17.15.6** *Display titles and headers.* Each display shall be labeled with a title or header.
- **5.17.15.6.1** *Unique.* The title or header shall be unique within the system.
- **5.17.15.6.2** *Position.* The title or header should be positioned at the top left corner or centered on the page.
- **5.17.15.6.3** *Content.* The title or header shall briefly describe the contents or purpose of the page.
- **5.17.15.6.4** *Meaningful.* The title or header shall be meaningful enough to be learned and remembered easily.
- **5.17.15.7** *Use of frames.* Frames shall border the window.
- **5.17.15.7.1** *Position.* Frame identification shall be prominently displayed in the top left corner of the frame.
- **5.17.15.7.2** *Size.* Frame identification shall not exceed the size of the frame when resized to its minimum size.
- **5.17.15.7.3** *Meaningful.* Frame identification shall be meaningful enough to be learned and remembered easily.
- **5.17.15.7.4** *Content.* Frame identification shall describe the content within that frame.
- **5.17.15.7.5** *Separation.* At least one blank line shall separate the frame identification and the body of the frame.
- **5.17.15.8.1** *Grouping.* All displays necessary to support a user activity or sequence of activities shall be grouped together.
- **5.17.15.8.2** *Frequency of use.* Displays used most frequently shall be grouped together and placed in the optimum visual zone.
- **5.17.15.8.3** *Importance.* Important or critical displays shall be located in the optimum projected visual zone or otherwise highlighted.
- **5.17.15.8.4** *Function and sequence.* Displays shall be arranged in relation to one another according to their sequence of use or the functional relations of the components they represent such that order in functional groups provide a viewing flow from left-to-right or top-to-bottom. This requirement does not apply to master warning, caution, or advisory indicators.
- **5.17.15.8.5** *Consistency.* The arrangement of displays within a system shall be consistent in principle from one application to another.

> Note: 5.17.8.2.1 refers to *figure 95* (example of a portlet) in the source standard.


## 10. Information content, format and presentation

Content, abbreviations, quantitative data, formats, grouping, text, tables, lists and information organization.

- **5.17.14.1** *Amount of information.* The amount of information required shall be in accordance with the criteria in 5.17.14.1.1 through 5.17.14.1.5.
- **5.17.14.1.1** *Scope.* Information displayed to a user shall be clear enough to allow the user to perform the intended mission.
- **5.17.14.1.2** *Precision.* Information displayed shall be limited to the precision required to perform specific actions or to make decisions.
- **5.17.14.1.3** *Context for displayed data.* Each data display shall provide contextual information, including data from prior displays as necessary.
- **5.17.14.1.4** *Combining operator and maintainer information.* Operator and maintainer information shall not be combined in a single display unless the information content, format, and timeliness support the needs of both users.
- **5.17.14.1.5** *Information density.* Critical information shall be separate and distinct enough to be salient from non-critical information.
- **5.17.14.2** *Presentation of information.* Information shall be presented in accordance with the criteria in 5.17.14.2.1 through 5.17.14.2.7.
- **5.17.14.2.1** *Plain text.* Information shall be displayed in language appropriate to the user’s level of training and be as specific as possible to the user’s particular application.
- **5.17.14.2.2** *Abbreviations.* Abbreviations should be in accordance with JSSG-2010.
- **5.17.14.2.3** *New abbreviations.* New abbreviations, if required, shall be developed using logical rules.
- **5.17.14.2.4** *Distinctive.* Abbreviations shall be distinctive to avoid confusion.
- **5.17.14.2.5** *Consistent.* Words shall have only one consistent abbreviation.
- **5.17.14.2.6** *Punctuation.* No punctuation shall be used in abbreviations.
- **5.17.14.2.7** *Definitions.* Definitions of all abbreviations, mnemonics, and codes shall be available to access within the system.
- **5.17.14.3** *Quantitative information.* Quantitative information shall be displayed in accordance with the following rules.
- **5.17.14.3.1** *Numeric digital displays.* Digital displays shall be used when the task requires identifying a specific numeric quantity.
- **5.17.14.3.2** *Pattern display.* Analog or graphic displays shall be used when the comparison of quantities is important.
- **5.17.14.3.3** *Comparison of quantities.* Numeric digital displays shall not be used as the only display of information when the comparison of quantities is important for accurate interpretation.
- **5.17.14.3.4** *Hybrid displays.* Hybrid displays, which combine both digital and analog or graphic displays, shall be provided when a task requires both identification of a specific numeric quantity and the ability to compare quantities.
- **5.17.14.3.5** *Units.* Displays of quantitative information shall include units of measure.
- **5.17.15.1** *Critical data.* Critical data shall not be obscured by pagination or scrolling.
- **5.17.15.2** *Readily usable form.* Information shall be presented to the user in a directly usable form that does not require the user to transpose, compute, interpolate, or mentally translate the information into other units, number bases, or languages.
- **5.17.15.3.1** *Display output consistency.* The content of displays and their formats shall be consistent within a system and across systems that are used by the same users.
- **5.17.15.3.2** *Consistency between output and input.* The same format shall be used for inputting data and displaying the output within a task (e.g., a date entered in the form of “mm/dd/yyyy” will be outputted in the same form).
- **5.17.15.3.3** *Recurring data field labels.* Recurring data fields within a system and across systems used by the same users shall have consistent labels.
- **5.17.15.3.4** *Comparative data fields.* Data fields to be compared on a character-by-character basis shall be positioned one above the other with alignment of characters to be compared.
- **5.17.15.3.5** *Position of recurring data fields.* Recurring data fields within a system and across systems used by the same users shall occupy consistent relative positions across displays.
- **5.17.15.3.6** *Consistency of data entry formats.* Whenever data has to be entered by hand, order and format should be as consistent as possible.
- **5.17.17** *Grouping within a display.* Information or data shall be ordered in accordance with a specific purpose or context. When the purpose or context does not suggest a method of grouping information or data on a display, they may be grouped according to the following guidelines listed in order of preference.
- **5.17.17.1** *Order and sequences.* When data fields have a naturally occurring order, such as chronological or sequential, such order should be reflected in the format organization of the fields.
- **5.17.17.2** *Grouped by importance.* Displayed data items that are critical or require immediate user response may be ordered at the top of the list.
- **5.17.17.3** *Ordered by function.* Sets of data that are associated with specific questions or related to particular functions may be ordered together to signify those functional relationships.
- **5.17.17.4** *Ordered by frequency.* Data items used more frequently than others may be ordered at the top of the list.
- **5.17.18.2** *Alphanumeric character and symbol sizes.* When measured from the greatest anticipated viewing distance, the height of alphanumeric characters and pictorial symbols shall subtend not less than 2.9 milliradians (10 minutes) of visual angle and should subtend not less than 4.5 milliradians (15 minutes) of visual angle.
- **5.17.18.4** *Character stroke width.* Assuming that the character height conforms to 5.17.18.2, stroke width shall be not less than 0.0834 nor greater than 0.1667 of the number of pixels used for character height.
- **5.17.18.5** *Character width.* Character width shall be approximately 0.9 of the height.
- **5.17.18.6** *Symbol size and image quality for complex shapes.* The size of a symbol or graphic shall be such that all text or graphics embedded within the symbol (e.g., label within symbol) shall subtend not less than 2.9 milliradians (10 minutes) of visual angle from the greatest anticipated viewing distance.
- **5.17.18.7** *Font characteristics.* Font style shall enable discrimination of similar characters (e.g., letter l/number 1, letter Z/number 2).
- **5.17.18.7.1** *Standard font.* A common standard font shall be used (e.g., Arial, Times New Roman, Courier, Verdana).
- **5.17.18.7.2** *Sans-serif.* Where users must read under adverse conditions (e.g., poor lighting), a sans-serif style shall be used (e.g., Arial, Verdana, Helvetica).
- **5.17.18.10.1** *Alphanumeric organization.* When five or more alphanumeric characters are presented, they shall be separated into groups per their natural organization and punctuation schemes (e.g., “mm/dd/yyyy”, “(123) 555-6789”, and “123-45-6789”).
- **5.17.18.10.2** *Combination of letters and digits.* When a code consists of both letters and digits, common character types shall be grouped together.
- **5.17.18.10.3** *Blocking.* When five or more alphanumeric characters without natural organization are displayed, characters shall be grouped in blocks of three to five characters.
- **5.17.18.10.4** *Separation of groups.* When five or more alphanumeric characters without natural organization are displayed, groups of characters and digits shall be separated from one another by a minimum of one blank space.
- **5.17.18.10.5** *Long numeric fields.* When five or more numeric characters without natural organization are displayed, long numeric fields shall be punctuated with commas, spaces, or slashes.
- **5.17.18.10.6** *Leading zeros.* Leading zeros shall not be used in numerical data.
- **5.17.19** *Text and program editing.* Text and program editing shall be in accordance with the criteria in 5.17.19.1 through 5.17.19.12.
- **5.17.19.1** *Buffer.* When appropriate, when inserting characters, words, or phrases (e.g., editing), items to be inserted shall be collected in a buffer area and displayed in the prescribed insert area of the screen for subsequent insertion by user command.
- **5.17.19.2** *Edit commands.* Edit commands, such as MOVE, COPY, PASTE, INSERT and DELETE, for adding, inserting, or deleting text and program segments shall be provided.
- **5.17.19.2.1** *Text edit commands.* In text editing, editing commands shall be based on character, word, sentence, paragraph, and higher-order segments.
- **5.17.19.2.5** *Tab controls.* For editing programs or tabular data, cursor tab controls or other provisions for establishing and moving readily from field to field shall be provided.
- **5.17.19.2.6** *Keying edit commands.* Where editing commands are made by keying into the display, the editing commands shall be readily distinguishable from the displayed textual material.
- **5.17.19.3** *Highlighted text.* Where text has been specified to become the subject of control entries (e.g., for underlining, bolding, moving, copying, or deleting), the affected segment of text shall be highlighted to indicate its boundaries.
- **5.17.19.4** *String search.* A string search capability shall be provided to allow the user to specify a string of text (words, phrases, or numbers) and request the computer to advance (or back up) the cursor automatically to the next (or previous) occurrence of that string.
- **5.17.19.5** *String search variance.* The string search capability shall provide the flexibility to select search variance.
- **5.17.19.6** *Automatic word wrap.* An automatic word wrap (carriage return) shall be provided when the text reaches the right margin for entry or editing of unformatted text.
- **5.17.19.7** *Override of word wrap.* User override of automatic word wrap shall be provided.
- **5.17.19.8** *Format control.* A means shall be provided for users to specify required format control features during text entry or editing (e.g., margins, tab settings, line spacing).
- **5.17.19.9** *Predefined formats.* When text formats must follow predefined standards, the required format shall be provided automatically.
- **5.17.19.10** *Text formats as a user option.* Where text formats are a user option, a convenient means shall be provided to allow the user to specify and store the formats that have been generated for particular applications for future use.
- **5.17.19.11** *Frequently used text.* The capability to label and store frequently used text segments (e.g., signature blocks, organizational names, call signs, coordinates), and to later recall (copy into current text) stored segments identified by their assigned labels shall be provided.
- **5.17.19.12** *Head and foot of file.* The means shall be provided to readily move the cursor to the head (beginning) or the foot (end) of the file.
- **5.17.20.1** *Labels.* Every element on a display shall be labeled unless its purpose is intuitively obvious to the intended user population
- **5.17.20.1.1** *Use.* User-requested element identification (e.g., tool tips) is acceptable where space constraints preclude the use of labels.
- **5.17.20.1.2** *Label characteristics.* Labels should adhere to the following requirements:
  - a. Be unambiguously related to the element they describe.
  - b. Be consistent in appearance with all other labels on the display.
  - c. Be readily distinguishable from and more salient than all other elements on the display.
  - d. Use an accentuating technique different than any method used to highlight or code emergency or critical messages (e.g., use upper case to highlight the criticality).
- **5.17.20.2** *Default values.* Default values shall meet the following requirements.
- **5.17.20.2.1** *Use when feasible.* To reduce user workload, default values shall be used where feasible.
- **5.17.20.2.2** *Display automatically.* Currently defined default values shall be displayed automatically in their appropriate data fields with the initiation of a data entry transaction.
- **5.17.20.2.3** *Acceptance.* The user shall be able to indicate acceptance of the default by a single keystroke.
- **5.17.20.2.4** *Replace default value.* The user shall be able to replace any default value during a given transaction without changing the default definition.
- **5.17.20.2.5** *Generate default values.* The user shall have the option of generating default values if the system has not provided them.
- **5.17.20.2.6** *Default options.* Where a series of default values has been defined for a data entry sequence, the user shall be able to accept those default entries.
- **5.17.20.3** *Tabular data.* Tabular data shall meet the following requirements.
- **5.17.20.3.1** *Use.* Tabular data displays shall be used to present row-column data to aid detailed comparison of ordered sets of data.
- **5.17.20.3.2** *Standard formats.* Location of recurring data shall be consistent among all tabular data displayed and common throughout the system.
- **5.17.20.3.3** *Arrangement.* When tabular data are sorted within rows, it should be in increasing order from left to right. Tabular data sorted within columns should be in an order dictated by context.
- **5.17.20.3.4** *Titles.* When tabular data are divided into classifications, the classification titles shall be displayed.
- **5.17.20.3.5** *Vertical extension – titles.* When tabular data extend over more than one page vertically, the columns shall be titled identically on each page.
- **5.17.20.3.6** *Horizontal extension.* Tabular displays should not extend over more than one page horizontally.
- **5.17.20.3.7** *Horizontal extension – titles.* If horizontal extension over more than one page is required, the rows shall be titled identically on each page.
- **5.17.20.3.8** *Distinctive and informative labels.* Rows and columns shall be labeled distinctively to guide data entry.
- **5.17.20.3.9** *Layout of numeric entry.* Users shall be allowed to make numeric entries in tables without concern for justification. The computer shall right-justify integers or justify with respect to a decimal point if present.
- **5.17.20.3.10** *Labeling units of measurement.* The units of displayed data shall be consistently included in the displayed column and row labels.
- **5.17.20.3.11** *Consistent column spacing.* The widths of columns containing the same data elements shall be uniform and consistent within a table and from one table to another.
- **5.17.20.3.12** *Consistent row spacing.* The heights of rows containing the same data elements shall be uniform and consistent within a table and from one table to another.
- **5.17.20.3.13** *Column scanning cues.* A column separation not less than three spaces shall be maintained.
- **5.17.20.3.14** *Row scanning cues.* A group of rows shall be separated at regular intervals of not more than every five lines.
- **5.17.20.4.1** *List arrangement.* Items in lists shall be arranged in a recognizable order, such as chronological, alphabetical, sequential, functional, or importance.
- **5.17.20.4.2** *List lines.* Each item in a list shall start on a new line.
- **5.17.20.4.3** *Vertical extension.* Where lists extend over more than one display page, the last line of one page shall be the first line on the succeeding page.
- **5.17.20.4.4** *Marking multi-line items in a list.* Where a single item in a list continues for more than one line, such items shall be marked in some way (e.g., blank line, indentation) so that the continuation of the item is obvious.
- **5.17.20.4.5** *Vertical ordering in multiple columns.* Where items in a list are displayed in multiple columns, items shall be ordered vertically within each column.
- **5.17.20.4.6** *Arabic numerals.* When listed items will be numbered, Arabic numerals shall be used rather than Roman numerals.
- **5.17.20.4.7** *Hierarchic structure for long lists.* Where lists are long and must extend beyond a single displayed page, a hierarchic structure shall be used to permit the logical partitioning into related shorter lists.


## 11. Coding of information (color, brightness, flash, size, pattern, symbol, shape)

Information coding, with the color requirements applicable to color-coded web content. Aircrew symbology, protective-equipment and night-vision-specific criteria are excluded.

- **5.17.24.1** *Use of coding.* Coding shall be used to facilitate discriminating between individual views (e.g., different modes of operation); identifying functionally related displays; showing the relationships between displays; and identifying critical information, unusual values, changed items, items to be changed, high priority messages, special areas of the display, errors in entry, criticality of command entry, and targets.
- **5.17.24.2** *Meaningful coding.* Meaningful codes shall be used.
- **5.17.24.3** *Common coding conventions.* Commonly used coding conventions should be used.
- **5.17.24.4** *Impact on performance.* Coding shall not reduce legibility or adversely impact user or system performance.
- **5.17.24.5** *Consistent coding.* All coding within the system shall be consistent.
- **5.17.24.6** *Techniques.* The following visual characteristics may be used for coding: color, brightness, flash, size, pattern, location, underlining, symbol, or shape.
- **5.17.25.1** *Use.* Color coding may be employed to differentiate between classes of information in complex, dense, or critical displays or to provide a means of indicating similarity among items (grouping items) along some meaningful dimension.
- **5.17.25.2** *Foveal view only.* Color shall not be used for gaining attention outside the optimum visual field.
- **5.17.25.3** *Consistency.* Color coding shall be used consistently within a display and, where appropriate, across displays of other systems used by the same users.
- **5.17.25.4** *Color customization.* Color customization by users shall be allowed only for information that is not tactically significant.
- **5.17.25.5** *Color recognition.* When the user must recognize categories of information (e.g., represent different variables on a graph, different types of information on a map) a maximum of 11 nameable colors shall be used to represent and distinguish between categorically different information.
- **5.17.25.6** *Color differentiation.* When using color to group elements of a display together (i.e., to show parts are related or different) and the color has no meaning beyond a grouping function, colors should be discriminable.
- **5.17.25.7** *Saturation coding.* Hue saturation may be used to indicate relative intensity (e.g., best, hottest, wettest, safest, deepest). The following are standardized uses of saturation that should be followed:
  - a. Hotter to cooler where saturation changes from red to blue.
  - b. More dangerous to less dangerous – saturation of red increases as danger increases.
- **5.17.25.8** *Color meanings.* Colors shall be associated with the common meanings presented in table XL.
- **5.17.25.8.1** *Cool colors.* Cool colors (those with shorter wavelengths, such as blue or green) shall be used to display information used infrequently and to convey status of background information.
- **5.17.25.8.2** *Warm colors.* Warm colors (those with longer wavelengths, such as red or orange) shall be used to convey action or the requirement for a response.
- **5.17.25.10** *Color-blind users.* If the user population includes color-blind users, every effort should be made for non-confusable colors to be used for coding.
- **5.17.25.11** *Redundant coding.* If non-confusable colors cannot be used for coding for color-blind users, redundant coding shall be used.
- **5.17.25.13** *Object size.* When accurate color perception is required, the major dimension of isolated large symbols shall subtend not less than 8.7 milliradians (30 minutes) of visual angle and should subtend not less than 13.1 milliradians (45 minutes).
- **5.17.25.14** *Object height.* When accurate color perception is required, the height of small symbols and characters should subtend at least 5.8 milliradians (20 minutes) of visual angle, as measured from the longest anticipated viewing distance.
- **5.17.25.15** *Fill symbols.* To enhance detectability and discriminability, color-filled symbols shall be used instead of outlined symbols.
- **5.17.25.16.3** *Text and background pairings for daytime or artificial lighting.* Text 14-point font or less should have a luminance contrast ratio of above 4.5:1. Text larger than 14-point font should have a contrast ratio of at least 3:1. Black or dark text should be on a light background.
- **5.17.25.16.4** *Text and background pairings for dark conditions.* White or light text should be on a dark background.
- **5.17.26.1** *Use.* Brightness intensity coding should be employed only to differentiate between an item of information and adjacent information.
- **5.17.26.2** *Brightness intensity levels.* No more than two levels of brightness shall be used, and each level shall be separated from the nearest other level by not less than a 2:1 ratio.
- **5.17.26.3** *Brightness inversion.* When a capability for brightness inversion is available (so-called “reverse video” where dark characters on a bright background can be changed under computer control to bright on dark, or vice versa), it may be used for highlighting critical items that require user attention. When a capability for brightness inversion is available and when used for alerting purposes, brightness inversion shall be reserved for that purpose and not be used for general highlighting.
- **5.17.27.1** *Use.* Flash coding should be employed to call the user’s attention to mission-critical events only.
- **5.17.27.2** *Duty cycle.* The percentage of “on” time shall be equal to but not less than the percentage of “off” time; a 50 percent duty cycle is preferred.
- **5.17.27.3** *Flash rate.* No more than two flash rates shall be used.
- **5.17.27.3.1** *Flash rate differences.* The two flash rates shall differ by not less than 2.0 hertz.
- **5.17.27.3.2** *Higher flash rate.* The higher flash rate shall be not greater than 5.0 hertz.
- **5.17.27.3.3** *Slower flash rate.* The slower flash rate shall be not less than 0.8 hertz.
- **5.17.27.3.4** *Critical information.* The higher flash rate shall reflect more critical information.
- **5.17.27.4** *Flash rate synchronization.* Items flashing at the same rate shall be synchronized.
- **5.17.27.5** *Flash rate text.* Characters that must be read shall not flash; an adjacent flashing symbol, flashing background, or alternating brightness intensity may be used to add emphasis to text.
- **5.17.27.6** *Flash suppression.* Event acknowledgment or flash suppression control shall be provided.
- **5.17.27.7** *Flashing area.* Only a small area of a display should flash at any time.
- **5.17.28** *Size coding.* No more than three size levels shall be used. The major dimensions of the larger size shall be not less than 150 percent of the major dimension of the smaller.
- **5.17.29** *Pattern and location coding.* Pattern and location coding may be used to reduce user search time by restricting the area to be searched to prescribed segments.
- **5.17.30** *Underlining coding.* Underlining may be employed to indicate unusual values, errors in entry, changed items, or items to be changed.
- **5.17.31.1** *Use.* Symbol coding may be used to enhance information assimilation from data displays.
- **5.17.31.2** *Symbol selection.* Symbols shall be analogs of the event or system element they represent or be in general use and well known to the expected users.
- **5.17.31.3** *Special symbols.* When special symbols are used to signal critical conditions, they shall be used for only that purpose.
- **5.17.31.4** *Markers close to words marked.* When a special symbol is used to mark a word, the symbol shall be separated from the beginning of the word by one space.
- **5.17.32** *Shape coding.* Shape coding may be used for search and identification tasks. When shape coding is used, the codes selected should be based on established standards or conventional meanings.

- **5.17.25.16.1** *Color contrast.* Colored symbols shall differ from their background by not less than 100 ΔE (color difference) (CIE L*u*v*) distances when calculated using the formula below. The elements required for the calculation are the luminance (Y), the UCS coordinates (u', v') for foreground and background colors, and the maximum luminance for the display (YM). As with the (CIE L*u*v*) distances, caution should be used in assessing legibility for characters in colors having small luminance differences. This caution applies not only to characters in color but also to small luminance differences in background colors and for very small luminance differences between characters in color and background in color. Unusually large or small characters may lead to erroneous estimates of legibility.

  ```
  ΔE(Yu'v') = [ (155 · ΔY/YM)² + (367 · Δu')² + (167 · Δv')² ]^(1/2)
  ```

  where ΔY is the difference in absolute luminance between foreground (e.g., text) and background; YM is the maximum luminance of any color on the display (i.e., pure white), in the same units as ΔY; Δu' and Δv' are the differences between foreground and background values of the u' and v' coordinates (in accordance with EIA TEB 26). The values 155, 367, and 167 are empirically derived weights.
- **5.17.25.16.2** *Color differences.* Colors in a set shall differ from one another by not less than 20 ΔE (CIE L*u*v*) distances when calculated using the formula below, with the reference white taken as the display white (D6500 K: u'0 = 0.198, v'0 = 0.468; or 9300 K + 27 MPCD: u'0 = 0.181, v'0 = 0.454).

  ```
  ΔE(L*u*v*) = [ (L*1 − L*2)² + (u*1 − u*2)² + (v*1 − v*2)² ]^(1/2)
  L* = 116 · (Y/Y0)^(1/3) − 16        (1.0 > Y/Y0 > 0.01)
  u* = 13 · L* · (u' − u'0)
  v* = 13 · L* · (v' − v'0)
  u' = 4X / (X + 15Y + 3Z)
  v' = 9Y / (X + 15Y + 3Z)
  ```


## 12. Graphics, dynamic displays, geospatial displays, printing and data transmission

Graphical user interface criteria, graphs and charts, real-time data updates, geospatial displays, printing and message/data transmission.

- **5.2.1.1.3** *Motion.* Graphic display items shall not move faster than 60 degrees (1.05 radians [rad]) (20 degrees [0.35 rad] preferred) of visual angle per second.
- **5.2.1.1.4** *Orientation of objects.* Orientation of displayed objects relative to the user shall be in accordance with the following criteria:
  - a. Displays shall include reference to the vertical or horizontal direction (e.g., “North” or “straight ahead”).
  - b. Orientation of objects relative to each other shall provide cues concerning the objects depicted (e.g., front and back, top and bottom).
  - c. Orientation of objects relative to each other shall provide cues concerning object dynamics (e.g., direction and speed of motion and rotation).
- **5.2.1.1.5** *Flash coding.* Flash coding shall be in accordance with 5.17.27.
- **5.17.21.1** *Graphical user interfaces.* See ANSI/HFES 200 for additional graphical user interface guidance.
- **5.17.21.1.1** *Selecting from displayed attributes.* During graphic data entry, users shall be allowed to specify attributes for displayed elements (e.g., text font, plotting symbol, line type).
- **5.17.21.1.2** *Examples.* The capability to specify attributes for displayed elements should include examples that illustrate the available options.
- **5.17.21.1.3** *Selecting graphic elements.* Users shall be provided some means for designating and selecting displayed graphic elements when manipulation of those elements is required. Normally this function is performed by a pointing device or cursor.
- **5.17.21.1.4** *Displaying current attributes.* The attributes that will be affected by a change shall be highlighted.
- **5.17.21.1.5** *Easy storage and retrieval.* The user shall be provided a means for saving and retrieving graphic data displays.
- **5.17.21.1.6** *Automatic data registration.* The computer shall provide automatic registration or alignment of computer-generated graphic data so that variable data are shown properly with respect to fixed background or data map at any display scale.
- **5.17.21.1.7** *Predefined graphic formats.* Where graphic data must be plotted in predefined standard formats (e.g., target areas on maps, flight plans), templates or skeletal displays shall be provided for those formats to aid data entry.
- **5.17.21.1.8** *Changing size.* When editing graphic data, users shall be provided with the capability to change the size (scale) of any selected element on the display rather than delete and recreate the element in a different size.
- **5.17.21.1.9** *Highlighting critical data.* When a user’s attention must be directed to a portion of a graphic display showing critical or abnormal data, that feature shall be highlighted with some distinctive means of data coding.
- **5.17.21.1.10** *Computer derivation of graphic data.* Common or complex data computations, such as curve fitting or plotting a list of coordinates, should be performed by the system upon user request.
- **5.17.21.1.11** *Normal orientation for labels.* The text on dynamic graphic displays shall remain upright when the displayed image rotates.
- **5.17.21.1.12** *Display of scale.* If a map or other graphic display has zoom functionality, the current zoom level or scale shall be displayed.
- **5.17.21.1.13** *Display accuracy.* If applicable, the current display accuracy shall be displayed.
- **5.17.21.1.14** *Interpolation.* The system shall provide accurate numeric information for any point on the graphic display upon request from the user.
- **5.17.21.1.15** *Unobtrusive grids.* Any displayed grid lines shall be unobtrusive.
- **5.17.21.1.16** *Obscuration of data elements.* Displayed grid lines shall not obscure data elements.
- **5.17.21.1.17** *Grid line suppression.* Users shall be provided the option to suppress or display gridlines.
- **5.17.21.2** *Graphs and charts.* Graphs and charts shall be in accordance with the criteria in 5.17.21.2.1 through 5.17.21.2.6.
- **5.17.21.2.1** *Graph axes.* The axes of graphs shall be labeled.
- **5.17.21.2.2** *Trend lines.* When trend lines are to be compared, they should be displayed on a single graph.
- **5.17.21.2.3** *Reference index.* When a user must compare graphic data to some significant level or critical value, that level or value shall be included in the display.
- **5.17.21.2.4** *Data annotation.* When precise reading of a graphic display may be required, the capability to supplement the graphic representation with the represented numeric values shall be provided.
- **5.17.21.2.5** *Consistent scaling.* When users must compare graphic data across a series of charts, the same scale shall be used for each chart.
- **5.17.21.2.6** *Single scale only.* Where graphs are presented, only a single scale shall be shown in each axis, rather than including different scales for different curves in the graph.
- **5.17.21.3.1** *Drawing shapes.* When shape drawing is required, users shall be provided with aids for drawing shapes.
- **5.17.21.3.2** *Drawing lines.* When line drawing is required, users shall be provided with aids for drawing straight line segments.
- **5.17.21.3.3** *Drawing intersecting lines.* When a user draws the end of a line segment in close proximity to a displayed element, the system should automatically connect between the line segment and displayed element.
- **5.17.21.3.4** *Override.* The user shall be provided a means to override the automatic line segment connection feature.
- **5.17.21.3.5** *Drawing lines and figures with numeric coordinates.* When lines or figures must be drawn to represent numeric coordinates, computer aids shall include methods for entering the coordinates and selecting the appropriate units for those coordinates.
- **5.17.22.1.1** Changing values. Alphanumeric values that change in real time shall be updated according to the criteria in 5.17.22.1.1.1 through 5.17.22.1.1.2.
- **5.17.22.1.1.1** *Reliable reading.* Values that the user must reliably read shall not be updated more often than once per second.
- **5.17.22.1.1.2** *Identify rate of change.* Changing values that the user uses to identify rate of change or to read gross values shall be updated three to four times per second.
- **5.17.22.1.2** *Update rate.* Update rate should be in accordance with programmatic-specific requirements.
- **5.17.22.1.2.1** *Display freeze.* A display freeze mode shall be provided to allow viewing any selected frame that is updated or advanced automatically by the system.
- **5.17.22.1.2.2** *Resume from freeze.* A single action option shall be provided to allow resumption at the freeze point or at the current real-time point.
- **5.17.22.1.2.3** *Display freeze prohibition.* The user shall be prohibited from entering display freeze mode when doing so would have an adverse effect on the mission being performed by the system.
- **5.17.22.1.2.4** *Freeze feedback.* A label shall be provided to remind the user when the display is in the freeze mode.
- **5.17.22.1.2.5** *Timeliness.* Displays requiring refreshed information (e.g., head-up displays) shall be updated in a synchronous manner.
- **5.17.22.1.2.6** *Advisory and alerting.* Devices displaying simultaneous and integrated information (e.g., multifunction displays) shall alert or cue operating personnel to information prior to it becoming critical within the display.
- **5.17.22.2.1** *Hard copy.* The system shall provide the capability for the user to obtain a paper copy of the exact contents of the alphanumeric or digital graphic display, where mass storage is restricted, mass stored data that can be lost by power interruption, or where record keeping is required or desirable.
- **5.17.22.2.2** *Display print.* The system shall provide the capability for the user to print a display screen by simple request (e.g., PRINT-SCREEN).
- **5.17.22.2.3** *Print page.* The system shall provide the capability for the user to request printing of a single page or sequence of pages by specifying the page numbers or printing all pages without specifying page numbers.
- **5.17.22.2.4** *Text displayed as printed.* The system shall provide the capability for the user to display text as it will be printed, including underlining, boldface, subscript, superscript, special characters, special symbols, and different styles and sizes of type.
- **5.17.22.2.5** *Flexible printing options.* In printing text, the system shall provide the capability for the user to select among available output formats (e.g., line spacing, character size, margin size, headers, footers) and to specify the pages of a document to be printed.
- **5.17.22.3.1** *Functional integration.* Data transmission functions shall be integrated with other information handling functions within a system such that a user is able to transmit data using the same computer system and procedures used for general entry, display, and other processing of data.
- **5.17.22.3.2** *Consistent procedures.* Procedures for preparing, sending, and receiving data and messages shall be consistent from one transaction to another and consistent with procedures for other information handling tasks.
- **5.17.22.3.3** *Minimal memory load on users.* The system shall provide computer aids for automatic insertion of standard information, such as headers and distribution lists, in order to minimize memory load on the user.
- **5.17.22.3.4** *Messages.* Messages shall be in accordance with the criteria in 5.17.22.3.4.1 through 5.17.22.3.4.8.
- **5.17.22.3.4.1** *Stored message forms.* Where message formats conform to a defined standard or are predictable in other ways, pre-stored forms shall be provided to aid users in message preparation.
- **5.17.22.3.4.2** *Incorporate existing files.* Users shall be allowed to incorporate an existing data file in a message, combine several files into a single message for transmission, and combine stored data with new data when preparing messages for transmission.
- **5.17.22.3.4.3** *Reenter data.* It shall not be necessary to reenter any data already entered for other purposes.
- **5.17.22.3.4.4** *Interrupt.* Users shall be allowed to interrupt message preparation, review, or disposition and then resume any of those tasks from the point of interruption.
- **5.17.22.3.4.5** *Data group labels.* Each individual data group or message shall contain a descriptive title, phrase, word, or similar device to designate the content of the group or message.
- **5.17.22.3.4.6** *Location of message labels.* Labels shall be consistently located adjacent to (and preferably above or to the left of) the data group or message they describe.
- **5.17.22.3.4.7** *Emergency or critical messages.* Labels shall use an accentuating technique different and easily distinguished from the method used to highlight or code emergency or critical messages.
- **5.17.22.3.4.8** *Originator identification.* Except for broadcast communication systems, the transmitter of each message in inter-user communications shall be automatically identified, if possible.
- **5.17.22.3.5** *Addresses.* When users must specify the address for messages, prompting shall be provided to guide the user in the process.
- **5.17.22.3.6** *Directories.* Users shall be provided with a built-in directory showing all acceptable forms of message addressing for each destination in the system and for links to external systems.
- **5.17.22.3.6.1** *Computer aids.* Computer aids shall be provided so that a user can search an address directory by specifying a complete or partial name.
- **5.17.22.3.6.2** *Extract addresses.* Users shall be able to extract selected addresses from a directory for direct insertion into a header in order to specify the destination(s) for a message.
- **5.17.22.4.1** *Use.* Geographic displays shall only be used for data that can be geo-referenced.
- **5.17.22.4.2** *Types.* Editable geographic coordinate displays allow users to enter a specific geographic location in one of several coordinate systems, such as decimal degrees; military grid reference system (MGRS); or degrees, minutes, seconds (DMS). Non-editable displays are system-generated values that match system or user-selected geographic coordinate system format.
- **5.17.22.4.3** *Default geographic coordinate systems.* Geospatial displays should have the option to save a default geographic coordinate system (e.g., decimal, MGRS, DMS).
- **5.17.22.4.4** *Geospatial display options.* Geographic displays are normally composed of a base map, overlays, and individual drawings. To permit customization and decluttering functionality, a list of options of available visual elements should be available to the user. Functionality of these options may include, but is not limited to, the following elements:
  - a. Toggle element visibility – the ability to show and hide individual visual elements.
  - b. Opacity – the ability to control how many visual elements can be seen when overlapped with other visual elements.
  - c. Precedence – the ability to set the order in which visual elements are layered over each other.
  - d. Hierarchical grouping – the ability to apply the above functionality to both individual visual elements and logical groupings, as required.
- **5.17.22.4.5** *Geospatial display drawings.* If the user is provided the capability to draw on the geospatial display, the following functionality should be provided:
  - a. Manipulation – drawings should be able to be moved, resized, or deleted after being initially placed. Resizing should include the capability to keep the aspect ratio locked.
  - b. Zooming and panning – the user should be able to zoom and pan the underlying base map while drawing.
  - c. Editing – relevant drawing properties (e.g., color, opacity, labeling) should be editable before and after the drawing is made.
- **5.17.22.4.6** *Relative positioning.* Drawings should be able to be aligned relative to other visual elements, such as other drawings or a grid overlay.


## 13. Display legibility, contrast and small-screen adaptation

Visual-display criteria that apply to the rendering of the web HMI (contrast, polarity, user-adjustable display settings, small screens).

- **5.2.1.2.3** *Luminance control.* Luminance shall be adjustable so that display content is visible and useable over the expected range of ambient illumination.
- **5.2.1.3** *Display contrast.* Display contrast shall be such that the display content is visible over the entire range of the expected ambient illumination.
- **5.2.2.6** *Image polarity.* To maintain dark adaptation, displays shall use light characters on a dark background when ambient light levels are less than approximately 0.1 lx (0.009 fc); otherwise, dark characters on a light background shall be used.
- **5.2.2.7** *Contrast ratio.* The contrast ratio between characters and a background shall be 6:1 or greater (10:1 or greater is preferred).
  - a. For applications related to ship bridge displays, see 5.18.
  - b. For applications related to cockpit displays, refer to guidance in MIL-HDBK-87213 (tables in MIL-HDBK-87213 may require adjustment to account for the assumption of 70 percent transmissivity of an aircraft bubble canopy).
  - c. A control shall be provided to vary the contrast between the characters and the background.
  - d. For large-screen displays, see 5.2.2.12.
- **5.2.2.10.2** *Adapt formatting.* When converting a display from traditional size to small-screen, the design should adapt (not copy) the current system design.
- **5.2.2.10.3** *Font characteristics.* Font size should be large enough to discriminate between similar characters (e.g., letter I to number 1 and letter Z to number 2) and shall conform to the requirements of 5.17.18.2.
- **5.2.2.10.3.1** *Standard font.* A common standard font should be used (e.g., Arial, Times New Roman, Courier, or Verdana).
- **5.2.2.10.3.2** *Sans serif.* Where users must read displays quickly under adverse conditions (e.g., poor lighting), a sans serif style shall be used (e.g., Arial, Verdana, or Helvetica).
- **5.2.2.10.4** *Text organization.* To increase ability to skim text, subtitles and hypertext shall be used in text passages that are longer than a single screen.
- **5.2.2.10.4.1** *Variable length lines.* Variable length lines shall be avoided by use of hyphenation of words at line breaks to improve readability on small screens.
- **5.2.2.10.4.2** *Scrolling markers.* When content cannot be displayed in one screen, scrolling markers shall be provided to enable users to identify where they are on the page.


## 14. Automation and supervisory control

Applicable when the web HMI supervises or controls automated functions.

- **5.12.3.1.2.1** *Performance improvement.* Functions shall be automated only if they improve user or system performance, user safety, or user workload.
- **5.12.3.1.2.2** *User focused.* Automation shall be used to support the user where appropriate (human-centered automation) and not implemented simply because the technology is available (technology-centered automation).
- **5.12.3.2.1** *User in command.* Automated systems shall not remove the user from the command role.
- **5.12.3.2.2** *User involvement.* To maintain engagement and situation awareness, users shall be given an active role through relevant and meaningful tasks in the operation of a system regardless of the level of automation being employed.
- **5.12.3.2.3** *Situation awareness.* The system shall provide the user situation awareness regarding system state (e.g., position, location), capability (e.g., health, readiness), and, where possible, a projection of system status in a timely manner.
- **5.12.3.2.3.1** *System state parameters.* The system shall provide the user with system state parameters that are appropriate to the user’s current role and task.
- **5.12.3.2.3.2** *Current situation.* The system shall provide the user with its current situation within both functional and mission contexts as appropriate to the user’s current role and task.
- **5.12.3.2.3.3** *Future system states.* The system should provide the user with likely future system states based on the current situation.
- **5.12.3.2.4** *Predictability.* Automated systems shall behave predictably so that the user knows the purpose of the automation and how the operation will be affected by that automation.
- **5.12.3.2.5** *Consistency.* The way that automated systems interact with their users shall reflect a high degree of consistency within and between systems.
- **5.12.3.2.6** *Intuitive.* Automated systems and associated information displays should be easy to understand, learn, use, and should meet user expectations.
- **5.12.3.2.7** *Input and setup.* Automated systems shall provide a way to check automation setup and to check information used as input for the automated system.
- **5.12.3.2.9** *Multiple systems.* Multiple systems designs shall meet the criteria in 5.12.3.2.9.1 and 5.12.3.2.9.2.
- **5.12.3.2.9.1** *Controls and displays.* Controls and displays shall be standardized across the different platforms that the same users are expected to control, including users who may be reassigned to or from other platforms.
- **5.12.3.2.9.2** *Terminology.* Terminology shall be standardized across the different platforms that the same users are expected to control, including users who may be reassigned to or from other platforms.
- **5.12.3.2.10.1** *Recovery.* Automation shall maintain sufficient user engagement to allow the user to safely recover during emergencies or operate the system manually if the automation fails.
- **5.12.3.2.10.3** *Override capability.* The user shall be provided the ability to override, shut down, or neutralize the automated system.
- **5.12.3.3.2** *High-risk actions or decisions.* For system tasks associated with greater uncertainty and risk, automation shall not proceed beyond the level of recommending a preferred decision or action alternative as specified (see 6.2).
- **5.12.3.4.1** *Relationship with user task.* The relationships among displays, controls, decision aids, information structure, and user tasks should be clear to the user.
- **5.12.3.4.2** *Clear roles and responsibilities.* The automated system shall clearly indicate whether the user or system hardware and software is supposed to perform a particular task at a specific time.
- **5.12.3.4.2.1** *Changes of state.* The interface shall provide clear indication of all changes to the state and level of automation, particularly when a system failure causes mode reversion, or the operator makes a mode change.
- **5.12.3.4.2.2** *Appropriate to level and mode of control.* The information elements appropriate to the level and mode of control shall be made salient to the operator as function allocation and level of automation changes.
- **5.12.3.4.2.3** *Return to manual control.* When the automation determines that the operator should return to manual control, the system should provide clear indications of the appropriate information and control actions required.
- **5.12.3.4.3** *Continuous feedback.* The system should provide an appropriate level of feedback for systems control, status, and outputs under normal operations as well as situations of system malfunction.
- **5.12.3.4.4** *System response.* Automated system responses to user commands shall be brief and unambiguous. System responses shall be in accordance with 5.1.2.1.6.
- **5.12.3.4.5** *Visualize consequences of decisions.* The automated system should provide information to the user to visualize the expected outcome of a decision, whether made by the user or the automated system.
- **5.12.3.4.6** *Consistency.* The behavior of automated systems and interfaces should be consistent with the expectations and understandings of users.
- **5.12.3.4.7** *Organization.* Automation interfaces should be organized to match user expectations, task processes, and system capabilities.
- **5.12.3.4.10** *Timeliness.* Dynamic information (information that changes over time) shall be presented in real time and on demand to ensure accurate and timely decision making.
- **5.12.3.4.11** *Error checking.* Displays and data entry devices should provide a means to cross-check the accuracy of data entered manually.
- **5.12.3.4.12** *Automation modes.* Mode designs shall meet the criteria in 5.12.3.4.12.1 through 5.12.3.4.12.7.
- **5.12.3.4.12.1** *Identification.* When control, display, or automation functions change in different modes of operation, the system shall provide the user a clear indication of the change and current mode, function identification, and status.
- **5.12.3.4.12.2** *Accessibility.* Frequently used modes should be more accessible than infrequently used modes.
- **5.12.3.4.12.3** *Number of modes.* The number of different modes for a given system should be minimized.
- **5.12.3.4.12.4** *Switching.* The user shall be able to easily switch between modes.
- **5.12.3.4.12.5** *Consistent.* Features and functions that are common between display modes shall be consistent.
- **5.12.3.4.12.6** *Alerts.* The automated system should alert the user when a particular mode may be potentially hazardous.
- **5.12.3.4.12.7** *Inadvertent activation.* The automated system should safeguard against the inadvertent activation of a potentially unsafe mode.
- **5.12.3.4.13** *Monitoring functions.* The system should be designed so that users are involved in active control and monitoring of the automated systems and the functionality of its hardware and software, including the display of status and trend information, as needed.
- **5.12.3.4.13.1** *Changing data.* Changing data that must be monitored by the users should be displayed in a graphic format.
- **5.12.3.4.13.3** *Multiple displays.* When users must monitor multiple displays, notification of important events should occur in the same single physical display in order to promote effective centralized monitoring performance. Important events should occur in the same location within and across displays in order to promote effective monitoring performance.
- **5.12.3.4.13.4** *Monitoring indication.* Automated systems that are without incident for long periods of time should provide some type of indication that the automation is still monitoring the system.
- **5.12.3.4.13.5** *User interactions.* Automated systems should be able to monitor user interactions and to warn of user errors.
- **5.12.3.4.14.4** *Feedback.* To promote successful situation awareness of the automated system, the user shall be given immediate feedback to command and control orders. System responses shall be in accordance with 5.1.2.1.6.
- **5.12.3.4.14.6** *Override.* When a user might need to operate in out-of-tolerance conditions, a deliberate overriding action should be provided.
- **5.12.3.4.15.1** *Incomplete data.* The automated system shall provide a means to indicate to the user that data are incomplete, missing, unreliable, or invalid or that the system is relying on backup data. When the information is graphically displayed, color coding should be used to provide the user the status of the information.
- **5.12.3.4.15.2** *Accurate status.* Information presented to the user shall accurately reflect system and environment status in such manner that the user rapidly recognizes, easily understands, and easily projects system outcomes in relation to system and user goals.
- **5.12.3.4.15.3** *Information presentation.* Both the content of the information made available through automation and the ways in which it is presented should be consistent with the task priorities.
- **5.12.3.4.15.4** *Cueing important information.* When information must be updated quickly, the most important information should be cued to ensure it will be the first to be processed by the user.
- **5.12.3.4.15.5** *Highlight changed data.* Data changes that occur following automatic display update should be temporarily highlighted.
- **5.12.3.4.15.6** *Store and prioritize information.* Long lists of information and tasks should be stored and prioritized by the automated aid to minimize the number of decision alternatives and reduce the visual processing load of human users.
- **5.12.3.4.15.7** *Information history.* Provisions should be made to display historical information that allows the user to display and review changes to critical information.
- **5.12.3.4.16.3** *Determine decision aid use.* Users should be able to determine when and how the decision aid should be used.
- **5.12.3.4.16.4** *Terms and criteria.* Decision aids shall use terminology and criteria appropriate to the target user group.
- **5.12.3.4.16.6** *Assist user decisions.* Decision aids should assist, rather than replace, human decision makers by providing data for making judgments rather than commands that the user must execute.
- **5.12.3.4.16.8** *Task interruption.* Use of decision aids shall not require ongoing user tasks to be cancelled.
- **5.12.3.4.16.14** *Decision aid limit.* Decision aids should alert the user when a problem or situation is beyond the aid’s capability.
- **5.12.3.4.16.16** *Estimate uncertainty.* Decision aids should estimate and indicate the certainty of analysis and provide the rationale for the estimate.
- **5.12.3.4.16.17** *Decision data and information accessibility.* Procedural information, derived or processed information, and the data from which it is derived should be visible or accessible for decision verification.
- **5.12.3.4.16.18** *Decision logging.* The system shall log decision aid data including screen displays, procedural information, rules and facts, data employed, hypotheses tested, and summary information. This capability should be user configurable to log all, some, or none of the above data.
- **5.12.3.4.16.19** *Decision explanation detail.* When the system provides explanations to the user, it should supply a short explanation initially with the ability to make available more detail at the user’s request, including access to process information or an explanation for the rules, knowledge basis, and solutions used by the decision aid.
- **5.12.3.4.16.20** *Clear explanations to user.* When the system provides explanations to the user, the explanations shall use terms familiar to the user and maintain consistency with the immediate task.
- **5.12.3.4.16.22** *Alert to newly available information.* Decision aids shall alert the user to changes in the status of important system information, such as when critical information becomes available during decision aid utilization.


## 15. Software safety

Safety-critical user interface software criteria (5.7.19), applicable when the HMI can initiate hazardous functions or present safety-critical information.

- **5.7.19.2.1** *Cancel current process.* The software shall cancel current processing with a single-user action and have the system revert to a designed safe state.
- **5.7.19.2.2** *Reaction time.* Where operator reaction time is not sufficient to prevent a mishap, the software shall revert the system to a known safe state, report the failure, and report the system status to the operator.
- **5.7.19.3.1** *Two or more actions.* The system shall require two or more unique operator actions in order to initiate any potentially hazardous function or sequence of functions.
- **5.7.19.3.2** *Inadvertent actuation.* The actions required shall minimize the potential for inadvertent actuation and shall be checked for proper sequence. Refer to separation distances for specific control types in 5.1.3 and 5.1.4.
- **5.7.19.4** *Safety-critical displays.* Safety-critical operator displays, legends, and other interface functions should be clear, concise, and unambiguous and, where possible, be duplicated using separate display devices.
- **5.7.19.5.1** *Positive confirmation.* The software shall provide positive confirmation of valid data entry or actions taken (i.e., the system shall provide visual or aural feedback to the operator such that the operator knows that the system has accepted the action and is processing it).
- **5.7.19.5.2** *Real-time indication.* The system shall provide a real-time indication that it is functioning.
- **5.7.19.5.3** *Status indication.* Processing functions with delays of 2 seconds or longer shall require a status indicator to the operator during processing.
- **5.7.19.5.4.1** *Improper operator entries.* The software shall be capable of detecting improper operator entries or sequences of entries or operations (so as to prevent execution of safety-critical functions) (see 5.1.2.1.7).
- **5.7.19.5.4.2** *Alert the operator.* The software shall alert the operator to the erroneous entry or operation and indicate the error and corrective action.
- **5.7.19.6.1** *Distinguish from routine alerts.* Routine alerts shall be coded visually, audibly, or both in a different manner than safety-critical alerts.
- **5.7.19.6.2** *Clear a safety-critical alert.* The operator shall not be able to eliminate a safety-critical alert without taking corrective action or performing subsequent actions required to complete the ongoing operation.
- **5.7.19.6.3** *Unsafe situation alerts.* The system shall provide signals alerting the user to unsafe situations via the user interface.
- **5.7.19.7.1** *Computer control.* Safety-critical timing functions shall be controlled by the computer rather than relying on human input.
- **5.7.19.7.2** *Not modifiable by operator.* The system shall prevent the operator from modifying safety-critical timing values from system consoles, unless specifically required by the system design and in accordance with approval protocols.


## 16. Use on mobile and handheld devices (responsive web HMI)

Software/application-level requirements for PED applications that apply when the web HMI is accessed from a phone or tablet. Device-hardware requirements (casing, mounting, power, temperature, etc.) are excluded.

- **5.19.1.9.1** *Inadvertent activation.* PED accidental activation shall be avoided by defining unique user action sequences required for activation.
- **5.19.2.5.2** *Indication of connection status.* PEDs should provide an indication of the status of connection in terms of strength or reliability in both wireless and wired modes.
- **5.19.5.1** *General.* In addition to the requirements below, PED applications shall be designed in accordance with the applicable criteria contained in 5.1, 5.2, and 5.17.
- **5.19.5.1.1** *PED adaptation of existing design.* When a PED application is based on an existing design, it shall adapt but not replicate the existing design and be tailored for the size of the PED display screen.
- **5.19.5.1.2** *Design around PED capabilities and constraints.* PED applications should be designed around the PED capabilities and constraints.
  - a. Where users will work with the same application across multiple devices, consistency of appearance and interaction should be provided. Elements of PED interface such as labels, color schemes, and dialog appearances should be the same across multiple devices.
  - b. Applications should focus on the tasks performed by the PED users by following logical sequences and dialogs (see 5.19.5.4).
- **5.19.5.1.3** *Dynamically changing operational contexts.* PED application design shall support operations across dynamically changing operational contexts (e.g., brightness, noise, weather). For example, the design should allow users to do the following:
  - a. Configure output to their needs and preferences (e.g., text size, brightness).
  - b. Change key attributes like display brightness with minimal steps.
  - c. Provide options for the user to access functions or key attributes.
- **5.19.5.1.4** *Limited and divided attention.* PED design should accommodate limited and divided attention through the use of audible, visual, or tactile displays. PED use should not distract users from other tasks when the user has the following levels of attention:
  - a. Limited attention available when doing a task not on the PED.
  - b. Divided attention when doing PED and non-PED tasks.
  - c. Limited attention when doing a task on the PED and information for another task is received.
- **5.19.5.1.5** *Application optimization.* PED applications should be optimized for performance (e.g., time to load, time to play multimedia file, time to save, time to connect to data) as well as minimize power drain on the PED.
- **5.19.5.1.6.1** *Use of industry guidelines.* Industry guidelines for use and development of PED functionalities (e.g., camera, gyroscope) and platforms (e.g., iOS, Android, Windows) should be used rather than creating interactions and behaviors that differ from the default expected behavior.
- **5.19.5.1.6.2** *User expectations.* PED application design should enable users to apply existing knowledge and to quickly build a mental model of the application’s functional concept through familiar objects and consistency of design, reducing surplus mental load and additional concentration effort.
- **5.19.5.1.6.3** *Multiple applications.* PED applications should be designed to behave consistently to other applications on the PED with which users are familiar.
- **5.19.5.1.6.4** *Designing for multiple PEDs.* If designing for multiple PEDs, established design conventions for each PED should be followed and the appearance and functionality should be consistent across PEDs.
- **5.19.5.1.7** *Facilitation of intended action.* PED application functions shall support time-critical intended user actions.
- **5.19.5.1.8** *Textual information.* Where appropriate, the amount of textual information presented should be minimized to limit the amount of reading required by the user and be consistent with 5.17.18.
- **5.19.5.2** *Error control.* PED applications shall meet the requirements for error control contained in 5.1.2.1.7.
- **5.19.5.3** *Feedback.* PED application feedback to the user shall meet the requirements for feedback contained in 5.1.2.1.6.
- **5.19.5.4.1** *Critical information.* When designing PED displays, critical information should be reachable in no more than two key actions (e.g., scrolling and tapping).
- **5.19.5.4.2** *Command shortcuts.* Shortcuts, such as hot keys, voice activation, or keystroke combinations, should be provided for frequently used PED commands and interactions, such as repetitive tasks.
- **5.19.5.4.3** *Application control.* PED applications should be designed for speed and recovery. For example, applications should load quickly and be stopped, started, resumed, or changed with minimal effort.
- **5.19.5.4.4** *Auto-rotation of screen.* PED applications shall be designed to support user selectable auto-rotation of the screen to accommodate information presentation and efficient data entry.
- **5.19.5.4.5** *Simplicity of application design.* PED application design should be simple and uncluttered by using approaches such as:
  - a. Hierarchical nesting where the user can decide whether or not to retrieve details.
  - b. Word selection rather than text input.
  - c. Plain language rather than jargon, unless the application is intended for a specific user population.
- **5.19.5.4.6** *Language.* Language usage shall be consistent with 5.1.3.11.1.8.
- **5.19.5.4.7** *Simplicity of text input.* User input should be simple and based on operational context and need. The application should:
  - a. Minimize text input.
  - b. Provide clear and obvious indication of the type and format of input required for text fields.
  - c. Allow the user to choose by selecting an item instead of manually entering text.
  - d. Show prepopulated data in forms wherever possible.
- **5.19.5.4.8** PED application navigation.
  - a. Navigation structures should be simple and focus on one specific task at a time.
  - b. Navigation between fields in forms should be predictable.
- **5.19.5.4.9** *Dialog closure.* Dialogs, which are an interaction between the user and the PED that is finite, should yield closure by providing a clear beginning, middle, and end of the sequence of actions that make up the dialog.
- **5.19.5.5.1** *Minimize clutter.* The PED application should provide the user with essential data and functionality for immediate action. Unnecessary elements, clutter, or information on the screen for other actions should be minimized.
- **5.19.5.5.2** *Selection highlighting.* The item selected on a PED screen shall be clearly indicated to facilitate the user’s ability to identify that it has, in fact, been selected. Color, background, text change, or other visual mechanisms shall be used.
- **5.19.5.5.3** *Basic control availability.* PED software or hardware controls used to provide basic navigation (e.g., “Back to home”, “Back to search results”, “Back”, and other relevant commands) shall always be available to the user.
- **5.19.5.5.4** *Content density.* PED screen content density should be minimized. The following criteria should apply:
  - a. Content and interaction should not be split across so many screens that it leads to confusion and loss of awareness of where one is in the application.
  - b. Screens should be designed so that they can be scrolled only in one dimension.
  - c. PED display design should minimize the amount of text information presented in order to decrease the reading requirement, which is a difficult task in dismounted and moving conditions.
- **5.19.5.5.5.1** *Ability to skim text.* To increase ability to skim text, subtitles and hypertext shall be used in text passages that are longer than a single screen.
- **5.19.5.5.5.2** *Avoid variable line lengths.* Variable line lengths shall be avoided by hyphenating words at line breaks to improve readability on small screens.
- **5.19.5.5.5.3** *Scrolling markers.* Scrolling markers shall be provided to enable users to identify where they are on the page when content cannot be displayed in one screen.
- **5.19.5.5.6** *Screen readability.* A range of high contrast text color and typefaces should be selectable to allow for the PED user to optimize readability as dictated by the intended operational environment.
- **5.19.5.5.7** *Color usage.* The following criteria shall apply:
  - a. Color shall be used carefully and consistently throughout the design of PEDs and be consistent with 5.17.25.
  - b. Dual coding, such as shape or labeling, shall be used with color.


---

## Appendix A – Tables referenced by the requirements

**Table V – Acceptable system response times** (referenced by 5.1.2.1.6.4 and 5.17.9.5)

| System interpretation | Response time definition | Time (s) |
|---|---|---|
| Key response, including scroll wheels, optical wheels, mouse clicks | Key pressed until positive response (e.g., "click") | 0.1 |
| Key print | Key pressed until appearance of character | 0.2 |
| Page turn | End of request until first few lines are visible | 1.0 |
| Page scan | End of request until text begins to scroll | 0.5 |
| XY entry | From selection of field until visual verification | 0.2 |
| Pointing | From input of point to display point | 0.2 |
| Sketching | From input of point to display of line | 0.2 |
| Local update | Change to image using local database (e.g., new menu list from display buffer) | 0.5 |
| Host update | Change where data is at host in readily accessible form (e.g., a scale change of existing image) | 2.0 |
| File update | Image update requires an access to a host file | 10 |
| Inquiry (simple) | From command until display of a commonly used message | 2.0 |
| Inquiry (complex) | Response message requires seldom used calculations in graphic form | 10 |
| Error feedback | From entry of input until error message appears | 0.2 |

**Table XL – Common color association meanings** (referenced by 5.17.25.8)

| Color | Maps and tactical meaning | Classification meaning | Alarm, alert, threat meaning | Equipment meaning | Other common meaning |
|---|---|---|---|---|---|
| Red | Red alert<br>Forces or situation at critical condition<br>Hostile target identification | Secret | Critical consequences<br>Danger or unsafe<br>Severe threat<br>Emergency<br>Alarm | Closed/stopped<br>Oxygen<br>Malfunction<br>Ordnance handling | Stop<br>Heat or fire<br>Failure<br>OFF (as opposed to ON) |
| Orange |  | Top Secret | Alarm, alert, or hazard<br>High threat |  | Value between red and yellow |
| Yellow | Forces or situation at marginal condition<br>Unknown target affiliation<br>CBRNE areas |  | Warning, caution, or hazard<br>Elevated threat<br>Approaching critical<br>Extreme caution<br>Impending danger | Oil | Abnormal state<br>Delay<br>Check or recheck |
| Green | Non-alert<br>Neutral target affiliation<br>Obstacles<br>Forces or situation at acceptable condition | Unclassified | Normal<br>Safe<br>Low threat | Open/flowing | Maintenance personnel<br>ON (as opposed to OFF)<br>In tolerance, acceptable<br>Ready, proceed, satisfactory |
| Blue | Friendly affiliation<br>Deep water |  | Safe<br>Guarded threat | Non-critical items<br>Water or flooding<br>Nitrogen | Cool or cold |
| Cyan | Friendly affiliation |  | Advisory | Aerated water | Cool |
| Dark (navy) blue |  |  | Advisory | Untreated water |  |
| Magenta |  |  | Alarm state<br>Radiation hazard |  |  |
| Purple (violet) |  |  |  | Aviation fuels |  |
| White |  |  | Advisory | Steam | Medical personnel<br>Empty<br>Functional or physical position<br>Action in progress |
| Black | Political boundary<br>Image or figure edge |  |  |  | Outline or border |
| Gray |  |  |  | Smoke | Inactive or unavailable options or actions |
