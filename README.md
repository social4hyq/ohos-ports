# @ohos-ports

OpenHarmony (OHOS) native-binary ports for npm packages whose upstream has no
official OHOS support and no fork relationship worth maintaining (unlike
`bun-pty`, `lightningcss`, and `tailwindcss-oxide`, which are forked and
cross-compiled in their own `ohos-*` repos).

Each package here reuses an existing, unmodified upstream prebuilt binary
(verified OHOS-ABI-compatible) with only the JS platform-detection loader
patched to recognize `process.platform === "openharmony"`.

## Packages

| Package | Reuses | Notes |
|---|---|---|
| `packages/opentui-core` | `@opentui/core-linux-arm64-musl@0.4.5`'s `libopentui.so` (Zig, dlopen via `bun:ffi`) | Verified: dlopen + FFI call succeeds on real OHOS aarch64 hardware |

## Why bundle instead of depend on the real platform package?

The upstream platform packages (e.g. `@opentui/core-linux-arm64-musl`)
declare `"os": ["linux"]` in their own `package.json`. OHOS reports
`process.platform === "openharmony"`, not `"linux"`, so npm/bun's platform
gate refuses to install them on an OHOS host even as a plain dependency.
The only way to get the binary onto disk on OHOS is to bundle it directly
inside a package with no such gate.

## Signing

Native binaries are stripped (`llvm-strip`) and self-signed with
[`ohos-bst-light`](https://github.com/hqzing/ohos-bst-light)'s
`self-sign.py` (vendored in `scripts/ohos/`) before being committed, so
they carry a valid OHOS `.codesign` section out of the box.
