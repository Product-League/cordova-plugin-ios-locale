# cordova-plugin-ios-locale

Sets `CFBundleDevelopmentRegion` and `CFBundleLocalizations` in the iOS `Info.plist` at build time, so the App Store correctly shows the app's language. Both keys must be set together — `CFBundleLocalizations` is what drives the App Store's "Language" field; `CFBundleDevelopmentRegion` alone is not enough.

This version (`1.0.0`) hardcodes the locale to `nl` (Dutch) directly in `plugin.xml`. It is not configurable per app — a configurable version was attempted but proved unreliable on this build pipeline, so this hardcoded version is the one currently in use.

## Usage in ODC

1. Reference the plugin in the Mobile Library's extensibility configuration. The source URL is exposed as a Library setting (`PluginGithubURL`), so it can be defined per environment/library instance instead of being hardcoded:
```json
{
  "plugin": {
    "url": "$extensibilitySettings.PluginGithubURL"
  }
}
```
Set the `PluginGithubURL` setting to:
```
https://github.com/hleite-productleague/cordova-plugin-ios-locale.git#1.0.0
```
2. No app-level configuration is needed — the locale is fixed to `nl` for every app that consumes this plugin version.

## Reusing this plugin for another language

Since the locale is hardcoded, reusing this for an app that needs a different language (e.g. French) currently means tagging a separate version of this repo with the target locale baked in (e.g. a `1.0.0-fr` tag with `fr` instead of `nl` in `plugin.xml`), and pointing that app's `PluginGithubURL` setting at that tag instead.

## Note

A configurable version (`2.x`, using a Cordova plugin preference so `pluginConfigurations.cordova.preferences` could set the locale per app) was built and worked once in testing, but could not be made to work reliably across builds despite extensive troubleshooting (naming, versioning, and source-mechanism changes all ruled out as the cause). This is being investigated further / raised with OutSystems Support. Until resolved, the hardcoded `1.0.0` approach above is the supported way to use this plugin.
