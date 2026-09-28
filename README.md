cordova-plugin-ios-locale
A minimal Cordova plugin that sets `CFBundleDevelopmentRegion` and `CFBundleLocalizations` in the iOS `Info.plist` at build time, using Cordova's native `<config-file>` mechanism.
Why this plugin exists
Setting `CFBundleDevelopmentRegion` (or the documented `defaultlocale` preference) directly through OutSystems ODC's extensibility configuration (`cordova.preferences`) does not reliably apply the value to the compiled app. This was confirmed across multiple builds by extracting and inspecting the compiled `Info.plist` from the resulting `.ipa` — the extensibility configuration itself was being read correctly (verified via a control setting), but these two plist keys were never updated.
Cordova preferences only take effect if they're either a "well-known" preference that `cordova-ios` reads natively, or a variable explicitly declared by an installed plugin via `<preference>` in its `plugin.xml`. `CFBundleDevelopmentRegion` and `CFBundleLocalizations` are neither, which is why the extensibility JSON approach silently did nothing. This plugin declares its own `<config-file>` entries, which is the standard, reliable way to edit arbitrary `Info.plist` keys in a Cordova/Capacitor iOS build.
Without `CFBundleLocalizations` set, the App Store's "Language" field on the app's product page falls back to whatever `CFBundleDevelopmentRegion` resolves to — so both keys need to be set together for the store listing to correctly reflect the intended language.
What it does
On iOS builds, it sets:
`CFBundleDevelopmentRegion` → value of the `DEVELOPMENT_REGION` preference (default `en`)
`CFBundleLocalizations` → `[value of DEVELOPMENT_REGION]`
Both are set with `overwrite="true"`, replacing the base template's hardcoded default.
Installation
Reference this plugin from a Mobile Library's extensibility configuration in ODC:
```json
{
  "buildConfigurations": {
    "cordova": {
      "source": {
        "npm": "https://github.com/hleite-productleague/cordova-plugin-ios-locale.git#2.0.0"
      }
    }
  }
}
```
Pin to a specific release tag rather than a branch, so builds stay stable until you deliberately bump the version.
Configuration
Set the target locale per app, in that app's own extensibility configuration:
```json
{
  "appConfigurations": {
    "cordova": {
      "preferences": {
        "DEVELOPMENT_REGION": "nl"
      }
    }
  }
}
```
Change `"nl"` to whichever locale that app needs (e.g. `"fr"`, `"de"`, `"en"`). If `DEVELOPMENT_REGION` isn't set, it defaults to `"en"`.
Verifying a build
The compiled value isn't visible in ODC build logs — the underlying Cordova hooks and `config-file` merges run silently. To confirm the fix applied:
Download the built `.ipa` (or simulator `.zip`).
Unzip it — `.ipa` files are plain zip archives (rename to `.zip` on Windows if needed to browse with File Explorer, or open directly with 7-Zip/WinRAR).
Locate `Payload/<AppName>.app/Info.plist`.
Read it with a plist-aware tool (it's usually binary format, not plain XML):
```bash
   python3 -c "import plistlib; d = plistlib.load(open('Info.plist','rb')); print(d.get('CFBundleDevelopmentRegion'), d.get('CFBundleLocalizations'))"
   ```
Confirm both keys match the value you set.
Notes
iOS only. Currently no Android equivalent is implemented.
Only one locale is set at a time (`CFBundleLocalizations` is a single-element array matching `DEVELOPMENT_REGION`). If an app needs to declare support for multiple languages, this plugin would need to be extended to accept a list.
Changing the live App Store "Language" field requires submitting and releasing a new app version — uploading the build to App Store Connect alone does not update the public listing.
Version history
2.0.0 — Made the target locale configurable via the `DEVELOPMENT_REGION` preference instead of being hardcoded.
1.0.0 — Initial release. Hardcoded `CFBundleDevelopmentRegion` and `CFBundleLocalizations` to `nl`.
