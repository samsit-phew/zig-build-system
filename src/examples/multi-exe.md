# Multiple Executables

Building a server and client from the same `build.zig`, sharing a common module.

```zig
const std = @import("std");

pub fn build(b: *std.Build) void {
    const target = b.standardTargetOptions(.{});
    const optimize = b.standardOptimizeOption(.{});

    const princ = b.dependency("princ", .{
        .target = target,
        .optimize = optimize,
    });

    // shared imports list
    const imports: []const std.Build.Module.Import = &.{
        .{ .name = "princ", .module = princ.module("princ") },
    };

    // server exe
    const server = b.addExecutable(.{
        .name = "server",
        .root_module = b.createModule(.{
            .root_source_file = b.path("src/server.zig"),
            .target = target,
            .optimize = optimize,
            .imports = imports,
        }),
    });
    b.installArtifact(server);

    // client exe
    const client = b.addExecutable(.{
        .name = "client",
        .root_module = b.createModule(.{
            .root_source_file = b.path("src/client.zig"),
            .target = target,
            .optimize = optimize,
            .imports = imports,
        }),
    });
    b.installArtifact(client);

    // zig build server
    const run_server = b.addRunArtifact(server);
    const server_step = b.step("server", "Run the server");
    server_step.dependOn(&run_server.step);

    // zig build client
    const run_client = b.addRunArtifact(client);
    const client_step = b.step("client", "Run the client");
    client_step.dependOn(&run_client.step);

    // zig build test — runs tests from both files
    const test_step = b.step("test", "Run all tests");

    for (&[_][]const u8{ "src/server.zig", "src/client.zig" }) |src| {
        const tests = b.addTest(.{
            .root_module = b.createModule(.{
                .root_source_file = b.path(src),
                .target = target,
                .optimize = optimize,
                .imports = imports,
            }),
        });
        test_step.dependOn(&b.addRunArtifact(tests).step);
    }
}
```

Now:
```bash
zig build server   # run server
zig build client   # run client
zig build          # build both into zig-out/bin/
zig build test     # test both
```
