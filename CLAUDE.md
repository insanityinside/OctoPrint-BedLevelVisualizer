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

**Current implementation**: Real-time ETA countdown during G29 bed probing
- Backend tracks probe timing and calculates ETA
- Frontend polls for progress updates (currently 200ms interval)
- Supports variable grid sizes (3x3 through 10x10 tested)
- Progress starts at 0%, measures actual probe duration for accuracy

**Known issues/TODOs**:
- 200ms polling interval may be excessive, investigate optimization
- Initial messages before probing starts need refinement
- Need to test with non-Marlin firmware (Klipper, Prusa Firmware, and Smoothieware)

**Design decisions**:
- Moved ETA calculation to backend (more accurate than frontend timing)
- Using measured probe timing rather than estimates (handles varying bed sizes)
- Progress bar animation smoothed to reduce jerkiness on larger grids

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

**Active**: Progress tracking during bed mesh probing (progress-bar branch)
- Progress bar, percentage, ETA now working for Marlin
- Need to handle skipped probe points (Marlin skips out-of-range points)
- Need to add non-Marlin firmware support (Klipper, Prusa, Smoothieware)

**Next priorities**:
1. Store/use previous probe counts to handle skipped points accurately
2. Klipper support (different output format, no probe count in messages)

See GitHub issues for detailed specs.

## Current Work
Recent: Adding progress indicators during G29/mesh probing operations

**Next priorities**:
1. Handle skipped probe points - see issue #1 for detailed spec
2. Add support for Klipper/Klippy - issue #2
3. Add support for Prusa Firmware if possible - issue #3
4. Add support for Smoothieware if possible - issue #4

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
