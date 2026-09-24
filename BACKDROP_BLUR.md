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
Dialog(
    onDismissRequest = { open = false },
    properties = DialogProperties(blurBehindRadius = 20.dp),
) { /* content */ }
```

On Android, window blur is used on API 31 and newer. Earlier versions keep the
normal sheet scrim without blur. The scrim also remains visible if Android
disables cross-window blur at runtime. Android Compose UI already provides
`DialogProperties.blurBehindRadius`; this fork adds the matching iOS behavior.

On iOS, the dialog layer applies a Skia backdrop filter before drawing the
scrim and dialog content. This blurs previously drawn Compose content in the
same Metal canvas. UIKit views rendered separately are not included.

The fork builds Material 3's Android variant locally so the new sheet API is
present in both Android and iOS artifacts. This is a source fork of the
current `jb-main` tree; consuming apps need a compatible artifact release.
