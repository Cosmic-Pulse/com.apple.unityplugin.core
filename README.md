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
whose tree carries Core 3.2.0. The tag written here always comes from the built
`package.json`, so it matches the package regardless of which upstream tag produced it.

## Deviations from upstream

- **No macOS or visionOS slices by default.** The mirror is built for iOS, tvOS and their
  simulators — what partner games ship.
- **`Tests/` removed.** Upstream's test assembly references `com.unity.test-framework`,
  which a partner project need not have installed; without it the assembly fails to
  compile and takes the console down with it.
- **Deployment targets** may be raised to iOS/tvOS 15.0 and macOS 12.0 when the build
  Xcode refuses upstream's 13.0/10.15 floors. Unity 6000.5 defaults to 15.0, so this costs
  partner games nothing. Whether it was applied is recorded in each publish commit.

Every published commit records the upstream tag, the exact Xcode, the machine, and the
platform list, so any tag here can be traced back to how it was built. The workflow
refuses a beta Xcode: partners ship these binaries, and Apple can reject submissions
containing anything built with beta tooling.
