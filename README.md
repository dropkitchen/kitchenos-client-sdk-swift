# KitchenOS Client SDK — Swift Package

The KitchenOS SDK for iOS, distributed as a binary Swift package. Appliance connectivity,
cooking sessions, authentication and device events for the KitchenOS platform.

This repository contains **no source code and no binaries in git** — only the package manifest.
Each release's `KitchenOS.xcframework.zip` (~22 MB) is attached as a GitHub Release asset and
downloaded by Swift Package Manager on resolution. Cloning this repository costs a few tens of
kilobytes rather than the 1.7 GB a binary-in-git repository would.

The same SDK is published for Android and Kotlin Multiplatform on
[Maven Central](https://central.sonatype.com/artifact/com.adaptics.kitchenos/kos-core).

## Installation

### Xcode

**File → Add Package Dependencies…**, then enter:

```
https://github.com/dropkitchen/kitchenos-client-sdk-swift
```

### Package.swift

```swift
dependencies: [
    .package(
        url: "https://github.com/dropkitchen/kitchenos-client-sdk-swift",
        .upToNextMinor(from: "1.59.4")
    )
],
targets: [
    .target(
        name: "YourTarget",
        dependencies: [
            .product(name: "KitchenOS", package: "kitchenos-client-sdk-swift")
        ]
    )
]
```

No credentials, SSH key or GitHub account are required — the manifest is public and the release
asset downloads anonymously over HTTPS.

## Version policy

Releases are **immutable**. A published tag's manifest, asset and checksum never change; a defect
is fixed by publishing a new patch version. This is what makes a pinned checksum a durable
guarantee rather than a snapshot.

**Depend on a version, never on a branch.** `branch: "main"` is not supported: `main` can briefly
reference an asset that is not yet published, and a branch dependency discards the checksum pinning
that makes binary distribution trustworthy.

Applications should pin exactly (`exact: "1.59.4"`) for reproducible builds. Libraries that
redistribute the SDK should declare a range (`.upToNextMinor(from:)`) so a host application can
satisfy both its own pin and yours.

## Supported platforms

| | |
|---|---|
| Minimum iOS | 15.0 |
| Device | `ios-arm64` |
| Simulator | `ios-arm64_x86_64-simulator` (Apple Silicon and Intel) |
| Binary form | dynamic framework (dylib) |
| Debug symbols | dSYMs for both slices are included in the artifact |
| Not supported | macOS, watchOS, tvOS, Mac Catalyst, visionOS |

Because dSYMs ship inside the xcframework and are declared via `DebugSymbolsPath`, Xcode copies
them into your app archive automatically. KitchenOS stack frames therefore symbolicate in your
crash reporter (Crashlytics, Datadog, Sentry) with no extra build steps or configuration.

## Configuration

The SDK ships with **no endpoints, client identifiers or credentials baked in**. The host
application supplies all configuration at launch, before any other SDK call:

```swift
import KitchenOS

KitchenOS.setConfiguration(
    clientId: "<your client id>",
    environment: "production",        // dev | stage | production
    region: "<aws region>",
    oauthRedirectUri: "<your-app-scheme>://auth"
)
```

Contact Fresco for the client identifier and configuration values for your integration.

## Artifact integrity

Every release declares the artifact's SHA-256 in `Package.swift`. Swift Package Manager verifies
the download against it and fails resolution loudly on any mismatch, so a corrupted or substituted
asset cannot be silently consumed. Each release also carries a `KitchenOS.xcframework.zip.sha256`
asset for manual verification:

```sh
shasum -a 256 KitchenOS.xcframework.zip
```

## Licence

Distributed under the [Fresco SDK License](LICENSE) — use and redistribute the unmodified binary as
part of your own application. Third-party components and their licences are listed in
[THIRD-PARTY-LICENSES.md](THIRD-PARTY-LICENSES.md).
