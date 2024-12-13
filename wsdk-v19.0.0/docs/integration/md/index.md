# Web Journey Capture Service (WJCS) Integration/Quick Start Guide

## What This SDK Offers

The WJCS SDK offers various tools which you can use to help verify and validate the identity of your customers. The SDK will help you to capture the following:

* Front side of identity documents
* Rear side of identity documents
* Proof of address documents
* Liveness of customer
* Face comparison using liveness data and documents

It will attempt to obtain the above using the following techniques:

* Manual file upload
* Drag and drop file upload
* Manual camera upload
* Smart capture upload
* Video streaming
* Remote scanner capture
* Manual image cropping
* Automatic image cropping

## Getting Started

### How to reference the SDK

To reference the SDK, you first need to decide how you're planning on working
with it. We support two main methods in this documentation, but there are many
other ways which will allow it work as intended which we will cover in the
appendix.

#### Vanilla JavaScript

If you're loading the library in a vanilla javascript environment with no
loaders, simply add the `idscan-jcs.min.js` file in a script tag to the head of
your web page with the `async` and `defer` tags as required.

When the script has been loaded, you can reference the main classes on the
window in the namespace `GBG.Idscan`. All enumerables, types and classes are
available here for use.

#### AMD Modules with Javascript/Typescript

If you're loading the library in an environment where you can use AMD module
imports, such as when you're using WebPack, you can simply import the sdk files
as any other module. For example, to import the commonly initialisation class,
you would do the following:

```javascript
	import { JourneyContainer } from 'idscan-jcs';
```

#### Mediated Access in a Web View

It is suggested that if you are using the SDK in a web view inside your own
native application that you follow the Vanilla JavaScript guide to begin with,
then expose the `window.GBG.Idscan` namespace and the `JourneyContainer` events
to your native application by following the below guides:

* [Android](https://developer.android.com/guide/webapps/webview)
* [iOS](https://developer.apple.com/library/archive/documentation/AppleApplications/Conceptual/SafariJSProgTopics/index.html)

This will allow your native application to know the current state of the
journey in the Web View and change what is displayed to the user accordingly.

#### Filenames

You may need to reference the scripts with their hashes on the end of their
filenames unless you rename the files first. This is not required, but it will
aid in cache busting if you do not have your own internal solution.

#### External Files

The IDES solution requires 3 external scripts to function correctly. These are
provided alongside the main library file. You can rename the
`idscan-jcs.[hash].js`, `ides-micro[hash].js` and `idesmicro_asm.js` files if
desired, but the other file, `idesmicro_asm.wasm`, must remain with the same
name in all circumstances. If you change any of the filenames, be sure to update
your script tags, imports and the options when initialising the
`JourneyContainer` class. This is detailed below.

### How to Initialise the SDK

To initialise a new instance of the WJCS SDK, an instance of the
`JourneyContainer` class must be created first, then initialise function should
be called on the instance to begin the process.

 **_Javascript Implementation:_**
```javascript
initialiseWJCSSDK() {
	this.container = new JourneyContainer({
		backendUrl: 'https://poc.idscan.cloud:443',
		container: '#YourContainerId',
		token: YOUR_API_TOKEN,
		smartCapture: {
			workerScriptUrl: "./ides-micro.[hash].js",
			asmScriptUrl: "./idesmicro_asm.js",
			autoStart:false
		}
	});

	this.container.initialize();
}
```

**_Typescript Implementation:_**
```typescript
initialiseWJCSSDK(): void {
	this.container = new JourneyContainer({
		backendUrl: 'https://poc.idscan.cloud:443',
		container: '#YourContainerId',
		token: YOUR_API_TOKEN,
		smartCapture: {
			workerScriptUrl: "./ides-micro.[hash].js",
			asmScriptUrl: "./idesmicro_asm.js",
			autoStart:false
		}
	});

	this.container.initialize();
}
```

The settings passed in above are the minimum required to initialise the SDK
without any errors being returned. For more detailed guides on which other
options are available for use, please refer to the `JourneyContainer` class
documentation provided alongside this document.

## How to Terminate Journeys Prematurely

In the event you need to terminate the current journey and release the camera
before the user has completed it, you can use the `terminate` function on the
instance of the SDK created in the previous step.

 **_Javascript Implementation:_**
```javascript
terminateWJCSSDKJourney() {
	this.journeyContainer.terminate();
}
```

**_Typescript Implementation:_**
```typescript
terminateWJCSSDKJourney(): void {
	this.journeyContainer.terminate();
}
```

This will terminate the journey and all information stored will be deleted. The
class instance will remain, but it will require reinitialising or disposing of
in your own solution's code.

## Additional Configuration

It's possible to configure many different aspects of the WJCS process by
specifying configuration options when initialising the SDK. For detailed
information about each different configuration, please consult the functional
documentation.

Some examples are detailed below along with use cases;

### Overriding Allowed Input Providers

In some occasions, you may wish to override the input providers specified by the
services, for instance you may want to force users down the file upload path
if you detect they are on a device which you don't want to offer a camera on.

By passing an array of `InputProvider`s into the configuration under the
`allowedInputProviders` option which will be used to filter out the ones
presented by the services.
### Disable file upload option for camera only
In some occasions, you may wish to control whether you want a file upload option
on a camera journey.

By passing a boolean value of `fileUploadOnCameraCaptureJourneys` into the configuration.
the default value is `true`

### Specifying the Default Input Provider

In some occasions, you may wish to change which provider is offered initially,
for instance on certain devices you may wish to offer file uploading by default
rather than camera capture. You could, for example, detect if a user is on a
portable device such as a phone, or a static device such as a desktop computer,
and guide them down a certain path by specifying the provider you want them to
use.

**NOTE:** There is advanced camera capability detection build into WJCS to
establish whether the device has cameras which are available and suitable. It is
advised that you leverage this functionality rather than override it. This
option is provided for completeness rather than a requirement.

By passing an `InputProvider` into the configuration under the
`defaultInputProvider` option will change which provider is initially offered
assuming it is available and enabled.

**NOTE:** Passing either `CAMERA` or `SMART_CAPTURE` will result in the "best"
available of the two to be specified to be used. If you want to use the `CAMERA`
provider explicitly, you should disable the `SMART_CAPTURE` one using the
`allowedInputProviders` options detailed above.


## Enabling to see processes logs in console
In case that it's required to get the exact WSDK steps execution time, You could activate the verbose logs either for 
smart capture or general timing logs by set in the configuration :
`timeLogsEnabled` inside `smartCapture` settings and make it true in case you would activate smart capture logs, 
`isVerboseLogEnabled` in the root configuration by making it true, you will activate the general time logging.

## Enabling Automatic Start of Camera Capture

If you want the camera capture process to start automatically without waiting for a user's click event, you can utilize the `autoStart` setting. You simply need to set `autoStart` inside the `smartCapture` configuration to `true`. By default, it's set to `false`, which means the camera capture process will wait for a user's click event to start. Utilizing the `autoStart` configuration can enhance the user experience by providing a seamless, automated capture process.

## Handling State Changes and Events

The WJCS SDK will emit many different types of events throughout its life cycle
which you, as a developer, can use to assist you in making a helpful and
engaging experience for your users.

For more information about the different events which are emitted and the
additional meta data emitted with each one, please refer to the `JourneyEvent`
documentation.

To utilize the events, begin by defining a function which will accept `event`,
`meta` and `state` as parameters, and pass it into the constructor for the
`JourneyContainer` as the `onJourneyEvent` parameter. Each time any event is
triggered, this function will be called.

Alongside the name of the event which was triggered, there will also be
additional meta data concerning the event, and an object dictating the current
internal state of the journey which will include information captured by the
system about the current user.

The snippet below shows how developers can hook into events to display loading
spinners for certain events.

**_Javascript Implementation:_**
```javascript
onJourneyEvent = (event, meta, state) => {
	const { name } = meta;

	switch (event) {
		case JourneyEvent.TRANSFER_STARTED:
		case JourneyEvent.TRANSFER_PROGRESS:
			this.isLoading(true, name);
		case JourneyEvent.TRANSFER_COMPLETE:
		case JourneyEvent.TRANSFER_FAILED:
			this.isLoading(false, name);
	}
}
```

**_Typescript Implementation:_**
```typescript
private onJourneyEvent = (event: JourneyEvent, meta: IJourneyEventMetaData, state: IJourneyState) => {
	const { name } = meta;

	switch (event) {
		case JourneyEvent.TRANSFER_STARTED:
		case JourneyEvent.TRANSFER_PROGRESS:
			this.isLoading(true, name);
		case JourneyEvent.TRANSFER_COMPLETE:
		case JourneyEvent.TRANSFER_FAILED:
			this.isLoading(false, name);
	}
}
```

## How to Override Templates

Templates are created to replace the default templates provided out of the box
by the WJCS SDK. This can be done when initialising the WJCS SDK by setting the
`templates` parameter in the options parameter. Templates should be a dictionary
which contains template objects distinguished by their keys. There are multiple
templates that can be overwritten as listed in the `Templates` documentation. It
is possible to only replace select entries if desired. Any which are not
overridden will have their defaults used.

When replacing the templates, be mindful of the `data-jcs-element` attributes on
certain elements as this is what the SDK uses to hook into the DOM to provide
updates and listen for interaction events. Refer to the `DefaultTemplates`
documentation for further information about the required elements and attributes
for each template. So long as the required elements are present, the ordering
and positioning can be altered to allow you to customise the experience for your
users.

Below is an example showing the `Templates.Camera` template being overridden
with a custom function returning the new template string to use.

**_Javascript Implementation:_**
```javascript
import {
	PreProcessor,
	Templates,
	TemplateType
} from 'idscan-jcs';

function cameraTemplate() {
	return `
		<form class="camera-options--container">
			<p data-jcs-element="camera__status" class="camera-status"></p>
			<select data-jcs-element="camera__select" class="camera-choices"></select>
		</form>
		<div class="camera-viewfinder--container">
			<canvas data-jcs-element="camera__viewfinder" class="camera-viewfinder"></canvas>
		</div>
		<form class="button-container">
			<input data-jcs-element="camera__capture" class="button button--primary" type="button" value="{{CAMERA_CAPTURE}}" />
		</form>
	`;
}

export const templates {
	[Templates.Camera]: {
		type: TemplateType.Function,
		processor: PreProcessor.Mustache,
		provider: () => cameraTemplate()
	}
}
```

**_Typescript Implementation:_**
```typescript
import {
	ITemplateConfiguration,
	PreProcessor,
	Templates,
	TemplateType
} from 'idscan-jcs';

function cameraTemplate(): string {
	return `
		<form class="camera-options--container">
			<p data-jcs-element="camera__status" class="camera-status"></p>
			<select data-jcs-element="camera__select" class="camera-choices"></select>
		</form>
		<div class="camera-viewfinder--container">
			<canvas data-jcs-element="camera__viewfinder" class="camera-viewfinder"></canvas>
		</div>
		<form class="button-container">
			<input data-jcs-element="camera__capture" class="button button--primary" type="button" value="{{CAMERA_CAPTURE}}" />
		</form>
	`;
}

export const templates: Partial<Record<Templates, ITemplateConfiguration>> = {
	[Templates.Camera]: {
		type: TemplateType.Function,
		processor: PreProcessor.Mustache,
		provider: () => cameraTemplate()
	}
}
```

Note in this case that the `type` has been set to `TemplateType.Function` which
instructs the rendering engine in the SDK to execute the function which is
passed in to retrieve the new template string. This allows you to pass
additional information into your template generator functions at run time, for
example passing in the current time, or custom class names if you are using a
tool like JSS for your styling.

For more information regarding the different template types, please refer to the
`Template` documentation where you will find detailed examples and use cases for
each option.

Also note that the `processor` has been set to `PreProcessor.Mustache`,
instructing the rendering engine in the SDK to run the new template string
through the [mustache](https://mustache.github.io/) templating engine before
writing it to the DOM. This enables you to pass markup in which will be repeated
or formatted without you having to write that functionality yourself. An example
of this can be seen in the `Templates.CameraList` default template.

For more information regarding the different `PreProcessor` types, please refer
to the `PreProcessor` documentation where you will find detailed examples and
use cases for each option.

## How to Override Dictionary Entries

The dictionary functionality provides support for string substitution throughout
the SDK. This includes strings in templates as well as values which are derived
by the services such as liveness instructions and final journey results.

For more information about the dictionary keys which are available, please
refer to the `TranslationKey` documentation. Any keys which are not overridden
will use the default values.

**_Javascript Implementation:_**
```javascript
import { TranslationKey } from 'idscan-jcs';

export const textTranslation = {
	// ...
	[TranslationKey.FLOW_STATE_IDENTITY_FRONT]: 'Please Upload an Image of the Front of your Identification',
	[TranslationKey.FLOW_STATE_IDENTITY_BACK]: 'Please Upload an Image of the Back of your Identification',
	[TranslationKey.PROVIDER_TITLE_FILESYSTEM]: 'Upload a file by clicking the button below',
	[TranslationKey.FILESYSTEM_DROP_IMAGE]: 'Alternatively drag & drop an image here',
	[TranslationKey.GATEWAY_CANCEL]: 'Cancel',
	[TranslationKey.VIEW_CANCEL]: 'Change File',
	// ...
};
```

**_Typescript Implementation:_**
```typescript
import { TranslationKey } from 'idscan-jcs';

export const textTranslation: Partial<Record<TranslationKey, string>> = {
	// ...
	[TranslationKey.FLOW_STATE_IDENTITY_FRONT]: 'Please Upload an Image of the Front of your Identification',
	[TranslationKey.FLOW_STATE_IDENTITY_BACK]: 'Please Upload an Image of the Back of your Identification',
	[TranslationKey.PROVIDER_TITLE_FILESYSTEM]: 'Upload a file by clicking the button below',
	[TranslationKey.FILESYSTEM_DROP_IMAGE]: 'Alternatively drag & drop an image here',
	[TranslationKey.GATEWAY_CANCEL]: 'Cancel',
	[TranslationKey.VIEW_CANCEL]: 'Change File',
	// ...
};
```

## CSS and Customisation

Within the WJCS library there are multiple ways to overwrite the styling and
customise components outputted as described below:

### Overwriting Styles

Each component outputted by the WJCS SDK has class names applied to all its
elements, these classes can be used within a stylesheet to allow developers to
customise the styling of the WJCS SDK without having to provide updated
templates or a dictionary. This provides a quick as easy way to get started with
the WJCS SDK and a code example of this can be seen here:
[Basic Static Website Integration](#).

### Replacing Styles Using Custom Templates

A more customisable way of implementing the WJCS SDK is to use a custom
templates which will allow developers to produced specific HTML and CSS for each
component outputted by the library. This provides a granular way to control how
the WJCS SDK library is displayed and a code example of how to do this with
[React](https://reactjs.org/) and [Aphrodite](https://github.com/Khan/aphrodite)
can be found here: [Advanced React Integration with JSS (via Aphrodite)](#).

## Device Capabilities and Camera Selection

When the SDK is first initialised, it will attempt to open each camera on the
device in turn and establish which capture resolutions are available for use. In
the event the device doesn't have any suitable capture devices, the camera and
smart capture providers will be disabled.

when the device capabilities have changed, and event is broadcast with the
available cameras capabilities listed inside. The `CAMERA:CAPABILITIES_UPDATED`
event includes minimum and maximum resolutions, frame rates and
aspect ratios as well as the device id and name.

Due to some devices having multiple cameras for the user to choose from, it is
advisable to suggest to your users that they should ensure the correct camera is
visible in the viewfinder before beginning the process. There are many ways of
doing this:

### Adding content into the templates

You can modify the camera templates for each step to add in custom text for your
users to advice them which camera they should be using for the current step. You
could also have a dialog of sorts which would require the user to acknowledge
they have understood the message, and remove it from the template with some
simple javascript.

### Listening for events

You could listen to the aforementioned events, and when the user is shown one of
the templates where a camera is required, you can use the known number of
cameras to decide whether to display a dialog for the user to read suggesting a
suitable camera for them to use.

## Supported Platforms

This SDK has been developed with the following platform and browser combinations
in mind and should function as intended with the latest versions from the
software providers.

**NOTE:** Platforms not on this list will not always function as intended but
should still allow for basic functionality. Please consult the Known Issues
section of this documentation for more information on this.

* Microsoft Windows 10
	* Google Chrome
	* Mozilla FireFox
	* Microsoft Edge
	* Microsoft Internet Explorer*

* Google Android 8+
	* Google Chrome

* Apple MacOS Mojave
	* Google Chrome
	* Mozilla FireFox
	* Apple Safari

* Apple iOS 11+
	* Apple Safari

\* There is no camera capture support in Internet Explorer, only file uploading
capabilities are offered.

## Known Issues, Workarounds, Fall-back Mechanisms and Gotchas

These are the current known issues which may be observed when using the SDK in
certain circumstances. Please note that these may change at any time as new
versions of browsers and operating systems are released by manufacturers.

### No Available Capture Devices

In the event no cameras are detected, the system will fall back to the file
upload provider automatically. This applies on all supported platforms.

**NOTE:** In case the user starts a journey that has a liveness check within it,
 and the device does not have a camera then the user will not be able to complete the journey.

### No Support for Web Assembly

In the event Web Assembly is not supported in the target browser, it will
automatically fall back to the default camera input method. This also applies on
all supported platforms.

### Camera Permissions

If camera permissions are not granted when requested, or there is a delay in the
user responding to the prompt, a customisable message will be shown on screen
which will prompt them to review and accept the request for access to the camera
hardware.

**NOTE:** When using Chrome on a Windows machine, there is a known issue where
the camera permissions prompt is not shown until the window is moved on the
screen. It is advisable that you set a message in the translations if this is
likely to affect your integrations.

**NOTE:** When using Safari on iOS, camera permissions are requested once for
each camera on the device every time the page is reloaded. There is no way
around this as of the time of writing due to how Apple have dealt with
permissions in Safari. Please see the following article for further information:

https://stackoverflow.com/a/49942017



### Image Cropping

It is advised that you leave suitable space around the edges of the cropping
area for the user to move around the page if you don't plan on capping the size
of the canvas yourself. Due to how the cropping functionality works, the
scrolling of the page has to be prevented when interacting with the canvas to
allow accurate positioning of the handles. Without this behaviour it's very
difficult to position handles as the page moves as well as the handles.

Due to how iOS handles browser navigation, it is also advisable to leave a
decent sized margin around the outside of the image area so the user can grab
the adjustment handles without initiating page navigation. Different devices
exhibit different screen sensitivities in this regard, so some trial and error
may be required when tuning this for your solution.

### Browser Support for CSS Masking

The provided basic version of the camera overlay relies on CSS masking which
isn't fully supported in iOS Safari of FireFox due to the lack of composition
configuration in iOS Safari and advanced masking functionality in FireFox.

Depending on your requirements for the type and appearance of overlay, the
composition issues present on iOS may well not affect you or your users.

### MIME Types

Some web servers will not serve files when there is no defined MIME types for
files in the directories you're serving. Microsoft's IIS is a good example of
this when it comes to `.wasm` files as these are uncommon. If you see the ides
scripts failing to load, be sure to check your MIME type configuration.

### Older Browser Support

There are provisions in the codebase to support older browsers which don't offer
support for retrieving media devices and such. They will fall back to file
upload, although they will still require some polyfills to function. It is
recommended you use a simple service to polyfill on these older browsers rather
than try and build this into your solution. One such example is the following:

```html
<!--[if IE]>
	<script type="text/javascript" src="https://polyfill.io/v3/polyfill.min.js?flags=gated&rum=true&features=default%2Ces7%2Ces6%2Ces5" charset="UTF-8"></script>
<![endif]-->
```

Adding the above into the HTML document you're serving your customers will
load the scripts which will polyfill all the missing functionality on demand to
avoid you having to impact all browsers unnecessarily. You may want to customise
this for your own needs.

### Camera Choices

Some browsers, such as FireFox do not allow the user to control which camera to
use once permissions have been granted meaning it's not possible for the camera
select box to function as intended. There is currently no workaround for this!

**NOTE:**  If the device has a back camera, it will be selected automatically first for the Proof of Address step

### Camera Capabilities

Some cameras on certain devices will report erroneous data for properties like
the `facingMode` and `deviceId`, so it is suggested these are not relied upon
when making choices. Additionally, the `deviceId` will change on a per device
basis, and may also be different between execution cycles.


### Triple Scan Guidance
in case of triple scan is enabled from ieos side, wsdk will displaying another page that showing 
more detail around the reason why triple scan was triggered if the journey enters triple scan.
by passing a boolean value to `tripleScanGuidancePage` in the configuration, the default value is true.


### Assets Folder Location
when you want to specify the assets folder location you can set it in the configuration key `assetsDirectory` 
this is route to access the resources of the animations and other assets needed for the application.
it can be a path route or a URL route