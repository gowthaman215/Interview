= Manual Code Analysis Guide for Qt/C++ Projects
:author: Developer Guide
:revdate: 2026-04-16
:toc: left
:toclevels: 3
:icons: font
:source-highlighter: rouge
:sectnums:

== Introduction

This guide documents a systematic, step-by-step approach to analyzing and debugging code issues in large Qt/C++ codebases — the same methodology used by AI-assisted code analysis tools like GitHub Copilot.

The techniques here can be applied **manually** using only `grep`, log analysis, and code reading.

A real-world example is used throughout: *the POI Search duplicate `SetFocus` bug in the NavUI application*.

---

== Step 1: Understand the Symptom

=== Goal
Read the logs or bug report and extract the **expected vs actual behavior**.

=== Technique
- Highlight anomalous lines in the log
- Create a timeline table on paper or in a text editor
- Identify what is **normal** and what is **not**

=== Example

.Timeline of anomalous Click #7
[cols="1,3,1", options="header"]
|===
| Time | Event | Normal?

| 13:28:23.684684
| `SetFocus(inputField) → true, hasfocus=true`
| ❌ Should be `hasfocus=false` on fresh screen

| 13:28:23.684765
| `SetFocus(inputField) → true, hasfocus=true`
| ❌ Duplicate call 81μs later

| 13:28:23.858282
| `SetFocus(inputField) → false, hasfocus=true`
| ❌ Focus removed unexpectedly

| 13:28:24.052621
| `SetFocus(inputField) → false, hasfocus=true`
| ❌ Redundant removal

| 13:28:24.099709
| `SetFocus(inputField) → true, hasfocus=false`
| ⚠️ Focus restored again
|===

=== Key Questions to Ask
* What is the **expected** sequence of events?
* What is the **actual** sequence?
* What **differs** between the two?
* Are there **timestamps** that reveal timing issues?

---

== Step 2: Identify the Entry Point

=== Goal
Find where the triggering event is **defined** and **handled** in code.

=== Technique
Search for the event ID, enum name, or log string in header and source files.

=== Commands
[source,bash]
----
# Search for the event ID value
grep -rn "1026" --include="*.h" --include="*.cpp"

# Search for the event name
grep -rn "POI_SEARCH" --include="*.h" --include="*.cpp"

# Search for the log message text
grep -rn "Button event" --include="*.cpp"
----

=== Example
Found the enum definition:
[source,cpp]
----
enum THomeModelDataID
{
    MAIN_MENU_CLICK = VIEW_HOME_RANGE_START_ID,  // = 1024
    SEARCH_SHORTCUT_CLICK,                       // = 1025
    EV_POI_SEARCH_CLICK,                         // = 1026
    ...
};
----

---

== Step 3: Trace the Call Chain (Forward)

=== Goal
From the entry point, follow **every function call** step by step until you reach the code that produces the symptom (the log line).

=== Technique
For each function encountered, search for its implementation and note what it calls next.

=== Commands
[source,bash]
----
# Find the implementation of a function
grep -rn "FunctionName" --include="*.cpp"

# Find where a function is declared
grep -rn "FunctionName" --include="*.h"
----

=== Example: Full Call Chain for Event 1026

[source,text]
----
Button Click (1026)
  → CHomeView::OnBtnEvPOISearchClicked()
    → iModel->OnButtonClick(EV_POI_SEARCH_CLICK)
      → CHomeScreen handles the event
        → OnEvPOISearchButtonClick()
          → CreateAction(ECreateSearchScreenAction)
            → CSearchScreen::SetScreenForeground(true)
              → CBaseSearchScreen::SetScreenForeground(true)
                → iSearchController->Resume()
                  → CFullSearchController::SetInputFocus()
                    → CBaseSearchController::SetSearchStringFocus()
                      → OnButtonClick(INPUT_FIELD_CLICK)
                        → CSearchView::FocusInputFieldAndShowSoftInput()
                          → iUi->modifierInputField->SetFocus(false)
                          → iUi->inputField->SetFocus(true)  ← THE LOG LINE
----

TIP: Draw this chain on paper as a tree structure. It helps visualize the flow.

---

== Step 4: Trace Backward from the Symptom

=== Goal
From the buggy log line, find **all callers** of that function to identify every possible code path that could trigger it.

=== Technique
Search for all call sites of the function that produces the symptom.

=== Commands
[source,bash]
----
# Find all callers of SetFocus
grep -rn "SetFocus" --include="*.cpp" | grep -v "\/\/"

# Find all callers of FocusInputFieldAndShowSoftInput
grep -rn "FocusInputFieldAndShowSoftInput" --include="*.cpp"
----

=== Example
Multiple code paths found calling `SetFocus` on input fields:

. Direct path: `FocusInputFieldAndShowSoftInput()` → normal screen transition
. `AppFocusChanged()` → external app focus change
. `mousePressEvent()` → user taps the input field
. `EFocusStart` custom event handler → focus mode changes
. `FocusOnList()` signal via `Qt::QueuedConnection` → deferred focus

Each of these is a **potential source** of the duplicate call.

---

== Step 5: Identify Async/Deferred Mechanisms

=== Goal
Find anything that can **delay** execution and cause race conditions: timers, queued connections, signals, event loops.

=== Why This Matters
In Qt, asynchronous mechanisms are the **#1 source of race conditions**:

* `Qt::QueuedConnection` — defers slot execution to the next event loop iteration
* `QTimer::singleShot` — fires after a delay
* `emit` with queued connections — signal delivery is deferred

If these operations survive a screen transition, they can fire on the **next** screen entry, causing stale/duplicate behavior.

=== Commands
[source,bash]
----
# Find queued connections
grep -rn "QueuedConnection" --include="*.cpp"

# Find timers
grep -rn "QTimer" --include="*.cpp" --include="*.h"

# Find single-shot timers
grep -rn "singleShot" --include="*.cpp"

# Find signal emissions related to focus
grep -rn "emit.*Focus" --include="*.cpp"

# Find all signal/slot connections in a specific file
grep -n "connect(" SearchView.cpp
----

=== Example: Async Mechanisms Found

.Deferred mechanisms that could cause stale focus
[cols="2,2,1", options="header"]
|===
| Mechanism | Location | Type

| `FocusOnList()` signal
| `CFocusHandler`
| `Qt::QueuedConnection`

| Focus clear timer (5000ms)
| `CFocusHandler`
| `QTimer` single-shot

| Keyboard visibility timer (300ms)
| `CTextInputView`
| `QTimer` single-shot

| Mouse-press → `SetFocus`
| `CInputField::mousePressEvent`
| Direct call

| 7× `Qt::QueuedConnection` signals
| `CSearchView` constructor
| Deferred signal delivery
|===

---

== Step 6: Compare Normal vs Abnormal Cases

=== Goal
Use the logs to identify **what differs** between working and broken cases.

=== Technique
Create a comparison table between a normal instance and the broken instance.

=== Example

.Comparison of normal Click #1 vs broken Click #7
[cols="2,1,1", options="header"]
|===
| Aspect | Normal (Click #1) | Broken (Click #7)

| `modifierInputField → false`
| ✅ Present
| ❌ Missing

| `inputField → true, hasfocus=false`
| ✅ Correct
| ❌ `hasfocus=true`

| Duplicate SetFocus calls
| ❌ None
| ✅ 81μs apart

| Prior incomplete clicks
| None
| 3 clicks with no SetFocus logged

| Time between click and SetFocus
| ~6 seconds
| ~1.4 seconds
|===

=== Key Insight
The `hasfocus=true` on the first `SetFocus` call means focus was **already set** before the normal code path ran. This indicates **stale state from prior incomplete transitions**.

---

== Step 7: Form a Hypothesis

=== Goal
Based on all evidence gathered in Steps 1–6, propose a **root cause**.

=== Technique
Connect the dots:

. **What changed?** — Rapid clicks #3, #4, #5 with no cleanup
. **What was left behind?** — Queued focus signals and stale focus state
. **What triggered the bug?** — Click #7 arrived while stale state existed
. **What was the result?** — Both a stale queued signal AND the new direct call fired

=== Example Hypothesis

[quote]
Rapid clicks (#3–#5) opened SearchScreen but the user navigated back before queued focus signals were consumed. These signals remained in the event queue. On click #7, both the new direct focus call AND a stale queued signal fired within 81μs, causing duplicate `SetFocus(true)` and subsequent focus oscillation.

---

== Step 8: Verify the Hypothesis

=== Goal
Check if the code **supports** your theory. Look for missing cleanup, missing guards, and unhandled edge cases.

=== Verification Checklist

[cols="3,1,2", options="header"]
|===
| Check | Result | Implication

| Does `SCREEN_FOREGROUND(false)` clear focus state?
| ❌ No
| Stale focus survives screen exit

| Does `hideEvent()` reset input field focus?
| ❌ No (only slides keyboard down)
| Focus state persists

| Do `Qt::QueuedConnection` signals survive screen transitions?
| ✅ Yes
| Queued signals can fire on next entry

| Does `Pause()` clear focus state?
| ❌ No
| Controller doesn't clean up focus

| Does `SetFocus()` guard against redundant calls?
| ❌ No
| Duplicate calls are processed
|===

All checks support the hypothesis. ✅

---

== Step 9: Design the Fix

=== Principles
* **Cancel pending operations** when leaving the screen
* **Guard against redundant calls** (idempotency)
* **Minimal change** — don't restructure, just add cleanup
* **No side effects** — ensure the fix is a no-op in normal cases

=== Example Fix

Add focus cleanup in the `SCREEN_FOREGROUND` handler when going to background:

[source,cpp]
----
case SCREEN_FOREGROUND:
{
    if (iModel->GetBoolean(aKey))
    {
        connect(iAppFocusHandler.get(),
                &NNavPort::IAppFocusHandler::FocusChanged,
                this, &CSearchView::AppFocusChanged);
    }
    else
    {
        disconnect(iAppFocusHandler.get(),
                   &NNavPort::IAppFocusHandler::FocusChanged,
                   this, &CSearchView::AppFocusChanged);

        // Cancel any pending focus operations to prevent stale
        // queued signals from firing on the next screen entry
        UnFocusInputfield(iUi->inputField);
        UnFocusInputfield(iUi->modifierInputField);
    }
    break;
}
----

---

== Step 10: Validate the Fix

=== Goal
Ensure the fix doesn't break other scenarios.

=== Validation Checklist

[cols="3,1", options="header"]
|===
| Question | Answer

| Does this change affect the normal flow?
| No — `UnFocusInputfield` checks `InFocus()` before acting

| Could it cause regressions?
| Only if something depends on focus persisting across screen transitions (unlikely)

| Does it handle edge cases?
| Yes — it's a no-op if focus is already cleared

| Is it minimal?
| Yes — 2 lines added in an existing handler
|===

---

== Appendix A: Essential grep Commands for Qt/C++ Debugging

[source,bash]
----
# Find enum/constant definition
grep -rn "ENUM_VALUE" --include="*.h"

# Find all callers of a function
grep -rn "FunctionName(" --include="*.cpp"

# Find function implementation
grep -rn "ClassName::FunctionName" --include="*.cpp"

# Find signal/slot connections
grep -rn "connect(" --include="*.cpp" | grep "SignalName"

# Find async mechanisms (race condition sources)
grep -rn "QueuedConnection\|QTimer\|singleShot" --include="*.cpp"

# Find signal emissions
grep -rn "emit" --include="*.cpp" | grep "Focus"

# Find class hierarchy
grep -rn "class CMyClass" --include="*.h"

# Find virtual overrides
grep -rn "override" --include="*.h" | grep "MethodName"

# Find all files in a module
find Kits/ViewKit -name "*.cpp" -o -name "*.h"

# Count occurrences (useful for impact assessment)
grep -rc "FunctionName" --include="*.cpp" | grep -v ":0$"
----

== Appendix B: Common Qt Race Condition Patterns

=== Pattern 1: Queued Connection Outlives Context
[source,cpp]
----
// PROBLEM: Queued signal fires after screen is hidden
connect(sender, SIGNAL(sig()), receiver, SLOT(slot()),
        Qt::QueuedConnection);

// FIX: Disconnect in cleanup/hide handler
disconnect(sender, SIGNAL(sig()), receiver, SLOT(slot()));
----

=== Pattern 2: Timer Fires After Context Change
[source,cpp]
----
// PROBLEM: Timer fires after screen transition
QTimer::singleShot(300, this, &MyClass::DelayedAction);

// FIX: Use a member timer and stop it in cleanup
iTimer->stop();  // In hideEvent or OnScreenBackground
----

=== Pattern 3: Missing Idempotency Guard
[source,cpp]
----
// PROBLEM: SetFocus called redundantly
void SetFocus(bool aFocus)
{
    iModel->SetBoolean(IN_FOCUS, aFocus);  // Always sets
    iUi->textEdit->SetFocus(aFocus);       // Always calls
}

// FIX: Guard against redundant calls
void SetFocus(bool aFocus)
{
    if (aFocus == iUi->textEdit->hasFocus()) return;  // Early return
    iModel->SetBoolean(IN_FOCUS, aFocus);
    iUi->textEdit->SetFocus(aFocus);
}
----

=== Pattern 4: Signal Connected Multiple Times
[source,cpp]
----
// PROBLEM: connect() called without prior disconnect()
connect(sender, &Sender::sig, receiver, &Receiver::slot);
// If called again, the slot fires TWICE per signal emission

// FIX: Disconnect before connecting, or use Qt::UniqueConnection
connect(sender, &Sender::sig, receiver, &Receiver::slot,
        Qt::UniqueConnection);
----

== Appendix C: Debugging Workflow Diagram

[source,text]
----
                    ┌─────────────────────┐
                    │ 1. Read Logs/Bug    │
                    │    Identify Symptom  │
                    └─────────┬───────────┘
                              │
                    ┌─────────▼───────────┐
                    │ 2. Find Entry Point │
                    │    (grep for ID)    │
                    └─────────┬───────────┘
                              │
              ┌───────────────┼───────────────┐
              │                               │
    ┌─────────▼───────────┐         ┌─────────▼───────────┐
    │ 3. Trace Forward    │         │ 4. Trace Backward   │
    │    (Entry → Symptom)│         │    (Symptom → Callers│
    └─────────┬───────────┘         └─────────┬───────────┘
              │                               │
              └───────────────┬───────────────┘
                              │
                    ┌─────────▼───────────┐
                    │ 5. Find Async/      │
                    │    Deferred Paths   │
                    └─────────┬───────────┘
                              │
                    ┌─────────▼───────────┐
                    │ 6. Compare Normal   │
                    │    vs Abnormal      │
                    └─────────┬───────────┘
                              │
                    ┌─────────▼───────────┐
                    │ 7. Form Hypothesis  │
                    └─────────┬───────────┘
                              │
                    ┌─────────▼───────────┐
                    │ 8. Verify in Code   │
                    └─────────┬───────────┘
                              │
                    ┌─────────▼───────────┐
                    │ 9. Design Fix       │
                    └─────────┬───────────┘
                              │
                    ┌─────────▼───────────┐
                    │ 10. Validate Fix    │
                    └─────────────────────┘
----

---

_Document generated: 2026-04-16_
