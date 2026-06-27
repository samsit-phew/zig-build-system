# Zig Build System

The Zig build system lives inside `build.zig` at the root of your project. Instead of a DSL like Make or CMake, it's just Zig code — you write a `build` function, describe what you want built, and `zig build` runs it.

```zig
const std = @import("std");

pub fn build(b: *std.Build) void {
    // everything hangs off b
}
```

The mental model is simple:

- **Artifacts** are things you produce (executables, libraries, modules)
- **Steps** are things you run (install, test, run, custom commands)
- **dependOn** is the glue that connects them

Nothing runs unless it's reachable from a step you invoke. Everything is lazy.

---

This book covers Zig `0.16.0`. The build API changed significantly between 0.13 and 0.16, so older examples you find online will likely be wrong.
