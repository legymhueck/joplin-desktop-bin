# Joplin desktop packaging notes (v3.6.16)

## Primary sources
- Upstream release assets: https://github.com/laurent22/joplin/releases/tag/v3.6.16
- Release metadata (`latest-linux.yml`): https://github.com/laurent22/joplin/releases/download/v3.6.16/latest-linux.yml
- AUR `joplin` PKGBUILD: https://aur.archlinux.org/cgit/aur.git/plain/PKGBUILD?h=joplin
- AUR `joplin-bin` PKGBUILD: https://aur.archlinux.org/cgit/aur.git/plain/PKGBUILD?h=joplin-bin
- AUR `joplin-appimage` PKGBUILD: https://aur.archlinux.org/cgit/aur.git/plain/PKGBUILD?h=joplin-appimage

## What the AUR packages are doing

### `joplin` / `joplin-desktop`
- Builds from the upstream source tarball for `v3.6.16`.
- Reuses Arch's system Electron (`electron42`) instead of shipping the bundled upstream Electron.
- Also builds the CLI package as part of a split package.
- This is the most Arch-native approach, but it is the most complex build: it patches the workspace tree, bootstraps Yarn/Corepack, and runs Electron builder.

### `joplin-bin`
- Repackages the official upstream `.deb` from the `v3.6.16` release.
- This is much simpler than the source build and uses an upstream-supported Linux artifact.
- The current AUR PKGBUILD is minimal: it mostly extracts `data.tar.xz` and adds `/usr/bin/joplin`.

### `joplin-appimage`
- Downloads the official AppImage, extracts metadata from it, and then installs the AppImage itself under `/opt/appimages/...`.
- This is the least attractive packaging route on Arch because it packages a package-like runtime artifact inside another package and adds AppImage/FUSE-specific handling.
- It is also currently behind the release (`3.6.15` vs `3.6.16`).

## What the upstream release offers
- `Joplin-3.6.16.deb` (105,163,460 bytes)
- `Joplin-3.6.16.AppImage` (159,878,096 bytes)
- The release metadata file lists both Linux artifacts and their SHA-512 values.

The `.deb` is the better binary source for an Arch package because:
1. it is smaller than the AppImage,
2. it already contains the desktop file and icon hierarchy,
3. it does not need `fuse2` or AppImage-specific desktop-integration workarounds,
4. it matches an upstream distro-package format rather than a self-mounting runtime image.

## Recommendation
- **Best overall Arch-style package:** the existing source-built `joplin` / `joplin-desktop` split package.
- **Best binary/repackaging method:** repackage the upstream `.deb`, not the AppImage.

## Improvements worth making to a `.deb`-based PKGBUILD
Compared with the current `joplin-bin` AUR package, a better PKGBUILD should:
- declare runtime dependencies explicitly,
- install the upstream AGPL license file,
- patch the desktop launcher to use an Arch-visible launcher path,
- remove `resources/app-update.yml` so package updates stay under pacman/AUR control,
- optionally expose `/usr/bin/joplin-desktop` instead of taking over `/usr/bin/joplin`.
