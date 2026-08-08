# com.apple.unityplugin.core — Cosmic Pulse mirror

Apple.Core, the foundation layer every Apple Unity plug-in depends on, built from
[`apple/unityplugins`](https://github.com/apple/unityplugins) and republished here so it
can be installed by UPM git URL. Apache 2.0 (`LICENSE.txt`); Apple is the author, Cosmic
Pulse only builds and hosts.

Apple publishes no GitHub releases and no registry listing for these plug-ins. The only
supported way to consume them is to clone the repo and run `build.py`, which compiles the
native libraries with Xcode. Partner games can't be asked to do that, so the build happens
once here and partners pin a tag.

Pulled in as a dependency of
[com.apple.unityplugin.gamekit](https://github.com/Cosmic-Pulse/com.apple.unityplugin.gamekit),
which the [cosmic-sdk](https://github.com/Cosmic-Pulse/cosmic-sdk) uses for Game Center.
UPM does not resolve git dependencies of git packages, so both are listed explicitly in
the SDK's manifest block. **Pin a tag; never track `main`.**

## Publishing a version

Run the **Mirror** workflow (Actions → Mirror → Run workflow). It runs on the self-hosted
macOS runners (Astra / Anubis), builds from the upstream tag you give it, commits the
package to `main`, and tags `v<version>`. Tags are written once and never moved — partners
pin them.

Apple does not tag Core separately, so the input is a plug-in tag such as `GameKit-4.0.1`,
whose tree carries Core 3.2.0. The tag written here comes from the built `package.json`.

Re-cutting a tag means deleting it and running the workflow again — deliberate, because
partners pin these. Once a tag is out in a partner's manifest, prefer `tag_suffix`
(`-cosmic.1`, `-cosmic.2`) so the old tag keeps resolving to what they already built
against.

## Deviations from upstream

- **iOS and iOS Simulator only.** macOS needs Apple's own signing team, and the runners
  carry no tvOS or visionOS platform component. No partner game targets any of them.
- **`Tests/` removed.** Upstream's test assembly references `com.unity.test-framework`,
  which a partner project need not have installed; without it the assembly fails to
  compile and takes the console down with it.
- **`AppleBuildProfileEditor` patched for Unity 6000.5.** Upstream calls
  `AssetDatabase.GetAssetPath(obj.GetInstanceID())`, and Unity 6000.5 marks both halves
  obsolete *as an error* — so `Apple.Core.Editor` does not compile, and it takes
  `Apple.GameKit.Editor` with it. That is the assembly which writes the
  `com.apple.developer.game-center` entitlement into the Xcode project, so without this
  the partner gets a console full of errors and silently no Game Center. The mirror uses
  the `GetAssetPath(Object)` overload instead: same call, no obsolete round trip, valid on
  every Unity version we support. Upstream `main` still has the original.
- **Deployment targets** may be raised to iOS/tvOS 15.0 and macOS 12.0 when the build
  Xcode refuses upstream's 13.0/10.15 floors. Unity 6000.5 defaults to 15.0, so this costs
  partner games nothing. Whether it was applied is recorded in each publish commit.

Every published commit records the upstream tag, the exact Xcode, the machine, and the
platform list, so any tag here can be traced back to how it was built. That is not
decoration: the runners carry different Xcode versions (26.1.1 and 26.6 as of the first
builds), and rebuilding the same source on the other machine changes every native binary
in the package. The trailer is how you tell which compiler produced a given tag.

The workflow refuses a beta Xcode: partners ship these binaries, and Apple can reject
submissions containing anything built with beta tooling.
