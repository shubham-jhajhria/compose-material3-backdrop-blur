# Backdrop blur

This fork adds an opt-in `blurBehindRadius: Dp` to Material 3 `ModalBottomSheet` and
the Compose Multiplatform `DialogProperties` implementation used on iOS.
The default is `Dp.Unspecified`, so existing call sites retain their appearance.
Callers may pass an animated `Dp` value to change the radius over time.

```kotlin
ModalBottomSheet(
    onDismissRequest = { open = false },
    blurBehindRadius = 20.dp,
) { /* content */ }
```

```kotlin
// In androidMain or iosMain:
Dialog(
    onDismissRequest = { open = false },
    properties = DialogProperties(blurBehindRadius = 20.dp),
) { /* content */ }
```

The common `DialogProperties` expect constructor does not expose platform-specific
blur options. For dialogs declared in `commonMain`, use an `expect`/`actual`
factory for the properties on Android and iOS.

On Android, window blur is used on API 31 and newer. Earlier versions keep the
normal sheet scrim without blur. The scrim also remains visible if Android
disables cross-window blur at runtime. Android Compose UI already provides
`DialogProperties.blurBehindRadius`; this fork adds the matching iOS behavior.

On iOS, the dialog layer applies a Skia backdrop filter before drawing the
scrim and dialog content. This blurs previously drawn Compose content in the
same Metal canvas. UIKit views rendered separately are not included.

This branch is source code, not a published library release. The JetBrains
build redirects Material 3's Android target to upstream AndroidX, so the
Android source change must also be built and published from AndroidX. Apps
need compatible patched AndroidX and Compose Multiplatform artifacts before
using this API on both platforms.
