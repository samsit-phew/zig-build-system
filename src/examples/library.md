# Library Package

How to structure a package meant to be consumed by other Zig projects.

## build.zig

```zig
const std = @import("std");

pub fn build(b: *std.Build) void {
    const target = b.standardTargetOptions(.{});
    const optimize = b.standardOptimizeOption(.{});

    // expose as a module — this is how other projects import you
    _ = b.addModule("princ", .{
        .root_source_file = b.path("src/root.zig"),
        .target = target,
        .optimize = optimize,
    });

    // also build a static lib (only needed for C consumers)
    const lib = b.addLibrary(.{
        .name = "princ",
        .root_module = b.createModule(.{
            .root_source_file = b.path("src/root.zig"),
            .target = target,
            .optimize = optimize,
        }),
    });
    b.installArtifact(lib);

    // tests
    const tests = b.addTest(.{
        .root_module = b.createModule(.{
            .root_source_file = b.path("src/root.zig"),
            .target = target,
            .optimize = optimize,
        }),
    });
    const run_tests = b.addRunArtifact(tests);
    const test_step = b.step("test", "Run tests");
    test_step.dependOn(&run_tests.step);
}
```

## build.zig.zon

```zig
.{
    .name = .princ,
    .version = "0.1.0",
    .minimum_zig_version = "0.16.0",
    .fingerprint = 0x9605f9a135a6e962,
    .paths = .{""},   // include everything
}
```

## src/root.zig

This is the entry point of your library. Everything `pub` here is part of your API.

```zig
const std = @import("std");

pub const Color = enum { red, green, blue };

pub fn princ(color: Color, comptime fmt: []const u8, args: anytype) void {
    std.debug.print("{s}" ++ fmt ++ "\x1b[0m", .{colorCode(color)} ++ args);
}

fn colorCode(color: Color) []const u8 {
    return switch (color) {
        .red   => "\x1b[31m",
        .green => "\x1b[32m",
        .blue  => "\x1b[34m",
    };
}

test "colorCode is non-empty" {
    inline for (@typeInfo(Color).@"enum".fields) |field| {
        const c: Color = @enumFromInt(field.value);
        try std.testing.expect(colorCode(c).len > 0);
    }
}
```
