# CLI Cheat Sheet

## Common commands

```bash
zig build                          # default step (install artifacts to zig-out/)
zig build run                      # run your "run" step
zig build test                     # run your "test" step
zig build --help                   # list all available steps
zig build run -- arg1 arg2         # pass args to your exe
```

## Optimize flags

```bash
zig build                          # Debug (default)
zig build -Doptimize=ReleaseSafe   # optimized + safety checks on
zig build -Doptimize=ReleaseFast   # max speed
zig build -Doptimize=ReleaseSmall  # smallest binary
```

## Cross compilation

```bash
zig build -Dtarget=aarch64-linux-gnu
zig build -Dtarget=x86_64-windows-gnu
zig build -Dtarget=wasm32-freestanding
zig build -Dtarget=x86_64-macos
```

## Package management

```bash
zig init                           # scaffold a new project
zig fetch --save <url>             # add a remote dependency
zig build --fetch                  # fetch all dependencies without building
```

## Debugging the build

```bash
zig build --verbose                # show all commands run
zig build --summary all            # show what was built/cached
zig build 2>&1 | head -50         # pipe stderr to see full errors
```

## Output

```
zig-out/
  bin/    ← executables
  lib/    ← static/shared libraries
  include/ ← headers (if you install any)
```
