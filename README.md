# Chooser

## Overview

File chooser plugin for Cordova.

Install with Cordova CLI:

	$ cordova plugin add cordova-plugin-chooser

Supported Platforms:

* Android (cordova-android ≥ 10; tested with cordova-android 15)

* iOS (cordova-ios ≥ 6; tested with cordova-ios 8 / Xcode 15+)

## Platform Compatibility

| Platform | Minimum | Tested |
|---|---|---|
| cordova-android | 10.0.0 | 15.x |
| cordova-ios | 6.0.0 | 8.x |

### iOS notes

* The plugin uses `UniformTypeIdentifiers` (`UTType`) APIs on **iOS 14+** and falls back to the legacy `MobileCoreServices` APIs on **iOS 13**, so iOS 13 (the minimum for cordova-ios 8) remains supported.
* On iOS 14+, `UIDocumentPickerViewController(forOpeningContentTypes:asCopy:)` is used; iOS 13 uses the older `(documentTypes:in:)` initializer.

### Android notes

* Uses `Intent.ACTION_GET_CONTENT` with `ContentResolver` — compatible with Android scoped storage (API 29+).
* The plugin saves/restores its pending-callback state via `onSaveInstanceState` / `onRestoreStateForActivityResult` so the picker result survives Activity recreation.

## API

	/**
	 * Displays native prompt for user to select a file.
	 *
	 * @param accept Optional MIME type filter (e.g. 'image/gif,video/*').
	 *
	 * @returns Promise containing selected file's raw binary data,
	 * base64-encoded data: URI, MIME type, display name, and original URI.
	 *
	 * If user cancels, promise will be resolved as undefined.
	 * If error occurs, promise will be rejected.
	 */
	chooser.getFile(accept?: string) : Promise<undefined|{
		data: Uint8Array;
		dataURI: string;
		mediaType: string;
		name: string;
		uri: string;
	}>
	
	/**
	 * Displays native prompt for user to select a file.
	 *
	 * @param accept Optional MIME type filter (e.g. 'image/gif,video/*').
	 *
	 * @returns Promise containing selected file's MIME type, display name,
	 * and original URI.
	 *
	 * If user cancels, promise will be resolved as undefined.
	 * If error occurs, promise will be rejected.
	 */
	chooser.getFileMetadata(accept?: string) : Promise<undefined|{
		mediaType: string;
		name: string;
		uri: string;
	}>

## Example Usage

	(async () => {
		const file = await chooser.getFile();
		console.log(file ? file.name : 'canceled');
	})();


## Platform-Specific Notes

The following must be added to config.xml to prevent crashing when selecting large files
on Android:

```
<platform name="android">
	<edit-config
		file="app/src/main/AndroidManifest.xml"
		mode="merge"
		target="/manifest/application"
	>
		<application android:largeHeap="true" />
	</edit-config>
</platform>
```

If it isn't present already, you'll also need the attribute `xmlns:android="http://schemas.android.com/apk/res/android"` added to your `<widget>` tag in order for that to build successfully.
