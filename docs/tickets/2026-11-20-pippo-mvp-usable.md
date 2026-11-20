---
title: Pippo MVP usable
date: 2026-11-20
status: draft
planner: none
project: ""
hierarchy: epic
---

# Pippo MVP usable

## Summary

Deliver the Must capabilities planned for the usable MVP: local system/device controls, durable clipboard and notes, remote AI, reminders, keep-awake, recovery, notifications, and the first agent handoff. The MVP remains local-first and explains unavailable integrations instead of claiming they work.

## Decisions

- PRD 0.6.18 is the approved product baseline; ROADMAP 1.2 is approved.
- One epic file per roadmap phase — user.
- Group stories by P0/MVP/v1 phase — (agent's call).
- Text-only ticket drafts — user.
- C-12/P2 integrations stay out of this epic — PRD.
- Clipboard pin/retention/quota behavior follows DECIDED OQ-36.
- Codex CLI is the first adapter; Pippo never sends Approve/Reject — OQ-30/OQ-31 DECIDED.

## Epic

### Problem

The approved product scope calls for everyday tasks to work from Pippo, but no application behavior exists yet. Data loss, secret exposure, hidden network requests, or unsupported controls would make a visually complete shell unsafe to rely on.

### Goal

Provide a usable MVP on KDE Plasma Wayland and Hyprland, with durable local workflows, explicit remote AI submission, recoverable services, and clear capability/error states.

### Users

- Linux developer / power user — manage clipboard, notes, media, and agent events while staying in context.
- Laptop / media user — inspect battery and control available devices or playback.
- Reminder user — receive predictable reminders and explicitly control idle inhibition.

### Success Metrics

- C-2 through C-7 MVP workflows pass their FEATURES acceptance criteria on both target desktops or report unsupported capability with a reason.
- Local data survives restart without silent loss; secrets and user content follow Q-5 privacy rules.
- Repeatable reminders resume with at most one missed-occurrence summary and no duplicate replay.

### Scope

- M1.1 persistence, migration, keyring, preferences, single-instance recovery, and Pippo-owned notifications.
- M1.2 battery, supported controls, and active-stream audio mixer.
- M1.3 local clipboard history and quick notes.
- M1.4 remote BYOK AI.
- M1.5 repeatable reminders and Caffeine.
- M1.6 first-party notification path and Codex CLI completion/approval-request handoff.

### Out of Scope

- Prayer schedule, public holidays, external notification adapters, FX, Microsoft meeting/call adapters, release packaging, and P2 features.
- Local AI inference, automatic clipboard-to-AI submission, direct agent approval, auto-answer, or silent privilege escalation.

### Anti-goals

- Silently deleting pinned clipboard data, sending private context to AI without submit, or storing credentials outside keyring.
- Claiming a desktop capability works when its backend is absent.
- Replaying a backlog of reminders or approval events after resume.

### Stories

| # | Story / task | Type | Depends on |
| --- | --- | --- | --- |
| MVP-1 | Persist preferences and user data safely | Task | P0 gate |
| MVP-2 | See battery state without false readings | Story | MVP-1 |
| MVP-3 | Control available laptop and network devices | Story | MVP-1, P0 integration spikes |
| MVP-4 | Control active playback streams independently | Story | MVP-1, P0-8 |
| MVP-5 | Restore clipboard items without silent data loss | Story | MVP-1, P0-7 |
| MVP-6 | Keep quick notes across restarts | Story | MVP-1 |
| MVP-7 | Use remote AI only after explicit submission | Story | MVP-1 |
| MVP-8 | Create and resume repeatable reminders safely | Story | MVP-1, P0-10 |
| MVP-9 | Keep the display awake only when requested | Story | MVP-1, P0-10 |
| MVP-10 | Receive Pippo notifications and recover from failures | Story | MVP-1 |
| MVP-11 | Review agent events in Codex CLI | Story | MVP-1, P0-14 |

### Constraints

- Local persistence uses SQLite for durable data and QSettings for preferences; credentials use OS keyring.
- Remote AI profiles include OpenAI, Anthropic, and compatible endpoints; no local model requirement.
- Clipboard defaults: 500 items, 30 days, 250 MiB total, 20 MiB per item; pinned items count toward quota.
- Reminder minimum interval is one minute; initial interval field is blank and required.
- Both KDE Plasma Wayland and Hyprland are target environments; each must be reported separately.

### Risks & Dependencies

- M1.1 follows the P0 gate; M1.2/M1.3 depend on relevant desktop capability spikes.
- Keyring, DND, global shortcuts, notification observation, and idle inhibition can be unavailable; show a clear fallback.
- Agent event transport/version remains a P0 verification dependency; no event API is assumed.

### Decisions

- User data, secrets, and integration consent follow PRD Q-5 and the approved FR contracts — user-approved PRD.
- Group stories by roadmap milestone — (agent's call).

### Open Questions

- None blocking this epic after P0 capability and protocol gates. Backend-specific unavailable states remain valid outcomes.

---

## MVP-1 — Persist preferences and user data safely

**Type:** Task · **Context:** [PRD](../PRD.md) C-2/C-5/Q-5; FEATURES FR-06/10/11; ROADMAP M1.1; ARCHITECTURE storage boundaries.

### Acceptance Criteria

- Given a first launch, when preferences and notes are saved and Pippo restarts, then preferences and durable records are restored without overwriting newer data.
- Given a schema upgrade, when the migration succeeds or fails, then the previous durable data remains recoverable and migration outcome is recorded.
- Given the OS keyring is unavailable, when a provider credential is configured, then Pippo refuses plaintext fallback and explains the limitation.
- Given two Pippo launches race, when the second instance starts, then it focuses or signals the first instance rather than starting duplicate timers and notifications.
- Given an internal worker/provider exception occurs, when recovery is applied, then the core UI remains usable and the failure is surfaced without logging secrets.

### Out of Scope

- Cloud sync, encryption-at-rest requirement, or self-updating with elevated privileges.

### Dependencies

- Blocked by: P0 gate.
- Relates to: FR-10/11, Q-5, CODEMAP.

**Labels:** pippo, mvp, persistence, recovery · **Priority (suggested):** High · **Target:** M1.1

## MVP-2 — See battery state without false readings

**As a** laptop user, **I want** accurate battery and charging state, **so that** I can tell whether the device needs power.

**Context:** [PRD](../PRD.md) C-1; FEATURES FR-02; ROADMAP M1.2.

### Acceptance Criteria

- Given UPower reports a battery state change, when Pippo receives the signal, then the displayed state updates within three seconds.
- Given a desktop has no battery or UPower is unavailable, when the widget opens, then it shows absent/unavailable and never displays a fabricated 0% battery.
- Given multiple batteries exist, when their data is valid, then the aggregate is displayed; otherwise each unsupported/unknown state is reported without a misleading aggregate.
- Given optional low-battery alerts are disabled, when charge crosses the alert threshold, then no alert is sent.

### Out of Scope

- Battery health estimation or changing power profiles.

### Dependencies

- Blocked by: MVP-1.
- Relates to: FR-02.

**Labels:** pippo, mvp, battery · **Priority (suggested):** Medium · **Target:** M1.2

## MVP-3 — Control available laptop and network devices

**As a** Linux laptop user, **I want** to inspect and change supported Bluetooth, Wi-Fi, and system controls, **so that** quick settings do not require another panel.

**Context:** [PRD](../PRD.md) C-3; FEATURES FR-05; ROADMAP M1.2. Brightness remains M2.1.

### Acceptance Criteria

- Given BlueZ or NetworkManager exposes a capability, when the user changes its state, then Pippo reads back and displays the actual OS state.
- Given a permission is denied, a service is absent, or a display lacks backlight control, when the user opens that control, then it shows Unavailable with a short reason and no fake slider.
- Given state changes outside Pippo, when the OS reports the change, then the displayed control reflects it.
- Given a user operates controls by keyboard, when focus is on a slider or toggle, then the control can be changed without a mouse.
- Given a control requires elevated privileges, when Pippo encounters that boundary, then it does not invoke sudo or modify permissions silently.

### Out of Scope

- Internal-display brightness (M2.1), external-monitor brightness, or changing system permissions.

### Dependencies

- Blocked by: MVP-1 and applicable M0.4 service probes.
- Relates to: FR-05, Q-5/Q-6.

**Labels:** pippo, mvp, controls · **Priority (suggested):** Medium · **Target:** M1.2

## MVP-4 — Control active playback streams independently

**As a** Linux desktop user, **I want** to change volume, mute, and output for an active app stream, **so that** one app's audio change does not affect another.

**Context:** [PRD](../PRD.md) C-3; FEATURES FR-14; ROADMAP M1.2; OQ-25/OQ-26.

### Acceptance Criteria

- Given two active playback streams, when the user changes volume, mute, or output for stream A, then stream B remains unchanged.
- Given a stream or device is unplugged or disappears, when the graph changes, then Pippo removes or updates the affected row without freezing or crashing.
- Given there is no active stream for an app, when the mixer renders, then it does not show a fake per-app slider.
- Given no shortcut is configured, when Pippo starts, then no global binding is claimed; after the user sets a shortcut, conflicts are reported without replacing another binding.
- Given a desktop cannot provide a shortcut or stream route, when the feature is opened, then the unavailable capability and fallback are explicit.

### Business Rules

- V1 supports active playback volume/mute/output; no per-app microphone capture or persistent custom routing.
- Shortcut starts empty; stream volume defaults to 0–100%; amplification above 100% requires explicit opt-in and warning.

### Out of Scope

- Audio recording, per-app microphone controls, or guaranteed browser-tab separation.

### Dependencies

- Blocked by: MVP-1 and P0-8.
- Relates to: FR-14, OQ-25/OQ-26.

**Labels:** pippo, mvp, audio · **Priority (suggested):** High · **Target:** M1.2

## MVP-5 — Restore clipboard items without silent data loss

**As a** Linux desktop user, **I want** to search, pin, and restore local clipboard items, **so that** I can recover copied content after switching tasks or restarting Pippo.

**Context:** [PRD](../PRD.md) C-2/Q-5; FEATURES FR-04; ROADMAP M1.3; OQ-36 DECIDED.

### Acceptance Criteria

- Given text, PNG, or JPEG is captured on a supported backend, when the user searches or previews history, then the matching item and image thumbnail are shown without sending content to a network service.
- Given Pippo restarts before an item expires, when history is reopened, then the item remains available and can be restored to the system clipboard.
- Given an item is pinned and older than 30 days, when retention cleanup runs, then it is retained but still counted toward item and byte quota.
- Given a new item would exceed a quota, when existing unpinned items can be evicted, then oldest eligible items are removed until the new item fits; if none can be evicted, the new item is rejected with visible feedback.
- Given recording is paused, cleared, or incognito is enabled, when clipboard changes occur, then Pippo does not record the new content.
- Given the backend is unsupported or permission is absent, when capture/restore is attempted, then the user sees an honest unavailable/error state and no silent auto-paste occurs.

### Business Rules

- Defaults: 500 items, 30 days, 250 MiB combined, 20 MiB per item. Pinned items count toward quota; after unpin, expired items become eligible for deletion.
- Auto-paste remains disabled unless a safe backend supports it and the user opts in.

### Out of Scope

- GIF/SVG history, cloud sync, auto-sharing with AI, or guaranteeing all sensitive data can be detected.

### Dependencies

- Blocked by: MVP-1 and P0-7.
- Relates to: FR-04, OQ-36, Q-5.

**Labels:** pippo, mvp, clipboard, privacy · **Priority (suggested):** High · **Target:** M1.3

## MVP-6 — Keep quick notes across restarts

**As a** Linux desktop user, **I want** to create, search, edit, pin, and delete local notes, **so that** short reminders stay available without another app.

**Context:** [PRD](../PRD.md) C-2/Q-5; FEATURES FR-06; ROADMAP M1.3.

### Acceptance Criteria

- Given a note is edited, when the debounce save completes and Pippo restarts, then the latest saved text is restored.
- Given a save fails, when the error is surfaced, then the last known good note remains recoverable and is not replaced by an empty value.
- Given note text is 10,000 characters, when the user types and searches, then input remains responsive and search does not block typing.
- Given note content is present, when AI is opened without an explicit submit containing that note, then no note content is sent.
- Given the user deletes a note, when deletion is confirmed, then that note is removed and unrelated notes remain.

### Out of Scope

- Network sync, rich-text editor, or attachments.

### Dependencies

- Blocked by: MVP-1.
- Relates to: FR-06, Q-5.

**Labels:** pippo, mvp, notes · **Priority (suggested):** High · **Target:** M1.3

## MVP-7 — Use remote AI only after explicit submission

**As a** Linux desktop user, **I want** to choose a remote AI provider/model and submit a prompt deliberately, **so that** I control when data leaves the device.

**Context:** [PRD](../PRD.md) C-4/Q-5; FEATURES FR-08; ROADMAP M1.4.

### Acceptance Criteria

- Given OpenAI, Anthropic, or a compatible profile is configured with a valid key, when the user submits a prompt, then the selected provider receives only the submitted content.
- Given the user has not submitted, when the prompt contains clipboard or note text, then no provider request is sent.
- Given keyring storage is unavailable, when a key is entered, then Pippo refuses plaintext storage and explains how to proceed.
- Given the provider returns 401, 429, timeout, or TLS failure, when the request ends, then an actionable error is shown, no hidden billable retry occurs, and other widgets remain usable.
- Given a streaming response is in progress, when the user selects Stop, then the request is cancelled and no later chunks are appended.

### Business Rules

- MVP uses remote inference; local inference is P2. Credentials use OS keyring only.

### Out of Scope

- Automatic prompt submission, local model execution, provider login via browser cookies, or fallback to another provider without user action.

### Dependencies

- Blocked by: MVP-1.
- Relates to: FR-08, Q-5.

**Labels:** pippo, mvp, ai, privacy · **Priority (suggested):** High · **Target:** M1.4

## MVP-8 — Create and resume repeatable reminders safely

**As a** Linux desktop user, **I want** one-shot and repeating reminders that survive restart and sleep, **so that** missed alerts do not become a notification flood.

**Context:** [PRD](../PRD.md) C-6/Q-5; FEATURES FR-20; ROADMAP M1.5; OQ-32 DECIDED.

### Acceptance Criteria

- Given a repeating reminder form opens, when no interval is entered, then the field starts blank and save is disabled; an interval below one minute is rejected with a reason.
- Given a one-minute or longer interval is saved, when its due time arrives, then a reminder is shown according to the selected local time/timezone.
- Given the device resumes after several occurrences were missed, when reminders reconcile, then at most one summary appears per reminder and the normal schedule continues without replaying old occurrences again.
- Given a user selects snooze 5, 10, 15, or custom minutes, when the current occurrence is snoozed, then only that occurrence is delayed by the selected duration.
- Given system DND is active and detectable, when a reminder becomes due, then Pippo honors DND; if detection is unavailable, it states the limitation and does not claim DND support.
- Given the reminder repeats forever or has an end date/count, when the end condition is reached, then no later occurrence is scheduled.

### Business Rules

- Default repeat end is “until turned off”; default snooze is 10 minutes; no separate Pippo quiet-hours schedule.

### Out of Scope

- Meeting/prayer policy changes, command execution, or replaying every missed occurrence.

### Dependencies

- Blocked by: MVP-1 and P0-10.
- Relates to: FR-20, OQ-32.

**Labels:** pippo, mvp, reminders · **Priority (suggested):** High · **Target:** M1.5

## MVP-9 — Keep the display awake only when requested

**As a** Linux desktop user, **I want** a visible, time-limited keep-awake control, **so that** the display stays active during a task without changing system policy permanently.

**Context:** [PRD](../PRD.md) C-6/Q-5; FEATURES FR-22; ROADMAP M1.5; OQ-33 DECIDED.

### Acceptance Criteria

- Given Caffeine is off, when Pippo starts, then no idle or suspend inhibitor is active.
- Given the user turns Caffeine on without changing duration, when the desktop supports idle inhibition, then the display-idle inhibitor is requested for 30 minutes and the UI shows remaining time.
- Given the user turns it off, it expires, the application exits, or the request fails, when the OS releases/denies the handle, then the UI reflects the actual state and normal idle behavior can resume.
- Given the user explicitly enables suspend inhibition and the backend supports it, when the request is active, then it is visibly distinct from display-idle inhibition; otherwise suspend remains uninhibited.
- Given the session is manually locked or system policy denies inhibition, when Caffeine is active, then Pippo does not bypass the lock or policy.

### Out of Scope

- Simulating mouse/keyboard/presence, auto-enabling on battery, or suspend inhibition by default.

### Dependencies

- Blocked by: MVP-1 and P0-10.
- Relates to: FR-22, OQ-33, Q-5.

**Labels:** pippo, mvp, caffeine · **Priority (suggested):** Medium · **Target:** M1.5

## MVP-10 — Receive Pippo notifications and recover from failures

**As a** Linux desktop user, **I want** Pippo's own alerts and recovery events without taking over the desktop notification service, **so that** I can see important states without duplicate popups.

**Context:** [PRD](../PRD.md) C-5/Q-5; FEATURES FR-11/15; ROADMAP M1.1/M1.6; OQ-24 DECIDED.

### Acceptance Criteria

- Given Pippo sends one of its own alerts, when the OS notification service is available, then the alert appears once and follows DND/fullscreen policy as supported.
- Given `org.freedesktop.Notifications` belongs to the OS notification server, when Pippo starts or stops, then Pippo never claims or disconnects that service.
- Given a second Pippo instance starts, when the primary is alive, then the primary is focused/signaled and no duplicate timers or notifications start.
- Given an internal provider crashes, when recovery runs, then the core UI remains available and the failure state is visible.
- Given external notification observation is unavailable, when the hub is opened, then it reports unavailable and the OS continues showing notifications normally.

### Out of Scope

- Universal third-party notification inbox or external history persistence; those adapters are M2.4.

### Dependencies

- Blocked by: MVP-1 and P0-9 where notification observation is used.
- Relates to: FR-11/15, OQ-24, Q-5.

**Labels:** pippo, mvp, notifications, recovery · **Priority (suggested):** High · **Target:** M1.1/M1.6

## MVP-11 — Review agent events in Codex CLI

**As a** developer, **I want** completion and approval-request notifications from my opted-in Codex CLI source, **so that** I can return to the agent for review without approving anything from Pippo.

**Context:** [PRD](../PRD.md) C-7/Q-5; FEATURES FR-19; ROADMAP M1.6; OQ-30/OQ-31 DECIDED.

### Acceptance Criteria

- Given a verified Codex CLI adapter sends a valid completion event, when Pippo accepts it, then one completion notification appears with the matching task and Open Agent target.
- Given a valid approval-request event arrives, when Pippo displays it, then the only action is Open Agent/Open Task; Pippo sends no Approve/Reject request.
- Given the event is duplicate, out of order, expired, malformed, oversized, or unauthenticated, when Pippo receives it, then it is ignored/rejected without changing pending approval state or logging secrets.
- Given the source is disabled, when another event arrives, then no event is shown and other Pippo features continue working.

### Out of Scope

- Direct approval, auto-approval, process/terminal scraping, or non-Codex connectors.

### Dependencies

- Blocked by: MVP-1 and P0-14.
- Relates to: FR-19, OQ-30/OQ-31, Q-5.

**Labels:** pippo, mvp, agent-events · **Priority (suggested):** High · **Target:** M1.6
