# Steps

Steps are the runnable nodes in your build graph. You create named steps with `b.step("name", "description")` and wire things to them with `dependOn`.

## Run step

```zig
const run_cmd = b.addRunArtifact(exe);
const run_step = b.step("run", "Run the app");
run_step.dependOn(&run_cmd.step);
```

Now `zig build run` builds and runs your exe.

You can pass args through from the command line:

```zig
if (b.args) |args| {
    run_cmd.addArgs(args);
}
```

Then: `zig build run -- arg1 arg2`

## Test step

```zig
const tests = b.addTest(.{
    .root_module = b.createModule(.{
        .root_source_file = b.path("src/main.zig"),
        .target = target,
        .optimize = optimize,
    }),
});

const run_tests = b.addRunArtifact(tests);
const test_step = b.step("test", "Run tests");
test_step.dependOn(&run_tests.step);
```

Now `zig build test` runs all `test` blocks in `src/main.zig`.

## Custom steps

You can chain any steps together:

```zig
const fmt_step = b.step("fmt", "Format source");
const fmt_cmd = b.addSystemCommand(&.{ "zig", "fmt", "src/" });
fmt_step.dependOn(&fmt_cmd.step);
```

Now `zig build fmt` runs `zig fmt src/`.

## dependOn

`dependOn` is how you express ordering. If step B depends on step A, A runs first.

```zig
test_step.dependOn(&run_tests.step);   // test_step requires run_tests to finish
run_step.dependOn(&run_cmd.step);      // run_step requires run_cmd to finish
```

You can depend on multiple things:

```zig
deploy_step.dependOn(&build_step.step);
deploy_step.dependOn(&test_step.step);  // must pass tests before deploying
```
