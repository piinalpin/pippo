---
title: Pippo v1 capability completion
date: 2026-11-20
status: draft
planner: none
project: ""
hierarchy: epic
---

# Pippo v1 capability completion

## Summary

Complete the Must capabilities scheduled for v1 after the MVP gate: offline prayer and calendar experiences, internal brightness, source-gated FX and Teams workflows, opt-in external notification adapters, accessibility, packaging, and beta/release readiness. Integrations remain capability-based and unsupported cases must keep a clear fallback.

## Decisions

- PRD 0.6.18 is the approved product baseline; ROADMAP 1.2 is approved.
- One epic file per roadmap phase — user.
- Group stories by P0/MVP/v1 phase — (agent's call).
- Text-only ticket drafts — user.
- C-12/P2 features, including SSO and server monitor, are excluded — PRD/OQ-15/OQ-34.
- Salat requires explicit method selection until an Indonesia default mapping is verified — OQ-11 conditional.
- English is required; Bahasa Indonesia depends on capacity — OQ-17 conditional.

## Epic

### Problem

The PRD's v1 capabilities depend on external providers and desktop-specific behavior that have not yet been proven. A working MVP alone does not justify compatibility, accessibility, packaging, or release claims.

### Goal

Complete the approved v1 workflows and release gates with evidence for each claimed desktop/package combination. Conditional integrations have a tested fallback and never invent success or data.

### Users

- Linux desktop user — get accurate prayer/calendar information, meeting actions, notifications, and system controls.
- Beta/release user — install, upgrade, recover, and remove Pippo with clear compatibility and privacy information.

### Success Metrics

- C-8 through C-11 and Q-6/Q-7 have evidence for every supported claim or an owner-approved exception.
- Q-4 soak and crash-free targets are measured using the protocol completed under OQ-38.
- Every released package passes install/upgrade/uninstall on the combinations named in the compatibility matrix.

### Scope

- M2.1 prayer, local calendar, and internal brightness.
- M2.2 source-gated FX.
- M2.3 authorized Teams meeting reminders, Join, and conditional call actions.
- M2.4 opt-in external notification adapters.
- M2.5 accessibility, i18n, and multi-monitor validation.
- M2.6 packaging, license audit, soak, documentation, beta and rollback readiness.

### Out of Scope

- C-12/P2 features, unsupported desktop claims, public holiday providers, call automation, or approval actions inside Pippo.
- Setting dates, beta participants, or resource budgets before OQ-39 is resolved.

### Anti-goals

- Showing prayer calculations as an official local method before parameters are verified.
- Showing stale or fabricated FX values as live, or claiming fees/net payout from an exchange rate.
- Opening a meeting or answering a call without an explicit user action.
- Publishing a package for an untested distro/compositor combination.

### Stories

| # | Story / task | Type | Depends on |
| --- | --- | --- | --- |
| V1-1 | Choose a verified prayer schedule | Story | MVP gate, OQ-11 condition |
| V1-2 | Use a local calendar without network access | Story | MVP gate, P0-11 |
| V1-3 | Adjust internal display brightness safely | Story | MVP gate, FR-05 backend probe |
| V1-4 | Check an FX rate with source and freshness | Story | MVP gate, P0-12, source access validation |
| V1-5 | Receive authorized Teams meeting reminders | Story | MVP gate, P0-13, OQ-39 not required for feature work |
| V1-6 | Open a meeting through the chosen OS handler | Story | V1-5 |
| V1-7 | Respond to calls only through a verified adapter | Story | MVP gate, P0-13 |
| V1-8 | View only supported, opted-in external notifications | Story | MVP-10, P0-9 |
| V1-9 | Use Pippo with keyboard, assistive settings, and multiple displays | Story | MVP gate |
| V1-10 | Install and recover Pippo on claimed package targets | Task | V1-1–V1-9, OQ-37/OQ-38 |
| V1-11 | Establish product outcome evidence before beta | Task | OQ-35 |
| V1-12 | Approve beta ownership and rollback plan | Task | OQ-39 |

### Constraints

- KDE Plasma Wayland and Hyprland are the v1 target desktops; report results separately.
- Microsoft Graph uses delegated, minimum-scope authorization; tokens stay in keyring.
- FX shows source and fetched-at; no fabricated provider timestamp or net payout.
- English is the minimum UI language; Indonesian remains conditional.
- Package support claims require tested distro, Qt, compositor, and portal versions.

### Risks & Dependencies

- M2.1 depends on the MVP gate and OQ-11's explicit-selection/verified-mapping rule.
- M2.6 depends on OQ-37 compatibility evidence, OQ-38 reliability protocol/results, and OQ-39 release plan.
- Microsoft tenant consent, FX access terms, call adapter, notification observer, and inhibitor support may remain unavailable; fallback is part of acceptance.

### Decisions

- Approved product scope and release gates — user-approved PRD.
- Separate per-phase epics — (agent's call).

### Open Questions

- OQ-35 evidence/target is resolved by V1-11 before beta.
- OQ-39 owners, capacity, participants, schedule, support, and rollback are resolved by V1-12 before beta/release dates.
- OQ-37/38 evidence and protocol remain prerequisites to compatibility and reliability claims.

---

## V1-1 — Choose a verified prayer schedule

**As a** user who follows prayer times, **I want** offline daily prayer times using a method I chose, **so that** reminders are predictable and not mislabeled as official without evidence.

**Context:** [PRD](../PRD.md) C-8/Q-5; FEATURES FR-07; ROADMAP M2.1; OQ-11 conditional.

### Acceptance Criteria

- Given a user has entered a city or coordinates, when the prayer schedule is calculated offline, then Subuh, Zuhur, Asar, Magrib, and Isya are displayed for the selected date and timezone.
- Given the Indonesia default mapping has not been verified, when onboarding asks for a method, then the user must choose a method explicitly and no official Kemenag label is shown.
- Given a verified method and reference set are selected, when representative dates and locations are tested, then deviations are recorded against that reference before a default is enabled.
- Given location permission is not granted, when the schedule is opened, then manually entered location continues to work and no geolocation request occurs.
- Given the device resumes after sleep or timezone changes, when the schedule refreshes, then past alerts are not replayed as a burst and the next prayer is recalculated.

### Business Rules

- Location is manual by default; geolocation and audio adzan are opt-in. Default reminder is silent visual.

### Out of Scope

- Network-based calculation, silent location collection, or labeling unverified results as official.

### Dependencies

- Blocked by: MVP gate and OQ-11 conditional method rule.
- Relates to: FR-07, Q-5.

**Labels:** pippo, v1, prayer · **Priority (suggested):** High · **Target:** M2.1

## V1-2 — Use a local calendar without network access

**As a** Linux desktop user, **I want** a monthly calendar with Today and month navigation, **so that** I can check dates even when offline.

**Context:** [PRD](../PRD.md) C-8; FEATURES FR-13; ROADMAP M2.1.

### Acceptance Criteria

- Given the Calendar tab is first opened, when the view renders, then the current local month and Today are correct for the system timezone.
- Given the user selects Previous month, Next month, or Today, when navigation crosses month/year boundaries or a leap year, then the displayed dates remain correct.
- Given the user selects a date, when details are shown, then Pippo displays that date and weekday without creating an event or reminder.
- Given the device is offline, when the calendar is used, then it works without a network request or background polling.
- Given keyboard-only navigation is used, when the user moves between dates and controls, then focus and selection remain visible and operable.

### Out of Scope

- Public holidays, event feeds, meeting sync, or holiday markers.

### Dependencies

- Blocked by: MVP gate and P0-11.
- Relates to: FR-13, C-8, Q-6.

**Labels:** pippo, v1, calendar · **Priority (suggested):** Medium · **Target:** M2.1

## V1-3 — Adjust internal display brightness safely

**As a** laptop user, **I want** to adjust internal display brightness from Pippo, **so that** I can change screen brightness without opening system settings.

**Context:** [PRD](../PRD.md) C-3; FEATURES FR-05; ROADMAP M2.1.

### Acceptance Criteria

- Given a supported internal display backend is present, when the brightness slider changes, then the actual OS brightness changes and the displayed value reflects the read-back state.
- Given the display has no supported backlight control or permission is denied, when the control is opened, then Pippo shows Unavailable with a reason and no fake slider.
- Given a brightness update fails, when the error is returned, then the prior OS state is displayed and no silent permission change is attempted.

### Out of Scope

- External monitor brightness or elevation through sudo/polkit without explicit design.

### Dependencies

- Blocked by: MVP gate and supported backend probe.
- Relates to: FR-05, C-3, Q-5.

**Labels:** pippo, v1, brightness · **Priority (suggested):** Medium · **Target:** M2.1

## V1-4 — Check an FX rate with source and freshness

**As a** user checking MYR to IDR, **I want** a rate with its source and retrieval time, **so that** I can distinguish live, cached, stale, and unavailable data.

**Context:** [PRD](../PRD.md) C-9/Q-5; FEATURES FR-12; ROADMAP M2.2; OQ-21; P0-12.

### Acceptance Criteria

- Given a valid JSON profile, when the configured endpoint responds with the mapped numeric field, then the rate, pair, source, and fetched-at are shown.
- Given the selected MoneyMatch raw-number profile responds with a valid decimal in a `text/html` body, when the response is parsed, then the number is accepted without an HTML parser or precision loss.
- Given the body is HTML, malformed, empty, nonpositive, non-finite, has trailing junk, or the endpoint returns an HTTP error, when refresh completes, then the invalid value is rejected and last-known-good data is labeled stale/unavailable.
- Given the provider supplies no timestamp, when the rate is displayed, then Pippo shows fetched-at only and does not claim provider-updated time, fees, or net payout.
- Given a request times out or returns an error, when the panel displays status, then the shell remains responsive and does not silently switch source.

### Out of Scope

- Money transfer, guaranteed settlement rates, scraping, or calling the observed rate a stable/public API without terms validation.

### Dependencies

- Blocked by: MVP gate, P0-12, and source access/rate-limit validation.
- Relates to: FR-12, OQ-21, Q-5.

**Labels:** pippo, v1, fx, source-gated · **Priority (suggested):** Medium · **Target:** M2.2

## V1-5 — Receive authorized Teams meeting reminders

**As a** Microsoft calendar user, **I want** reminders from authorized calendar events and a manual fallback, **so that** I can prepare for meetings without scraping browser sessions.

**Context:** [PRD](../PRD.md) C-10/Q-5; FEATURES FR-16; ROADMAP M2.3; OQ-27/OQ-28.

### Acceptance Criteria

- Given the user grants delegated Graph consent and tenant policy allows the requested minimum scope, when the calendar sync succeeds, then meeting title, start time, timezone, and valid join URL are available to the reminder workflow.
- Given the user denies/revokes consent or sync fails, when the meeting reminder view opens, then Pippo explains the failure and permits manual link/time entry without a false synced event.
- Given a meeting starts at 10:00 in `Asia/Kuala_Lumpur`, when the default reminder is scheduled, then it appears at 09:55 Malaysia time with the correct local display.
- Given a meeting is rescheduled, cancelled, recurring, or has an exception, when sync refreshes, then outdated occurrences are updated/deduplicated and no cancelled meeting reminder remains active.
- Given the optional at-start reminder is off, when the meeting begins, then no at-start alert appears; enabling it adds the alert without removing the default T−5 alert.

### Business Rules

- Never read Teams/browser cookies or session tokens; Graph delegated auth only. Manual time/link entry is local fallback.

### Out of Scope

- Calendar scraping, auto-joining, or a general calendar sync unrelated to meeting reminders.

### Dependencies

- Blocked by: MVP gate and P0-13.
- Relates to: FR-16, OQ-27/OQ-28, Q-5.

**Labels:** pippo, v1, teams, source-gated · **Priority (suggested):** High · **Target:** M2.3

## V1-6 — Open a meeting through the chosen OS handler

**As a** meeting attendee, **I want** Join to open the selected valid meeting link, **so that** I can choose whether to enter the call.

**Context:** [PRD](../PRD.md) C-10/Q-5; FEATURES FR-17; ROADMAP M2.3; OQ-28 decided.

### Acceptance Criteria

- Given a meeting has a validated `joinUrl` and no custom handler is selected, when the user clicks Join, then the URL opens in the OS default browser.
- Given the user selects another supported handler, when Join is clicked, then the selected handler receives that meeting URL.
- Given the URL is missing or invalid, when the meeting card renders, then Join is hidden or disabled with a reason.
- Given the URL contains unsafe content or attempts command injection, when it is validated, then it is rejected without executing a shell command.
- Given the user clicks Join, when the browser opens, then Pippo does not auto-enable camera/microphone or claim the user joined.

### Out of Scope

- Automatic meeting entry, camera/microphone consent, or forcing Teams PWA as handler.

### Dependencies

- Blocked by: V1-5.
- Relates to: FR-17, OQ-28, Q-5.

**Labels:** pippo, v1, meetings · **Priority (suggested):** High · **Target:** M2.3

## V1-7 — Respond to calls only through a verified adapter

**As a** Teams user, **I want** call actions only when the installed client exposes a tested adapter, **so that** Pippo does not claim to control unsupported calls.

**Context:** [PRD](../PRD.md) C-10/Q-5; FEATURES FR-18; ROADMAP M2.3; OQ-29 decided.

### Acceptance Criteria

- Given a supported, user-opted-in adapter reports ringing, when Pippo shows the call banner, then Accept Audio/Video or Decline is available only if that method is supported.
- Given no supported adapter exists, when a call notification is available, then Pippo offers Open Teams only and does not show active Accept/Decline controls.
- Given an action is dispatched, when the adapter has not confirmed the resulting call state, then the UI does not claim the call was accepted.
- Given caller metadata is absent or caller display is not opted in, when the banner renders, then it uses the generic “Panggilan masuk” label.
- Given the user triggers an action, when the client reports rejection/unavailable/timeout, then an error is shown and no second action is sent automatically.

### Out of Scope

- Auto-answer, PWA controls without an action API, reading browser secrets, or displaying caller identity by default.

### Dependencies

- Blocked by: MVP gate and P0-13.
- Relates to: FR-18, OQ-29, Q-5.

**Labels:** pippo, v1, teams, conditional · **Priority (suggested):** Medium · **Target:** M2.3

## V1-8 — View only supported, opted-in external notifications

**As a** desktop user, **I want** to see external notification events only when a supported adapter is enabled, **so that** I can use a lightweight hub without losing normal OS notifications.

**Context:** [PRD](../PRD.md) C-5/Q-5; FEATURES FR-15; ROADMAP M2.4; OQ-24 DECIDED.

### Acceptance Criteria

- Given an external notification source is disabled or unsupported, when an OS notification arrives, then Pippo does not capture it and the OS notification still appears normally.
- Given a supported adapter is explicitly enabled, when a fixture event arrives, then it appears once with source/capability information and no duplicate toast.
- Given third-party history is not opted in, when the hub closes or Pippo restarts, then notification content is not persisted to disk.
- Given a notification is private, marked secret, or hidden by a detectable DND/lockscreen policy, when the adapter receives it, then Pippo suppresses or redacts it according to the supported protocol.
- Given source identity or action metadata is unavailable, when an event is shown, then Pippo uses a generic origin label and does not invent an app, URL, or action.

### Out of Scope

- Universal notification interception, replacing the OS daemon, or reading terminal output/history.

### Dependencies

- Blocked by: MVP-10 and P0-9.
- Relates to: FR-15, OQ-24, Q-5.

**Labels:** pippo, v1, notifications, opt-in · **Priority (suggested):** Medium · **Target:** M2.4

## V1-9 — Use Pippo with keyboard, assistive settings, and multiple displays

**As a** desktop user, **I want** accessible controls and stable layout across displays, **so that** I can use Pippo with different input and visual needs.

**Context:** [PRD](../PRD.md) C-11/Q-6; FEATURES FR-10 and feature-level accessibility ACs; ROADMAP M2.5; OQ-17 conditional.

### Acceptance Criteria

- Given the user navigates without a mouse, when each supported panel/widget is opened and closed, then controls have visible focus, keyboard operation, and accessible labels.
- Given reduced motion is enabled, when media or shell state changes, then nonessential motion is disabled.
- Given high contrast or light/dark mode is used, when the shell and controls render, then state is not communicated by color alone.
- Given a monitor is added, removed, or uses HiDPI/fractional scale, when Pippo reflows, then the island remains visible and controls are not clipped or placed offscreen.
- Given UI strings are rendered, when English is selected, then all v1 flows have English strings; Indonesian is included only if owner confirms capacity under OQ-17.

### Out of Scope

- Formal WCAG conformance claims unless a standard and audit are approved; unsupported screen-reader behavior claims.

### Dependencies

- Blocked by: MVP gate.
- Relates to: C-11, Q-6, OQ-17.

**Labels:** pippo, v1, accessibility, i18n · **Priority (suggested):** High · **Target:** M2.5

## V1-10 — Install and recover Pippo on claimed package targets

**Type:** Task · **Context:** [PRD](../PRD.md) C-11/Q-4/Q-7; ROADMAP M2.6; VALIDATION; OQ-37/OQ-38/OQ-39.

### Acceptance Criteria

- Given a distro/compositor/Qt combination is named as supported, when its package is installed, upgraded, and removed from release files, then all three workflows pass and the exact environment is recorded.
- Given the package is upgraded with user data present, when migration runs, then data is preserved or a recoverable backup/error is provided; no silent overwrite occurs.
- Given license and dependency audit runs, when the release package is prepared, then required notices and license files are present and unreviewed dependencies are flagged.
- Given the release benchmark protocol is approved, when Q-4 is measured, then the report includes the 24-hour workload, RSS-growth result, crash-free numerator/denominator/period, and environment.
- Given a package or data rollback is needed, when the documented rollback procedure is followed, then the previous supported package/data can be restored without silent loss.

### Out of Scope

- Managed repositories before post-beta capacity approval or support claims for untested combinations.

### Dependencies

- Blocked by: V1-1 through V1-9; OQ-37/OQ-38 evidence; V1-12/OQ-39 release plan.
- Relates to: Q-4/Q-7, G-3/G-5/G-6.

**Labels:** pippo, v1, packaging, release · **Priority (suggested):** High · **Target:** M2.6

## V1-11 — Establish product outcome evidence before beta

**Type:** Task · **Context:** [PRD](../PRD.md) G-1; DECISIONS OQ-35; ROADMAP OQ-35 gate.

### Acceptance Criteria

- Given the core MVP workflows are available, when product discovery is completed, then referenced interviews/observations or task data describe the user problem and current context switching.
- Given baseline data exists, when G-1 targets are proposed, then task success and completion-time targets cite that baseline and are not invented in advance.
- Given a beta review is prepared, when G-1 is reported, then the report includes method, participant/sample scope, date, limitations, and approved target.

### Out of Scope

- Mandatory telemetry, unapproved collection of private usage data, or using an unmeasured target as a result.

### Dependencies

- Blocked by: MVP workflows usable; owner review before beta.
- Relates to: G-1, OQ-35, Q-5.

**Labels:** pippo, v1, product-evidence · **Priority (suggested):** Medium · **Target:** Before beta

## V1-12 — Approve beta ownership and rollback plan

**Type:** Task · **Context:** [PRD](../PRD.md) §8/§11; DECISIONS OQ-39; ROADMAP OQ-39 gate.

### Acceptance Criteria

- Given a beta or release date is proposed, when the release plan is reviewed, then named product, engineering, QA, and release owners plus capacity/budget are recorded.
- Given beta participants and support channels are selected, when invitation is prepared, then participant scope, support contact, and feedback/incident route are documented.
- Given the package or user data needs rollback, when the release plan is approved, then backup, upgrade, rollback, and recovery steps are documented and exercised on a test install.
- Given any required owner, capacity, or rollback decision is missing, when a date or public beta invite is proposed, then the plan remains blocked and no date is presented as committed.

### Out of Scope

- Picking actual names, dates, budget, or beta participants inside this ticket draft.

### Dependencies

- Blocked by: product/release owner input; needed before V1-10 can close.
- Relates to: OQ-39, Q-7.

**Labels:** pippo, v1, release-planning · **Priority (suggested):** High · **Target:** Before beta/date commitment
