# Upstream V7.5 archive audit

## Artifact identity

- Archive: `HyperOS_LauncherV7.5F@kashis_cringey_stuffs (2).zip`
- Size: 29,313,954 bytes
- SHA-256: `62135159b073926429331f202e07ff14b3b03fdc60b3285e05b4972a420c9d2b`
- Expected upstream release asset: HyperOS Launcher Mod V7.5

## Relevant archive contents

- `files/MiuiHome.apk` — 26,774,943 bytes
- `files/ext/XiaomiEUExt.apk` — 2,855,336 bytes
- `system/product/overlay/MiuiHomeLauncherResOverlay.apk`
- `system/etc/permissions/privapp_whitelist_com.miui.home.xml`
- `system/etc/permissions/privapp_whitelist_eu.xiaomi.ext.xml`
- `META-INF/com/google/android/magisk/customize.sh`
- `customize.sh` — delegates to the bundled DI installer

## Installer behavior relevant to POCO

The upstream installer:

1. Checks Android release and aborts below Android 15.
2. Warns that APK signature verification must be disabled for stock ROM and POCO.
3. Checks for `eu.xiaomi.ext`; if absent, stages the included Xiaomi EU extension APK under `/product/priv-app/XiaomiEuExt/`.
4. Detects POCO Launcher by package name `com.mi.android.globallauncher`.
5. Runs `pm uninstall-system-updates` against the POCO package.
6. Copies the prebuilt `MiuiHome.apk` to the discovered POCO APK path (or fallback `/product/priv-app/PocoHome/PocoHome.apk`).
7. Attempts `pm install -r` on the replacement APK.
8. Requires `/system_ext/etc/init/init.miui.ext.rc`, copies it into the module and replaces occurrences of `com.mi.android.globallauncher` with `com.miui.home`.
9. Deletes files with `home` in their names under `/data/system/package_cache`.

## Risks / unresolved checks

- The ZIP is a prebuilt binary distribution, not source code. Feature-level porting into the separately supplied POCO Launcher APK cannot be assumed.
- The installer is built around a custom DI recovery/module installer flow. KernelSU module installation compatibility must be verified independently; presence of `module.prop` does not prove the custom installer flow is safe in KernelSU.
- The installer's POCO branch performs package-manager operations and edits init configuration. These are not yet validated against the exact POCO X7 Pro HyperOS 3 / Android 16 build.
- The upstream warning about disabling signature verification is a security-sensitive prerequisite. This project will not silently enable or instruct a global bypass.
- Exact live paths, APK package/signing identity, privapp permissions, overlay compatibility, init service expectations, and SELinux contexts remain to be checked on the target build.

## Decision

Do not install the upstream archive unchanged. Do not publish a KernelSU flashable ZIP until the above device-specific checks and a reliable rollback path are complete.
