# How it Works

When you run `zig build`, Zig compiles your `build.zig` and runs it. Your `build` function builds up a **directed acyclic graph** (DAG) of steps. Then Zig executes whatever steps you asked for, in the right order, in parallel where possible.

```
zig build          → runs the default step (usually "install")
zig build run      → runs your "run" step
zig build test     → runs your "test" step
zig build --help   → lists all available steps
```

The `b` parameter is your handle to the build graph. Every artifact, module, and step you create is attached to it.

```zig
pub fn build(b: *std.Build) void {
    // b.addExecutable    → create an exe artifact
    // b.addLibrary       → create a lib artifact
    // b.addModule        → create a zig module (no artifact)
    // b.addTest          → create a test runner
    // b.step             → create a named step (zig build <name>)
    // b.dependency       → pull in a package from build.zig.zon
    // b.installArtifact  → add artifact to the default install step
}
```

Nothing is built unless a step that depends on it is actually invoked. If you create an executable but never call `b.installArtifact(exe)` or wire it to a step, it simply won't build.
