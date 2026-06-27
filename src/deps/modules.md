# Modules

A module is just a Zig source file (and its transitive imports) that you can give a name and wire into other modules. It's the primary way Zig code is shared between packages.

## Creating a module

```zig
// in your library's build.zig
const mod = b.addModule("princ", .{
    .root_source_file = b.path("src/root.zig"),
    .target = target,
    .optimize = optimize,
});
```

This makes `princ.module("princ")` available to anyone who depends on your package.

## Consuming a module

```zig
// in the consumer's build.zig
const princ = b.dependency("princ", .{
    .target = target,
    .optimize = optimize,
});

const exe = b.addExecutable(.{
    .name = "myapp",
    .root_module = b.createModule(.{
        .root_source_file = b.path("src/main.zig"),
        .target = target,
        .optimize = optimize,
        .imports = &.{
            .{ .name = "princ", .module = princ.module("princ") },
        },
    }),
});
```

Then in `src/main.zig`:

```zig
const princ = @import("princ");

pub fn main() !void {
    princ.princ(.hot_pink, "hello {s}\n", .{"world"});
}
```

## Module vs Library

| | Module | Static Lib (`.a`) |
|---|---|---|
| Zig → Zig | ✅ use this | ❌ awkward |
| Zig → C | ❌ | ✅ use this |
| C → Zig | ❌ | ✅ use this |

If both sides are Zig, always use a module. The `.a` is only needed for C interop.
