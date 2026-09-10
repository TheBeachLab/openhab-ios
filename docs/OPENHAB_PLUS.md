# openHAB Plus internal builds

This fork uses Apple team `MX9PJX2XM5`, main bundle ID
`org.beachlab.openhab`, and shared container `group.org.beachlab.openhab`.
The companion identifiers retain the upstream suffixes: `.openHABWidget`,
`.NotificationService`, `.watchkitapp`, and
`.watchkitapp.OpenHABWatchComplications`.

Verified on 2026-09-10 against the Apple Developer identifiers portal and
[the App Store Connect record](https://appstoreconnect.apple.com/apps/6810768743/distribution):
Apple app ID `6810768743`, name `openHAB Plus`, SKU `beachlab-openhab-plus`.
App Groups is assigned to the app, widget, notifications, and Watch app.

The initial release train is `1.0.0`, starting with build `1`. Increment
`CURRENT_PROJECT_VERSION` in `Version.xcconfig` before subsequent uploads.

## Signing and validation

Use Xcode 26.6 with the watchOS 26.5 platform installed. The archive contains
an embedded Watch app, so Xcode requires the watchOS platform even for iOS.
Debug uses automatic signing; Release uses the five named `openHAB Plus App Store`
profiles and the existing Apple Distribution certificate for this team. Keep API
keys and generated profiles outside Git. Use the existing distribution
certificate; do not revoke or replace certificates used by other apps.

Resolve packages once before starting an archive. Do not run two package
resolution operations against the same checkout directory concurrently.
Set `CI=1` for command-line builds to skip the upstream source-formatting
build phase. Validate simulator startup with the newly built app before
uploading. Example archive command (paths and API credentials supplied by
the caller):

```sh
CI=1 xcodebuild archive -workspace openHAB.xcworkspace -scheme openHAB \
  -configuration Release -destination 'generic/platform=iOS' \
  -archivePath "$ARCHIVE_PATH" -derivedDataPath "$DERIVED_DATA_PATH" \
  -allowProvisioningUpdates -authenticationKeyPath "$ASC_KEY_PATH" \
  -authenticationKeyID "$ASC_KEY_ID" -authenticationKeyIssuerID "$ASC_ISSUER_ID"
```

Export using `method=app-store-connect`, team `MX9PJX2XM5`, and the app's
App Store provisioning profiles. Upload only to internal TestFlight and
assign the build to `Francisco Internal`. Do not invoke upstream `fastlane
beta`: it uses upstream match profiles and additional release actions.
The account holder is the intended tester; do not add other account users.

## Initial build limitations

- CarPlay entitlement is excluded until this team has its own approval.
- The upstream Firebase configuration does not match this bundle ID. Startup
  skips Firebase in this case, and crash-report calls are guarded. Firebase
  notifications and crash reporting require a separate project configuration.
- No interface redesign is included in this signing/bootstrap build.
- Apple displayed an updated developer agreement pending acceptance before
  submission. Check its current status in the account portal before release.
