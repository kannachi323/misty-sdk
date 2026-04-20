# Misty SDK

The official plugin SDK for [Misty](https://mistysys.com) — a desktop file manager built for speed and extensibility.

Misty is closed source. This repository hosts the public plugin SDK, prebuilt release binaries, and example plugin source.

---

## Downloads

Grab the latest release for your platform from the [Releases](../../releases) page.

| Platform | Architecture |
|----------|-------------|
| macOS    | Apple Silicon, x86-64 |
| Windows  | x86-64 |
| Linux    | x86-64 |

---

## Plugin SDK

Misty plugins are native shared libraries (`.dylib` / `.dll` / `.so`) loaded at runtime over a stable C ABI.

### What's in this repo

| Path | Contents |
|------|----------|
| `include/misty_plugin_c_api.h` | Raw C ABI — the stable boundary between Misty and plugins |
| `include/misty_plugin_sdk.h` | C++ convenience wrapper (header-only, compiled into the plugin) |
| `plugins/preview_manager/` | Bundled sample plugin with full source |
| `vendor/` | Vendored third-party headers used by the sample plugin |

### Versioning

| Thing | Current value |
|-------|--------------|
| Misty version | 1.0 |
| Plugin ABI version (`MISTY_PLUGIN_ABI_VERSION`) | 3 |
| Manifest `schema_version` | 2 |
| SDK version | 1.0 |

Misty checks `abi_version` in the plugin manifest at load time. A plugin built against ABI 3 will not load in a host that expects a different version.

### Plugin manifest

Every plugin ships a `manifest.json` alongside its library:

```json
{
  "schema_version": 2,
  "id": "com.yourname.myplugin",
  "name": "My Plugin",
  "version": "1.0.0",
  "author": "Your Name",
  "description": "What this plugin does.",
  "enabled": true,
  "platforms": ["macos"],
  "plugin": {
    "abi_version": 3,
    "variants": [
      {
        "os": "macos",
        "arch": "arm64",
        "runtime": "release",
        "library": "libmyplugin.dylib",
        "sdk_version": "1.0",
        "build_id": "1"
      }
    ]
  }
}
```

### Plugin discovery

Misty loads plugins from two locations:

- **Bundled:** `<install>/plugins/`
- **User:** `~/.misty/plugins/`

Each plugin lives in its own subdirectory containing `manifest.json` and the compiled library.

### Quick start

```cpp
#define MISTY_PLUGIN_BUILD 1
#include "misty_plugin_sdk.h"

extern "C" MISTY_PLUGIN_EXPORT
void misty_plugin_invoke(const MistyInvokeContext* ctx) {
    misty::Host host(ctx);
    host.notify_info("Hello", "Plugin loaded!");
}
```

See `plugins/preview_manager/` for a complete, buildable example.

---

## More

- **Website:** [mistysys.com](https://mistysys.com)
- **Issues / feature requests:** open a GitHub issue in this repository
