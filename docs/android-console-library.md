# Console library on Android

In Ozone, open **Import Content → Scan Console Folders**. Browse to the parent
directory of the system folders, then select **Scan Console Folders** again.
The importer walks the supported folders recursively and uses RetroArch's
standard console playlist names. It can use an installed database for game
names and adds unmatched games by filename. Scanning again merges entries by
content path.

Supported folder names are `nes`, `snes`, `gb`, `gbc`, `gba`, `n64`,
`nds`, `mastersystem`, `gamegear`, `megadrive`, `sega32x`, `segacd`,
`saturn`, `dreamcast`, `psx`, `psp`, `pcengine`, and `pcenginecd`.
Use these lowercase names. Keep disc descriptors and their tracks together.
An M3U should reference the disc images or descriptors by relative path.
For example:

```text
roms/
  snes/
    Homebrew.sfc
  psx/
    Adventure.m3u
    Adventure (Disc 1).chd
    Adventure (Disc 2).chd
```

Install cores and update **Core Info Files** in the Online Updater. The
importer sets a playlist's default core when one installed core claims that
system and the playlist has no explicit default. With multiple candidates,
choose a core in the playlist settings or on first launch.

Enable **Settings → User Interface → Appearance → Console Carousel**, then
open a console playlist. Switch playlists with L/R or tap the sides of the
header. Turn the option off to restore the standard Ozone header and L/R
navigation. Choose **Boxart** as the thumbnail type and enable **On-Demand
Thumbnail Downloads** for network lookup. RetroArch saves downloads in its
configured thumbnails directory. Games without matching artwork remain in
the list.

## Build debug APKs

Use the Gradle wrapper in `pkg/android/phoenix` with JDK 21, SDK 36, Build
Tools 36.0.0 and NDK 29.0.14206865. On Windows, run the following from the
repository root to bundle the Ozone resources:

```powershell
Set-Location pkg/android/phoenix
curl.exe -fL https://buildbot.libretro.com/assets/frontend/assets.zip -o assets.zip
New-Item -ItemType Directory -Force assets/assets | Out-Null
tar -xf assets.zip -C assets/assets ozone xmb/monochrome rgui COPYING
.\gradlew.bat :assembleAarch64Debug :assembleRa32Debug -PbundleConsoleAssets
```

The APKs are under `build/outputs/apk/aarch64/debug` and
`build/outputs/apk/ra32/debug`. The first includes `arm64-v8a` and `x86_64`.
The second includes `armeabi-v7a` and `x86`. The debug APKs use RetroArch's
existing package IDs and a local debug signature. Android requires matching
signatures to install over an existing copy of the same package.

For a connected Galaxy S21 or Philips OLED855, query the ABI of the installed
Android system before selecting an APK:

```text
adb devices -l
adb -s SERIAL shell getprop ro.product.cpu.abilist
```

Use `aarch64` if the device reports `arm64-v8a` and `ra32` if it only
reports `armeabi-v7a` among the supported ARM ABIs.

## Verify on a device

1. Install the APK, launch Ozone and grant access to the ROM folder. Verify
   that the app remains usable after restarting.
2. Import a test directory like the one above. Check that each system has one
   playlist and that the M3U produces one playable entry. Scan the same
   directory again and verify the entry counts.
3. Open both playlists with the carousel enabled. Check L/R with a controller
   and header taps on a touch device. Disable the carousel and verify standard
   Ozone navigation.
4. Check a title with a known cover, a title without artwork, and an offline
   revisit to the downloaded cover. With compatible cores and legal test ROMs,
   launch a game on each device and exercise disc switching.
