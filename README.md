# cordova-plugin-ios-locale

Sets `CFBundleDevelopmentRegion` and `CFBundleLocalizations` in the iOS `Info.plist` at build time, so the App Store correctly shows the app's language. Both keys must be set together — `CFBundleLocalizations` is what drives the App Store's "Language" field; `CFBundleDevelopmentRegion` alone is not enough.

The locale is configurable via a `DEVELOPMENT_REGION` variable (default `en`), so the same plugin/library can be reused across apps that need different languages.

## Usage in ODC

1. Reference the plugin in the Mobile Library's extensibility configuration:
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

2. In each **app's** extensibility configuration, set the desired locale:
```json
{
  "pluginConfigurations": {
    "cordova": {
      "preferences": {
        "DEVELOPMENT_REGION": "nl"
      }
    }
  }
}
```

Change `"nl"` per app (e.g. `"fr"`, `"de"`). If not set, it defaults to `"en"`.
