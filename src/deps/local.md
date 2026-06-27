# Using a Local Package

A local package is one that lives on your filesystem, like a sibling directory. Great for splitting a project into multiple packages without publishing anything.

## Setup

Say you have:
```
projects/
  princ/          ← your color library
    src/root.zig
    build.zig
    build.zig.zon
  server/         ← your app
    src/main.zig
    build.zig
    build.zig.zon
```

**Step 1** — expose a module in `princ/build.zig`:

```zig
pub fn build(b: *std.Build) void {
    const target = b.standardTargetOptions(.{});
    const optimize = b.standardOptimizeOption(.{});

    _ = b.addModule("princ", .{
        .root_source_file = b.path("src/root.zig"),
        .target = target,
        .optimize = optimize,
    });
}
```

**Step 2** — declare the dependency in `server/build.zig.zon`:

```zig
.{
    .name = .server,
    .version = "0.1.0",
    .minimum_zig_version = "0.16.0",
    .paths = .{""},
    .dependencies = .{
        .princ = .{
            .path = "../princ",
        },
    },
}
```

**Step 3** — use it in `server/build.zig`:

```zig
const princ_dep = b.dependency("princ", .{
    .target = target,
    .optimize = optimize,
});

const exe = b.addExecutable(.{
    .name = "server",
    .root_module = b.createModule(.{
        .root_source_file = b.path("src/main.zig"),
        .target = target,
        .optimize = optimize,
        .imports = &.{
            .{ .name = "princ", .module = princ_dep.module("princ") },
        },
    }),
});
```

**Step 4** — use it in code:

```zig
const princ = @import("princ");

pub fn main(init: std.process.Init) !void {
    princ.princ(.cyan, "Server started\n", .{});
}
```
