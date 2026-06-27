# Target and Optimize

Always add these two at the top of your `build` function. They let callers control the build from the command line.

```zig
const target = b.standardTargetOptions(.{});
const optimize = b.standardOptimizeOption(.{});
```

Then pass them into every module you create:

```zig
b.createModule(.{
    .root_source_file = b.path("src/main.zig"),
    .target = target,
    .optimize = optimize,
});
```

## Target

Controls what platform to build for.

```bash
zig build                              # native (your machine)
zig build -Dtarget=aarch64-linux       # ARM Linux
zig build -Dtarget=x86_64-windows     # Windows 64-bit
zig build -Dtarget=wasm32-freestanding # WebAssembly
```

Zig cross-compiles out of the box with no extra toolchain needed.

## Optimize

Controls the optimization level.

```bash
zig build                        # Debug (default) — fast compile, slow run, safety checks on
zig build -Doptimize=ReleaseSafe # optimized + safety checks
zig build -Doptimize=ReleaseFast # max speed, no safety
zig build -Doptimize=ReleaseSmall # min binary size
```

For development always use `Debug`. Ship with `ReleaseSafe` unless you have a specific reason for `ReleaseFast`.
