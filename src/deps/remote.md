# Using a Remote Package

Remote packages are fetched from a URL (usually a GitHub tarball). Zig caches them in `~/.cache/zig`.

## Adding a remote dependency

The easiest way is `zig fetch`:

```bash
zig fetch --save https://github.com/zigzap/zap/archive/refs/tags/v0.9.0.tar.gz
```

This automatically updates your `build.zig.zon` with the url and hash:

```zig
.dependencies = .{
    .zap = .{
        .url = "https://github.com/zigzap/zap/archive/refs/tags/v0.9.0.tar.gz",
        .hash = "1220b3b4c7f2a...",
    },
},
```

## Using it in build.zig

Same as a local package — `b.dependency` by the name you gave it in `.zon`:

```zig
const zap = b.dependency("zap", .{
    .target = target,
    .optimize = optimize,
});

exe.root_module.addImport("zap", zap.module("zap"));
```

## The hash

The hash is a content hash of the downloaded archive. It ensures you always get exactly the same code, even if the URL changes. If you add a url without a hash, `zig build` will error and print the correct hash for you to paste in.

```
error: hash mismatch
expected: 1220...
found:    1220b3b4c7f2a...
```

Just paste the `found` value into your `.zon`.

## Pinning versions

Always pin to a specific tag or commit hash in the URL, not a branch like `main`:

```zig
// bad — mutable, changes over time
.url = "https://github.com/user/pkg/archive/refs/heads/main.tar.gz",

// good — immutable
.url = "https://github.com/user/pkg/archive/refs/tags/v1.2.3.tar.gz",
.url = "https://github.com/user/pkg/archive/abc123def456.tar.gz",
```
