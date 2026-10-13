# Changelog

All notable changes to this project. Format based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/); versioning follows [Semantic Versioning](https://semver.org/).

## [1.3.1] - 2026-10-13

### Fixed

- Stray `true` text next to the 事件日志 (event log) section title — `buildSection` was mistakenly passed a boolean as an extra header element.
- `TypeError` when a `room.state` frame lacks the `spectators` field — `renderMatch` now tolerates partial frames, so one odd frame can no longer stall the panel's render loop.

### Changed

- `mod.json` gains a `repository` metadata field; docs and version strings synced.

## [1.3.0] - 2026-10-13

### Added

- **Invite friends**, end to end:
  - The 邀请 (invite) section shows the password-free `?room=CODE` link while you are in an alliance, with one-click copy (an `execCommand` fallback for plain-HTTP origins) plus a copy-the-code-only button.
  - An online roster from `GET /api/players` (lobby / in-room / in-match badges) with a per-player 邀请 button (`room.invite` → the server forwards a targeted `room.invited` push, rate-limited by the heavy bucket).
  - An incoming invitation appears as a top banner with 立即加入 (join via the game's own `room.join`) / 忽略 (dismiss); joining before entering the game is refused with a hint.
  - Graceful degradation: on a server without `/api/players` the roster hides itself after a single notice and stops polling; copying the invite link keeps working.
- The settings panel and layout presets cover the new 邀请 section.

### Changed

- The roster poll (every 10 s) runs only while the invite section is visible, the tab is in the foreground and the panel is not paused.

## [1.2.0] - 2026-10-12

First entry into the game repository with the 0.2.5 upgrade (the 1.0–1.2 evolution predates the imported history).

### Added

- Latency RTT from the game's own heartbeat with a 60-sample sparkline, jitter, loss and clock offset.
- Connection state, reconnect attempts, last error detail.
- Server panel from `/healthz` (players, rooms, matches, version, uptime).
- Match panel (room code, mode, difficulty, seats, phase, round, countdown).
- Performance panel (FPS / frame time — battles are simulated client-side, so this is the stutter metric).
- Colour-graded event log with server `detail` lines, filters, expand-all and copy.
- Draggable, collapsible, mini mode, pause, layout presets, position memory; `Shift+H` to recall; author credits.
