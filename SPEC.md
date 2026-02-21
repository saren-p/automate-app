# Automate App — Product & Engineering Specification

This document defines the complete feature set, behavior, and UX contract for the Automate Android app.

This app is:
- Native Android (Kotlin + Jetpack Compose)
- A real automation engine
- Not a web wrapper
- Not a UI prototype

All features below are mandatory unless explicitly restricted by Android platform policy.

------------------------------------------------------------
# 1. PRODUCT OVERVIEW

Automate is a rule-based Android automation app.

Users can create rules using:

Trigger → (Optional Conditions) → Actions

When a trigger fires and all conditions are satisfied,
the defined actions execute in order.

The app must be deterministic, professional, and transparent.
No silent failures. No fake capabilities.

------------------------------------------------------------
# 2. RULE MODEL

Each Rule contains:

- id
- name
- enabled (true/false)
- trigger
- conditions (0+)
- actions (1+)
- createdAt
- updatedAt

Rules can be:
- Enabled
- Disabled
- “Needs Setup” (missing permissions)

------------------------------------------------------------
# 3. TRIGGERS (8 TOTAL — ALL REQUIRED)

1. Notification Received
   - Filter by:
     - App package
     - Title contains text
     - Body contains text

2. Bluetooth State
   - Device selected
   - State: Connected / Disconnected

3. Wi-Fi State
   - SSID selected
   - State: Connected / Disconnected

4. App Activity
   - App selected
   - Event: Opened / Closed (foreground)

5. Location
   - Named location (geofence)
   - Event: Enter / Exit

6. Time Schedule
   - Specific time OR time window
   - Must use exact AlarmManager where required

7. Battery Level
   - Threshold (e.g., below 30%)

8. Charging State
   - Charging / Not Charging

------------------------------------------------------------
# 4. CONDITIONS (5 TOTAL — ALL REQUIRED)

1. Battery Level > X%
2. Time Window (between start and end)
3. Wi-Fi Connected (SSID)
4. Driving Mode
   - Based on Activity Recognition or heuristic
5. Screen Locked / Unlocked

Conditions must:
- Be AND-based
- Be evaluated before actions execute

------------------------------------------------------------
# 5. ACTIONS (15 TOTAL — ALL REQUIRED)

1. Set Wi-Fi
2. Set Bluetooth
3. Set VPN
4. Set Airplane Mode
5. Set Do Not Disturb
6. Set Ringer Mode
7. Set Volume
8. Set Brightness
9. Set Flashlight
10. Vibrate
11. Speak Text (TextToSpeech)
12. Set Auto-Rotate
13. Show Notification
14. Launch App
15. Delay

Actions execute sequentially in order defined.

------------------------------------------------------------
# 6. ANDROID PLATFORM RESTRICTIONS (MANDATORY HONESTY)

The app must NEVER fake capability.

Airplane Mode:
- Direct toggle restricted
- Must show “Restricted by Android”
- Provide Settings fallback

Wi-Fi toggle:
- May be restricted
- Use Settings panel fallback where required

VPN:
- Do NOT assume third-party VPN profile access
- Support:
  - Discoverable system VPN profiles
  - Launching selected VPN app
- Never fabricate VPN profile control

Brightness / Auto-Rotate:
- Requires WRITE_SETTINGS
- If not granted → open correct Settings screen

DND:
- Requires Notification Policy access

App Activity:
- Requires Usage Access

Notification trigger:
- Requires NotificationListenerService

All restricted attempts must:
- Log reason
- Show user guidance

------------------------------------------------------------
# 7. PERMISSIONS SYSTEM (MANDATORY)

Implement:

PermissionRegistry
PermissionChecker
PermissionNavigator

Each Trigger/Condition/Action must declare:
- Runtime permissions
- Special access requirements
- Restricted flags

Rules with missing requirements:
- May be saved
- Must show “Needs Setup”
- Must not execute until resolved

------------------------------------------------------------
# 8. LOGGING SYSTEM (MANDATORY)

Each rule execution must log:

- Timestamp
- Rule name
- Outcome (Success / Blocked / Failed)
- Reason
- Suggested user action (if applicable)

Logs must be human-readable.

------------------------------------------------------------
# 9. USER INTERFACE SPECIFICATION

The UI must match the following design language.

------------------------------------------------------------
## DESIGN PRINCIPLES

- Professional automation tool
- Deterministic
- Calm
- Trustworthy
- Comparable to Tasker / MacroDroid
- Not playful
- Not flashy
- Not default Material demo look

------------------------------------------------------------
## COLOR SYSTEM

Primary Accent:
- Indigo / blue-violet

Success:
- Emerald green

Warning / Restricted:
- Amber

Error:
- Red

Background:
- Very light gray / off-white

Card surfaces:
- White

Text:
- Primary: dark slate (not pure black)
- Secondary: muted gray
- Disabled: light gray

Borders:
- Subtle light gray

Shadows:
- Minimal elevation

------------------------------------------------------------
## TYPOGRAPHY

System font.
Hierarchy:

- Screen title: Large, semibold
- Section header: Medium, semibold
- Body text: Regular
- Helper text: Small, muted

------------------------------------------------------------
## LAYOUT RULES

- Stable app shell
- ONE scroll container per screen
- Do NOT make entire app scroll by default

Wizard layout:
- Fixed header
- Scrollable content
- Fixed footer (Back / Next / Save)

Buttons must never be hidden behind system navigation.

------------------------------------------------------------
## WIZARD UX

- Step-based progression
- Visible progress indicator
- “Next” always visible when applicable

If Next is disabled:
- Show inline helper:
  - “Select a Wi-Fi network to continue”
  - “Select an app to continue”

Never silently block user.

------------------------------------------------------------
## INPUT RULES

- Time → time picker
- Toggles → switches or segmented controls
- Enumerations → dropdowns or chips
- No unnecessary freeform typing

------------------------------------------------------------
## PERMISSIONS HUB SCREEN

Must show:

- Permission name
- Status (Granted / Required / Restricted)
- One-line WHY
- One-line BENEFIT
- Enable button

------------------------------------------------------------
# 10. ENGINE REQUIREMENTS

Architecture must include:

- RuleRunner
- TriggerManager
- ConditionEvaluator
- ActionExecutor
- Alarm scheduling
- Boot rescheduling
- Foreground service where required
- Room persistence

------------------------------------------------------------
# 11. MVP PHASING

Phase 1 must implement end-to-end:

- Time Trigger
- Show Notification action
- Delay action
- Logging

Then incrementally add:

- Notification trigger
- Battery trigger
- App Activity
- DND
- etc.

------------------------------------------------------------
# 12. NON-NEGOTIABLES

- No feature removal
- No fake system control
- No silent permission requests
- No UI regressions
- No giant single PR

------------------------------------------------------------
# END OF SPEC
