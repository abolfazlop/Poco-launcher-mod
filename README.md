# POCO Launcher Mod

A device-targeted project to evaluate and adapt selected HyperOS Launcher Mod V7.5 features for the stock POCO Launcher on:

- Device: POCO X7 Pro
- ROM: Xiaomi HyperOS
- Android: 16
- Root manager: KernelSU
- Metamodule: meta-overlayfs

## Current status

**Audit / compatibility work only. No installable module has been released.**

The supplied HyperOS Launcher Mod V7.5 archive was verified against the published release SHA-256:

`62135159b073926429331f202e07ff14b3b03fdc60b3285e05b4972a420c9d2b`

The archive contains a prebuilt `MiuiHome.apk`, Xiaomi EU extension APK, overlays, privileged-permission XML files, and an installer. It is not launcher source code.

The supplied stock POCO Launcher APK is version `6.01.05.2239-03141600`.

## Important compatibility findings

The V7.5 installer has a POCO-specific branch. It copies the prebuilt `MiuiHome.apk` into the POCO launcher location and edits `/system_ext/etc/init/init.miui.ext.rc`. It also attempts package-manager installation and removes package-cache files. The upstream installer explicitly requires signature-verification bypass on stock ROM / POCO devices.

These operations are device- and build-dependent. They must not be blindly installed. In particular, disabling APK signature verification globally weakens Android security, and replacing the launcher APK can cause launcher crashes or leave the home screen unavailable.

KernelSU's `meta-overlayfs` provides systemless mounting for module `system/` files, but it does not itself guarantee that the replacement APK, init rules, permissions, overlays, or Android 16 framework dependencies are compatible.

## Safety policy

- Do not flash the upstream Magisk ZIP unchanged.
- Do not disable signature verification as a casual troubleshooting step.
- Do not ship a module until target paths, package identity, signatures, permissions, SELinux labels, and boot recovery are validated.
- Keep the stock launcher APK and a tested disable/rollback path.
- Test on the exact device build before publishing a release.

## Planned work

1. Inspect the stock POCO APK and the V7.5 launcher APK (manifest, package identity, signatures, resources, DEX and native libraries).
2. Compare framework and device dependencies for HyperOS / Android 16.
3. Build a KernelSU-compatible systemless package only after confirming the correct live launcher path and required init changes.
4. Add validation and rollback scripts.
5. Test installation, boot, launcher startup, widgets, folders, recents, gestures, and recovery before tagging a release.

## Credits and upstream

- HyperOS Launcher Mod V7.5 by its original authors: https://github.com/Mods-Center/HyperOS-Launcher
- KernelSU: https://github.com/tiann/KernelSU
- meta-overlayfs: https://github.com/KernelSU-Modules-Repo/meta-overlayfs

This project is an independent compatibility effort and is not affiliated with Xiaomi or the upstream mod authors.
