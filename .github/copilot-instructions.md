# LowAudioLatency AI Coding Guidelines

## Architecture Overview
This Rust project minimizes Windows audio latency by setting buffer sizes to minimum supported values for output (render) and input (capture) devices. It uses multi-threading for concurrent audio endpoint configuration and manipulates CPU affinity to optimize real-time performance.

Key components:
- `src/main.rs`: Entry point handling process setup, argument parsing, thread spawning
- `src/sound.rs`: Audio device enumeration and buffer configuration using WASAPI
- `src/cpuset.rs`: CPU set information retrieval and system-wide CPU affinity management

## Critical Workflows
- **Build Release**: Run `build.bat` to compile optimized binary with size monitoring
- **Debug Run**: Use `run.bat` for iterative development with auto-restart
- **Admin Privileges**: Must run as administrator for CPU set modifications and debug privilege elevation

## Project Conventions
- **Panic Handling**: Use `panic = "abort"` in all profiles for minimal binary size
- **Optimization**: Release builds use `opt-level = "z"`, LTO, single codegen unit, and symbol stripping
- **COM Initialization**: Always call `CoInitializeEx(None, COINIT_MULTITHREADED)` before audio operations
- **Thread Management**: Spawn audio configuration threads and keep them parked indefinitely
- **Error Handling**: Use `.expect()` for critical failures, print diagnostics for non-fatal issues

## Windows-Specific Patterns
- **Audio APIs**: Use `IAudioClient3` for shared mode engine period queries and stream initialization
- **CPU Affinity**: Check `SYSTEM_CPU_SET_INFORMATION_REALTIME` flag before modifying system allowed CPU sets
- **Privilege Elevation**: Enable `SE_INC_BASE_PRIORITY_NAME` privilege for CPU set operations
- **Process Priority**: Set to `IDLE_PRIORITY_CLASS` and configure power throttling for execution speed

## Dependencies
- `windows` crate with specific features for Audio, Security, Threading APIs
- `ntapi` for low-level NT system information functions
- `winres` build dependency for embedding icons in release builds

## Examples
- Default audio settings: `vec![(eRender, eConsole, 0), (eCapture, eCommunications, 0)]`
- Buffer size check: Compare `pminperiodinframes >= pdefaultperiodinframes` to avoid unnecessary changes
- CPU set bitmask: `vec![(1 << number_of_processors()) - 1]` for all-processor affinity</content>
<parameter name="filePath">c:\Users\dusti\Documents\GitHub\LowAudioLatency\.github\copilot-instructions.md