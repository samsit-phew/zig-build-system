# Artifacts

Artifacts are the things you build. There are three kinds.

## Executable

```zig
const exe = b.addExecutable(.{
    .name = "myapp",
    .root_module = b.createModule(.{
        .root_source_file = b.path("src/main.zig"),
        .target = target,
        .optimize = optimize,
    }),
});
```

Produces `zig-out/bin/myapp`.

## Static Library

```zig
const lib = b.addLibrary(.{
    .name = "mylib",
    .root_module = b.createModule(.{
        .root_source_file = b.path("src/root.zig"),
        .target = target,
        .optimize = optimize,
    }),
});
```

Produces `zig-out/lib/libmylib.a`. Useful when consuming from C. For pure Zig-to-Zig use a module instead.

## Module

```zig
const mod = b.addModule("mymod", .{
    .root_source_file = b.path("src/root.zig"),
    .target = target,
    .optimize = optimize,
});
```

No file is produced. This is how Zig packages expose themselves to other Zig projects — just a module other build scripts can import.

---

## Installing Artifacts

Calling `b.installArtifact(exe)` copies the built artifact into `zig-out/` and adds it to the default step, so `zig build` alone produces it.

```zig
b.installArtifact(exe);   // now zig build produces zig-out/bin/myapp
b.installArtifact(lib);   // now zig build produces zig-out/lib/libmylib.a
```
