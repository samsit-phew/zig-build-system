# Common Errors

## no field named 'root_source_file' in struct 'Build.TestOptions'

You're using the old 0.13/0.14 API. In 0.16, `addTest` takes `.root_module`:

```zig
// old (broken in 0.16)
const tests = b.addTest(.{
    .root_source_file = b.path("src/main.zig"),
});

// correct
const tests = b.addTest(.{
    .root_module = b.createModule(.{
        .root_source_file = b.path("src/main.zig"),
        .target = target,
        .optimize = optimize,
    }),
});
```

---

## error: hash mismatch

You added a remote dependency without a hash. Run `zig build` — it will error and print the correct hash. Paste the `found:` value into your `.zon`.

---

## dependency 'X' not found

The name in `b.dependency("X", ...)` must exactly match the key in `.dependencies` in your `build.zig.zon`.

```zig
// build.zig.zon
.dependencies = .{
    .princ = .{ .path = "../princ" },  // key is "princ"
}

// build.zig
const dep = b.dependency("princ", .{});  // must match
```

---

## module 'X' not found

The name in `dep.module("X")` must match the name you passed to `b.addModule("X", ...)` in the dependency's `build.zig`.

```zig
// princ/build.zig
_ = b.addModule("princ", .{ ... });  // must match

// server/build.zig
princ_dep.module("princ")  // this name
```

---

## unused variable / unused import

Zig treats unused variables as errors. If you pulled in a dependency but aren't using it yet:

```zig
const dep = b.dependency("princ", .{ ... });
_ = dep;  // suppress error temporarily
```

---

## zig-out not updating

If your artifact isn't in `zig-out/`, make sure you called `b.installArtifact(exe)`. Without it, the default step doesn't know about your exe.
