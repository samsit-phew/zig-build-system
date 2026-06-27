# Executable with a Dependency

A full working `build.zig` for an executable that depends on a local package.

```zig
const std = @import("std");

pub fn build(b: *std.Build) void {
    const target = b.standardTargetOptions(.{});
    const optimize = b.standardOptimizeOption(.{});

    // pull in the princ color library
    const princ = b.dependency("princ", .{
        .target = target,
        .optimize = optimize,
    });

    // build the exe
    const exe = b.addExecutable(.{
        .name = "server",
        .root_module = b.createModule(.{
            .root_source_file = b.path("src/main.zig"),
            .target = target,
            .optimize = optimize,
            .imports = &.{
                .{ .name = "princ", .module = princ.module("princ") },
            },
        }),
    });
    b.installArtifact(exe);

    // zig build run
    const run_cmd = b.addRunArtifact(exe);
    if (b.args) |args| run_cmd.addArgs(args);
    const run_step = b.step("run", "Run the server");
    run_step.dependOn(&run_cmd.step);

    // zig build test
    const tests = b.addTest(.{
        .root_module = b.createModule(.{
            .root_source_file = b.path("src/main.zig"),
            .target = target,
            .optimize = optimize,
            .imports = &.{
                .{ .name = "princ", .module = princ.module("princ") },
            },
        }),
    });
    const run_tests = b.addRunArtifact(tests);
    const test_step = b.step("test", "Run tests");
    test_step.dependOn(&run_tests.step);
}
```

The corresponding `build.zig.zon`:

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
