---
title: Pippo P0 foundation and feasibility
date: 2026-11-20
status: draft
planner: none
project: ""
hierarchy: epic
---

# Pippo P0 foundation and feasibility

## Summary

Create a buildable Pippo foundation and prove that the core shell and risky desktop integrations are feasible on KDE Plasma Wayland and Hyprland. P0 delivers evidence and a capability matrix, not production-ready integrations. The phase establishes whether the MVP can meet the product's responsiveness and desktop-safety goals.

## Decisions

- PRD 0.6.18 is the approved product baseline; ROADMAP 1.2 is approved.
- One epic file per roadmap phase — user.
- Group stories by P0/MVP/v1 phase — (agent's call), because approved ROADMAP uses those phases.
- Text-only ticket drafts — user.
- C-12/P2 integrations are excluded — PRD.
- Qt 6.8 is an accepted M0.1 candidate; supported minimum remains open pending OQ-37 evidence — user decision recorded in DECISIONS.
- Unresolved OQ items become explicit prerequisite tasks or dependencies; ticket ACs do not assume results.

## Epic

### Problem

Pippo has no source, build, or runtime evidence yet. Overlay behavior and integration access differ by compositor, so starting MVP features without a buildable base and feasibility evidence risks focus theft, unsupported claims, or expensive redesign.

### Goal

Build a minimal QML application and prove the P0 shell, performance measurement, and key integration paths on both target desktops. Unsupported capabilities have a recorded reason and fallback.

### Users

- Linux developer / power user — inspect system and media status without leaving the current task.
- Engineering team — make implementation choices from reproducible build and platform evidence.

### Success Metrics

- Build and empty test target pass in CI on the chosen Qt 6.8 candidate.
- P0 shell and capability evidence recorded separately for KDE Plasma Wayland and Hyprland.
- Q-1 to Q-3 measured against their targets; report records results even when targets fail.

### Scope

- M0.1 build foundation and licenses.
- M0.2 overlay shell, CPU/RAM status, and MPRIS media status.
- M0.3 repeatable benchmark protocol and baseline.
- M0.4 feasibility spikes for clipboard, audio/shortcuts, notifications, scheduler/inhibitor, calendar, FX, Teams, and Codex CLI.

### Out of Scope

- Production adapters for integrations before their feasibility and permission gates pass.
- P2/C-12 work, GNOME Wayland, X11, ARM, or package compatibility claims.
- Declaring benchmark targets met without reports.

### Anti-goals

- The pill shifts or replaces a system panel, steals focus, or remains visible over fullscreen by default.
- A capability is called supported based only on a mock or documentation link.
- P0 spikes become undocumented production code.

### Stories

| # | Story / task | Type | Depends on |
| --- | --- | --- | --- |
| P0-1 | Build Pippo's QML foundation in CI | Task | — |
| P0-2 | Open and operate the floating island safely | Story | P0-1 |
| P0-3 | Read CPU and memory status without busy polling | Story | P0-2 |
| P0-4 | Control the active MPRIS media player | Story | P0-2 |
| P0-5 | Finalize a repeatable benchmark protocol | Task | — |
| P0-6 | Record the P0 performance baseline | Task | P0-2, P0-3, P0-4, P0-5 |
| P0-7 | Prove clipboard access on both Wayland desktops | Task | P0-2 |
| P0-8 | Prove per-stream audio and shortcut paths | Task | P0-2 |
| P0-9 | Prove supported notification observation paths | Task | P0-2 |
| P0-10 | Prove scheduler and idle-inhibit behavior | Task | P0-2 |
| P0-11 | Prove the local calendar path | Task | P0-2 |
| P0-12 | Prove the FX endpoint and response modes | Task | P0-1 |
| P0-13 | Prove authorized Teams calendar and call paths | Task | P0-1 |
| P0-14 | Verify the Codex CLI event and handoff path | Task | P0-1 |

### Constraints

- C++20 + Qt 6 Quick/QML; CMake + Ninja candidate from TECH-STACK.
- KDE Plasma Wayland and Hyprland are separate test targets.
- Run as the user; no root, permission changes, hidden notification-server takeover, or browser-secret extraction.
- Benchmark hardware target: x86_64, 8 GiB RAM, iGPU, 1920×1080 at 60 Hz (OQ-16).

### Risks & Dependencies

- Qt 6.8 is only a candidate; minimum supported Qt and distro/compositor/portal matrix remain open in OQ-37.
- OQ-38 still needs engineering/QA sampling and threshold choices before M0.3.
- Clipboard, media, portal, audio, Microsoft tenant, and Codex CLI capability may differ by installed version; record the tested environment.

### Decisions

- Qt 6.8 candidate for M0.1; not a support guarantee — user.
- Per-phase epic grouping — (agent's call).

### Open Questions

- OQ-37: what minimum Qt and distro/compositor/portal combinations pass packaging tests?
- OQ-38: what remaining sampling cadence, RSS-growth threshold, crash-free denominator/period, and reproducible workload will engineering/QA approve?

---

## P0-1 — Build Pippo's QML foundation in CI

**Type:** Task · **Context:** [PRD](../PRD.md) C-1/C-11; ROADMAP M0.1; TECH-STACK; OQ-14.

### Acceptance Criteria

- Given a clean checkout and Qt 6.8 candidate environment, when CI configures and builds the project with CMake/Ninja, then it produces the minimal QML executable.
- Given the project build, when CI runs its initial test target, then the test command completes successfully and its real command is documented in CODEMAP.
- Given a new checkout, when CI runs for the supported build environment, then the build and test jobs report independently and preserve their logs.
- Given license files are part of M0.1, when the project is inspected, then `LICENSE-MIT` and `LICENSE-APACHE` are present and match the approved `MIT OR Apache-2.0` expression.

### Out of Scope

- Product widgets, package release artifacts, or a final minimum Qt support claim.

### Dependencies

- Blocked by: none.
- Relates to: OQ-37 candidate Qt selection.

**Labels:** pippo, p0, foundation · **Priority (suggested):** High · **Target:** M0.1

## P0-2 — Open and operate the floating island safely

**As a** Linux desktop user, **I want** a small island with collapsed, peek, and expanded states, **so that** I can access Pippo without disrupting my desktop.

**Context:** [PRD](../PRD.md) C-1; PRD Q-6; FEATURES FR-03/10; ROADMAP M0.2; ARCHITECTURE §3.

### Acceptance Criteria

- Given KDE Plasma Wayland or Hyprland, when Pippo starts, then the island is top-center and does not move or replace the system panel.
- Given the island is collapsed, when the user opens it by the supported interaction, then it enters peek or expanded state and the selected state is visible.
- Given the island is open, when the user presses Escape or clicks outside, then it closes according to the documented interaction without leaving keyboard focus trapped.
- Given fullscreen hide is at its default, when a fullscreen window is active, then the island hides and does not cover that window.
- Given a monitor is removed or scale changes, when the display layout updates, then the island remains on-screen and its geometry is recalculated.
- Given the island is unavailable on a target environment, when startup probes capabilities, then the user sees an unavailable reason rather than a false working state.

### Out of Scope

- GNOME Wayland, X11, animation polish beyond basic state transitions, or changing system panel configuration.

### Dependencies

- Blocked by: P0-1.
- Relates to: M0.2 shell exit check.

**Labels:** pippo, p0, shell, accessibility · **Priority (suggested):** High · **Target:** M0.2

## P0-3 — Read CPU and memory status without busy polling

**As a** Linux desktop user, **I want** current CPU and memory status in the island, **so that** I can check system load without opening another utility.

**Context:** [PRD](../PRD.md) C-1/Q-1; FEATURES FR-01; ROADMAP M0.2.

### Acceptance Criteria

- Given the monitor is enabled and visible, when sample data arrives, then CPU percentage and used/total memory update without blocking UI interaction.
- Given the island is collapsed, when monitoring remains enabled, then sampling uses the documented lower cadence of about 10–15 seconds rather than the visible cadence of about 2 seconds.
- Given the monitor is disabled or the island is hidden under its disabled policy, when the next sampler cycle is due, then no high-frequency sampling continues.
- Given memory or CPU data is unavailable, when the widget renders, then it shows an unavailable state instead of stale or fabricated values.
- Given a reference system tool is used during validation, when the values are compared, then the report records measurement conditions and sampling tolerance rather than claiming exact equality.

### Out of Scope

- Per-process monitoring, alerts, historical analytics, or claiming Q-1 until the benchmark passes.

### Dependencies

- Blocked by: P0-2.
- Relates to: FR-01, Q-1.

**Labels:** pippo, p0, system-monitor · **Priority (suggested):** High · **Target:** M0.2

## P0-4 — Control the active MPRIS media player

**As a** media user, **I want** to see and control a player that exposes MPRIS, **so that** I can manage playback from the island.

**Context:** [PRD](../PRD.md) C-1; FEATURES FR-03; ROADMAP M0.2.

### Acceptance Criteria

- Given a player exposes MPRIS2, when metadata or playback state changes, then title, artist, album, artwork fallback, duration, and state update from D-Bus events without aggressive polling.
- Given multiple MPRIS players are present, when one is Playing or the user selects a pinned player, then the documented priority/select behavior chooses that player.
- Given a media control is supported by the selected player, when the user invokes it, then play/pause/previous/next/seek is sent to that player only.
- Given a player does not expose a requested control or usable metadata, when the UI renders, then the unsupported or missing-data state is clear and no action is sent to another player.
- Given playback is paused, the island is hidden, or reduced motion is enabled, when an animation frame would run, then the media animation is stopped.

### Out of Scope

- FFT/audio-reactive capture, reading browser tabs, or support for players without MPRIS beyond an explicit unavailable state.

### Dependencies

- Blocked by: P0-2.
- Relates to: FR-03, Q-1/Q-3.

**Labels:** pippo, p0, media · **Priority (suggested):** High · **Target:** M0.2

## P0-5 — Finalize a repeatable benchmark protocol

**Type:** Task · **Context:** [PRD](../PRD.md) G-2/G-6 and Q-1–Q-4; DECISIONS OQ-38; VALIDATION.

### Acceptance Criteria

- Given the accepted OQ-38 proposal, when QA publishes the protocol, then it specifies 5-minute warm-up, 100 interaction samples for p50/p95, 10-minute idle measurement, and 24-hour soak.
- Given CPU/RAM measurements, when the protocol is approved, then it states sampling cadence, aggregation and tolerance for each measure.
- Given the soak and crash-free goals, when the protocol is approved, then it states the RSS-growth threshold, workload, crash-free session denominator and observation period.
- Given a benchmark run, when another engineer follows the protocol on KDE or Hyprland, then the environment, build mode, commands, samples and pass/fail results can be reproduced from the report.

### Out of Scope

- Changing PRD Q targets or making a claim that any target already passes.

### Dependencies

- Blocked by: engineering/QA decisions for remaining OQ-38 parameters.
- Relates to: P0-6.

**Labels:** pippo, p0, benchmark · **Priority (suggested):** High · **Target:** Before M0.3

## P0-6 — Record the P0 performance baseline

**Type:** Task · **Context:** [PRD](../PRD.md) G-2/G-6 and Q-1–Q-3; ROADMAP M0.3; OQ-16/OQ-38.

### Acceptance Criteria

- Given a Release build on the PRD baseline hardware, when Q-1 through Q-3 are measured, then results are recorded separately for KDE Plasma Wayland and Hyprland.
- Given any Q target misses its threshold, when the report is published, then the measured result and environment are recorded as a miss, not described as passing.
- Given the report is complete, when it is added to project docs, then it includes revision, hardware, distro, Qt, compositor, portal, commands, sample count and report path.

### Out of Scope

- Q-4 soak/release sign-off or tuning code to hide failed measurements.

### Dependencies

- Blocked by: P0-2, P0-3, P0-4, P0-5.
- Relates to: Q-1/Q-2/Q-3.

**Labels:** pippo, p0, benchmark · **Priority (suggested):** High · **Target:** M0.3

## P0-7 — Prove clipboard access on both Wayland desktops

**Type:** Task · **Context:** [PRD](../PRD.md) C-2; FEATURES FR-04; ROADMAP M0.4; OQ-36.

### Acceptance Criteria

- Given KDE Plasma Wayland and Hyprland test environments, when the clipboard spike runs, then it records whether background read/write for text and PNG/JPEG works on each desktop.
- Given clipboard access is denied or unavailable, when the spike completes, then the capability matrix records the permission/fallback and confirms no focus theft.
- Given the spike finishes, when engineering reviews the result, then the report documents limits, dependencies, and whether the backend can support the FR-04 retention and restore behavior.

### Out of Scope

- Production history UI, storage, or clipboard content capture beyond synthetic test data.

### Dependencies

- Blocked by: P0-2.
- Relates to: OQ-36 policy, already DECIDED.

**Labels:** pippo, p0, clipboard, spike · **Priority (suggested):** High · **Target:** M0.4

## P0-8 — Prove per-stream audio and shortcut paths

**Type:** Task · **Context:** [PRD](../PRD.md) C-3; FEATURES FR-14; ROADMAP M0.4; OQ-25/OQ-26.

### Acceptance Criteria

- Given two active playback streams, when the spike adjusts volume, mute, or output for stream A, then stream B remains unchanged on each tested desktop.
- Given a candidate GlobalShortcuts portal or desktop fallback, when a binding is registered, then the spike records whether conflicts are detectable and confirms no existing binding is overwritten.
- Given no supported audio or shortcut backend exists, when the matrix is written, then the capability is marked unsupported with the observed reason and fallback.

### Out of Scope

- Microphone-per-app control, persistent custom routing, production UI, or default shortcut bindings.

### Dependencies

- Blocked by: P0-2.
- Relates to: FR-14, OQ-25/OQ-26.

**Labels:** pippo, p0, audio, shortcut, spike · **Priority (suggested):** High · **Target:** M0.4

## P0-9 — Prove supported notification observation paths

**Type:** Task · **Context:** [PRD](../PRD.md) C-5/Q-5; FEATURES FR-15; ROADMAP M0.4; OQ-24.

### Acceptance Criteria

- Given each candidate native/PWA/terminal path, when the spike runs only through an allowed desktop adapter, then the matrix records whether events can be observed on KDE and Hyprland.
- Given no supported observation adapter exists, when the desktop shows a notification, then the OS remains the notification server and the result records an OS-only fallback.
- Given duplicate and private notifications are included in fixtures, when observation is tested, then duplication, consent, DND and data-persistence behavior are recorded without storing private content by default.

### Out of Scope

- Taking over `org.freedesktop.Notifications`, universal interception, or production inbox/history.

### Dependencies

- Blocked by: P0-2.
- Relates to: FR-15, OQ-24.

**Labels:** pippo, p0, notifications, spike · **Priority (suggested):** High · **Target:** M0.4

## P0-10 — Prove scheduler and idle-inhibit behavior

**Type:** Task · **Context:** [PRD](../PRD.md) C-6; FEATURES FR-20/22; ROADMAP M0.4; OQ-32/OQ-33.

### Acceptance Criteria

- Given suspend/resume and wall-clock changes, when a scheduler prototype recalculates a due reminder, then it can avoid replaying every missed occurrence and report the measured behavior.
- Given timed idle inhibition is requested, when KDE and Hyprland accept or reject the portal/compositor request, then the spike records the effective result and release behavior.
- Given the island is hidden or the session locks, when a surface-bound inhibitor is ineffective, then the matrix records unsupported behavior rather than claiming keep-awake succeeded.

### Out of Scope

- Production reminder UI, indefinite keep-awake, or enabling suspend inhibition by default.

### Dependencies

- Blocked by: P0-2.
- Relates to: FR-20/22, OQ-32/OQ-33.

**Labels:** pippo, p0, reminder, caffeine, spike · **Priority (suggested):** High · **Target:** M0.4

## P0-11 — Prove the local calendar path

**Type:** Task · **Context:** [PRD](../PRD.md) C-8; FEATURES FR-13; ROADMAP M0.4.

### Acceptance Criteria

- Given a local date and locale, when a prototype displays a month, then first/previous/next month and Today calculations are correct across month/year boundaries and leap years.
- Given network access is disabled, when the month view is used, then it remains functional and makes no network request.
- Given the spike is reported, when product/engineering reviews the result, then date-library support, accessibility risks and tested locale behavior are documented.

### Out of Scope

- Public holidays, event synchronization, or a production calendar UI.

### Dependencies

- Blocked by: P0-1.
- Relates to: FR-13.

**Labels:** pippo, p0, calendar, spike · **Priority (suggested):** Medium · **Target:** M0.4

## P0-12 — Prove the FX endpoint and response modes

**Type:** Task · **Context:** [PRD](../PRD.md) C-9/Q-5; FEATURES FR-12; ROADMAP M0.4; OQ-21.

### Acceptance Criteria

- Given a JSON fixture and a raw-decimal fixture with `text/html` MIME, when the spike parses each allowed mode, then it returns the configured rate or a precise parse error without evaluating scripts or parsing arbitrary HTML.
- Given timeout, HTTP error, invalid numeric text, or missing JSON field, when the endpoint fails, then the spike preserves last-known-good data as stale/unavailable rather than fabricating a rate.
- Given the selected MoneyMatch endpoint, when its access terms and rate limits are reviewed, then the report separates observed HTTP behavior from permission/stability claims.

### Out of Scope

- Financial transactions, guaranteed payout estimates, scraping, or a production adapter before access policy is validated.

### Dependencies

- Blocked by: P0-1.
- Relates to: FR-12, OQ-21.

**Labels:** pippo, p0, fx, spike · **Priority (suggested):** Medium · **Target:** M0.4

## P0-13 — Prove authorized Teams calendar and call paths

**Type:** Task · **Context:** [PRD](../PRD.md) C-10/Q-5; FEATURES FR-16/18; ROADMAP M0.4; OQ-27/OQ-29.

### Acceptance Criteria

- Given an authorized Microsoft test tenant, when delegated calendar access is attempted, then the report records required consent/scopes and whether event time and `joinUrl` are available.
- Given no tenant consent or no supported call-control adapter, when the spike runs, then it records manual meeting entry or Open Teams as fallback and does not inspect browser cookies or extracted tokens.
- Given a compatible call adapter is available, when a mock ringing event is exercised, then the report distinguishes action dispatch from confirmation that the call was accepted.

### Out of Scope

- Production Graph integration, unauthorized session reuse, automatic call answer, or claiming all Teams clients support call actions.

### Dependencies

- Blocked by: P0-1.
- Relates to: FR-16/18, OQ-27/OQ-29.

**Labels:** pippo, p0, teams, spike · **Priority (suggested):** Medium · **Target:** M0.4

## P0-14 — Verify the Codex CLI event and handoff path

**Type:** Task · **Context:** [PRD](../PRD.md) C-7/Q-5; FEATURES FR-19; ROADMAP M0.4; OQ-30/OQ-31.

### Acceptance Criteria

- Given the selected Codex CLI version and documented event route, when a completion and approval-request fixture is sent, then the spike records payload, authentication, event identity, dedupe and Open Agent target behavior.
- Given an approval-request event is received, when Pippo renders it, then the only v1 handoff is notification plus Open Agent; no Approve/Reject request is sent and pending state remains unchanged.
- Given event payload is malformed, duplicated, oversized, or unauthenticated, when the adapter receives it, then it rejects the event without logging secrets or changing task state.

### Out of Scope

- Production connector, process/terminal scraping, direct approval, or auto-approval.

### Dependencies

- Blocked by: P0-1.
- Relates to: FR-19, OQ-30/OQ-31.

**Labels:** pippo, p0, agent-events, spike · **Priority (suggested):** Medium · **Target:** M0.4
