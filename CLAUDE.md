# OctoPrint-BedLevelVisualizer Development Context

## Project Overview
Plugin for OctoPrint that visualizes bed mesh leveling data from various firmware (Marlin, Klipper, etc.). Primarily Python/JavaScript.

**Current focus**: Progress tracking features for bed leveling operations, improving visualization accuracy.

## Technical Stack
- Python 2.7/3.x compatible (OctoPrint constraint)
- Frontend: Knockout.js, Plotly.js for 3D visualization
- Backend: OctoPrint plugin API
- Testing: Limited test coverage currently

## Development Constraints
- Must maintain backward compatibility with OctoPrint 1.4+
- Python 2.7 compatibility required (legacy OctoPrint installs)
- Avoid breaking changes to existing API
- Plugin settings migration must be handled gracefully

## Code Style & Preferences
- Direct, blunt feedback on approach - no hand-holding
- Use existing code patterns from the repo - consistency over "best practices"
- Don't over-engineer solutions - this is a hobbyist project

## Progress Bar Feature Context
- Work with GitHub issues at insanityinside/OctoPrint-BedLevelVisualizer ("origin" in git)

**Current implementation**: Real-time progress bar, percentage, and ETA countdown during G29 bed probing
- Backend tracks probe timing via WebSocket events (not polling), calculates ETA
- Caches total execution time and probe count to OctoPrint settings after each successful run
- On subsequent runs, cached data provides ETA from the first probe onwards
- Cache invalidation: if probe count changes (grid config changed), derives per-probe estimate from cached data
- Smooth animation with dynamic tick intervals (200ms-1000ms)
- Supports variable grid sizes (3x3 through 10x10 tested)
- Marlin only currently; non-Marlin firmware tracked in issues #2-4

**Design decisions**:
- ETA calculation in backend (more accurate than frontend timing)
- Measured probe timing takes over from cached estimates once probe 2 arrives
- Cached execution time (#6) preferred over counting/storing probe points (#5, closed) — simpler, firmware-agnostic foundation
- "Probing point X of Y" display removed — redundant with progress bar/percentage, and eliminating it sidesteps the skipped-points accuracy problem
- Frontend `calculateTickInterval` priority: measured average → backend ETA → 10s default (last resort, true first run only)

## Common Tasks
- Adding new firmware support (follow existing parser patterns)
- Extending visualization features (Plotly config in static/js)
- Settings/configuration changes (settings dict in __init__.py)
- WebSocket event handling for real-time updates

## What NOT to waste time on
- Don't suggest complete rewrites or major refactors unless explicitly asked
- Don't explain OctoPrint basics - assume familiarity
- Don't add type hints everywhere (Python 2.7 compat)
- Skip the "we should add comprehensive tests" speech

## Current Work
Branch: `progress-bar`

**Completed**:
- Progress bar, percentage, ETA working for Marlin
- Removed "Probing point X of Y" display (redundant)
- Cached execution time and probe count (#6) — stored in plugin settings, used for first-probe ETA and cache invalidation
- Closed #5 (superseded by #6)

**Next priorities**:
1. Probe-gated progress (#1) — use probe messages as checkpoints to cap progress bar, prevent it outrunning reality. Depends on #6 (done). Handles stall detection.
2. Klipper support (#2) — different probe message format, no count/total. Research needed.
3. Prusa Firmware (#3) — Marlin fork, may already work. Research needed.
4. Smoothieware (#4) — independent firmware. Research needed.

## Useful References
- OctoPrint plugin docs: https://docs.octoprint.org/en/master/plugins/
- Plotly.js: https://plotly.com/javascript/
- Marlin G-code: https://marlinfw.org/meta/gcode/

## Non-Marlin Firmware Support
See `docs/firmware-support-research.md` for detailed output format analysis.

## Live Testing Environment
OctoPrint test instance available
Credentials/details in `.env` (not in git)
Local `octoprint_bedlevelvisualizer/` → Remote `$FILEPATH` (from `.env`)
Deploy: SCP local changes to remote, then `$RESTART_COMMAND`
Use the Chrome connector to access the UI for manual testing. Service restart available from menu to refresh the interface after making changes, or via ssh
API key can be made created and credentials stored in the `.env` file
