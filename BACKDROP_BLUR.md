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

## Maven publication

The fork builds both Android and iOS source, including the patched Android
Material 3 AAR. GitHub Actions publishes Compose and Material 3 artifacts to
GitHub Packages at `https://maven.pkg.github.com/shubham-jhajhria/compose-material3-backdrop-blur`.
The version is `9999.0.0-SNAPSHOT`. Run the `Publish backdrop blur Maven packages`
workflow manually or push to `feature/backdrop-blur` to publish automatically.
GitHub Packages requires a token with `read:packages` even for public Maven
artifacts. The workflow uses its own `GITHUB_TOKEN` for publishing.

For local development, the same artifacts can be built with:

```bash
./gradlew :mpp:publishComposeJbToMavenLocal \
  -Pcompose.platforms=ios,android \
  -Pjetbrains.publication.libraries=COMPOSE,COMPOSE_MATERIAL3 \
  --no-configuration-cache -Dorg.gradle.configureondemand=false
```
