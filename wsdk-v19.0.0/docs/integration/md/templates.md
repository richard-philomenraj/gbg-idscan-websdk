# Templates

## Template Type Configuration

There are five main types of template types. The aim of each one is to provide a
static string which is passed into the templating function to render to the
page. Internally, each of the five types is converted into a Promise which will
resolve with the string mentioned above.

### TemplateType.Function

The `Function` type provider should be a function which should accept a single
parameter which is the `Templates` string. This can be used to streamline the
template storage in the consuming application if desired.

The function should return a single string which is ready to be passed through
to the pre processor in the next rendering stage.

```typescript
[Templates]: {
	type: TemplateType.Function,
	provider: (templateName: Templates) => `<div>
		<h1>Template Heading</h1>
		<p>This is the template for ${templateName}</p>
	</div>`
}
```

### TemplateType.Null

The `Null` type must not specify any provider function. It is designed to
explicitly state to the system that there should be nothing rendered for this
template.

```typescript
[Templates]: {
	type: TemplateType.Null
}
```

### TemplateType.Promise

The `Promise` type provider should be a function which should accept a single
parameter which is the `Templates` string. This can be used to streamline the
template storage in the consuming application if desired.

The function should return a promise which resolves with a single string to be
passed through to the pre processor in the next rendering stage.

**NOTE:** You can use this type to programmatically generate asynchronous values
in your own code, or request the content from a web server using your own code
rather than depending on the `Url` type if desired. This will allow greater
control over request parameters and cookies.

```typescript
[Templates]: {
	type: TemplateType.Promise,
	provider: new Promise((resolve) =>
		resolve(`<div>
			<h1>Template Heading</h1>
			<p>This is your custom template</p>
		</div>`)
	)
}
```

### TemplateType.String

The `String` type provider should be a single string to be passed through to the
pre processor in the next rendering stage.

```typescript
[Templates]: {
	type: TemplateType.String,
	provider: `<div>
			<h1>Template Heading</h1>
			<p>This is the template for ${templateName}</p>
		</div>`
	)
}
```

### TemplateType.Url

The `Url` type provider should be a single string which will the system will
make an HTTP request to providing current cookies. The resulting string will be
passed through to the pre processor in the next rendering stage.

Loading events will be triggered during these requests for you to use to display
loading messages to your users.

```typescript
[Templates]: {
	type: TemplateType.Url,
	provider: `https://mywebsite.com/?template=my-template`
}
```

## Pre-Processor Configuration

There are currently two supported pre processors in the solution. The aim of
each one is to accept in a string and process it before returning it for
rendering to the DOM. They are only designed to accept and return static strings
for efficiency and uniformity, but there are parameters passed into them. Each
pre-processor is executed with the entire dictionary spread over the properties
provided by the current template function.

### PreProcessor.None

The `None` processor does nothing to the content passed into it and is
recommended for content which has already been processed for language strings.

### PreProcessor.Mustache

The `Mustache` processor runs the string through the
[mustache.js](https://github.com/janl/mustache.js) node module which uses the
[mustache](https://mustache.github.io/) templating engine internally to process
the provided strings.

## Default Templates

This file describes the default templates for each possible layout the SDK will
use during a journey life cycle. These can be overridden by following the
[How to Override Templates](#) section of the primary documentation. Any
templates not overridden will default to the values described here.

Some elements of the templates are required for the sdk to function correctly,
whereas others are not. These will be highlighted in the defaults below. These
elements can be moved around and wrapped in other elements if desired, but the
`data-jcs-element` attribute must remain. Additional class names, attributes and
ids can be added too if desired.

### Templates.None

This template is used when nothing should be displayed. You can override this
if required to display some kind of "loading" screen in the very brief moments
it may be visible.

#### Configuration

| Key | Value |
| - | - |
| Type | TemplateType.Null |

### Templates.Initializing

This template is used when the SDK is initialising and scanning hardware to
attempt to detect viable capture devices. This can take a variable amount of
time depending on how many capture devices are present on the device.

#### Configuration

| Key | Value |
| - | - |
| Type | TemplateType.String |
| Processor | PreProcessor.Mustache |

#### Default Provider

```html mustache
<h2 class="section-title">{{INITIALIZING_TITLE}}</h2>
<p>{{INITIALIZING_DESCRIPTION}}</p>
```

### Templates.Camera

This template is used to allow the user to use their device camera to capture an
image on the fly rather than having to take a picture with their normal camera
application and upload it separately. This is especially useful on desktop
devices where it's more difficult/slower to take a photo and upload it.

#### Configuration

| Key | Value |
| - | - |
| Type | TemplateType.String |
| Processor | PreProcessor.Mustache |

#### Default Provider

```html mustache
<div class="dark-inner-provider-container">
	<div>
		<form class="camera-options--container">
			<select data-jcs-element="camera__select" class="camera-choices camera-choices-half-s camera-choices-full-xs control control--select"></select>
		</form>
		<div class="help-container">
			<div class="camera-viewfinder--container">
				<canvas data-jcs-element="camera__viewfinder" class="camera-viewfinder"></canvas>
				<div data-jcs-element="overlay_viewfinder" class="camera-viewfinder--overlay--transparent">
					<div class="loading-animation-container" data-jcs-element="loading-animation" style="display:none">
					</div>
				</div>
			</div>
			<div data-jcs-element="info__journey__action__container" class="camera-capture--caption" style="display: none">
				<p data-jcs-element="info__journey__action__text"></p>
			</div>
			<div data-jcs-element="info__journey__action__selfie__container" class="camera-capture--caption" style="display: none">
				{{CAMERA_CAPTURE_SELFIE_CAPTION}}
			</div>
			<div data-jcs-element="info__journey__action__address__container" class="camera-capture--caption" style="display: none">
				{{CAMERA_CAPTURE_ADDRESS_CAPTION}}
			</div>
			<div data-jcs-element="info__journey__action__covid__container" class="camera-capture--caption" style="display: none">
				{{CAMERA_CAPTURE_COVID_CAPTION}}
			</div>
			<div class="help-inner-container" data-jcs-element="capture__help__container" style="display:none">
				<h2>{{HELP_MODAL_AUTOCAPTURE_TITLE}}</h2>
				<p>{{HELP_MODAL_AUTOCAPTURE_SUBTITLE}}</p>
				<b>{{HELP_MODAL_AUTOCAPTURE_TIPS}}</b>
				<div class="help-images-container">
					<div class="help-image-inner-container">
						<div class="help-image" data-jcs-element="capture__modal__glare__animation__container"></div>
						<div class="help-text">
							<b>{{HELP_MODAL_AUTOCAPTURE_GLARE_TITLE}}</b>
							<p>{{HELP_MODAL_AUTOCAPTURE_GLARE_SUBTITLE}}</p>
						</div>
					</div>
					<div class="help-image-inner-container">
						<div class="help-image" data-jcs-element="capture__modal__low__res__animation__container"></div>
						<div class="help-text">
							<b>{{HELP_MODAL_AUTOCAPTURE_FAR_TITLE}}</b>
							<p>{{HELP_MODAL_AUTOCAPTURE_FAR_SUBTITLE}}</p>
						</div>
					</div>
					<div class="help-image-inner-container">
						<div class="help-image" data-jcs-element="capture__modal__blur__animation__container"></div>
						<div class="help-text">
							<b>{{HELP_MODAL_AUTOCAPTURE_BLUR_TITLE}}</b>
							<p>{{HELP_MODAL_AUTOCAPTURE_BLUR_SUBTITLE}}</p>
						</div>
					</div>
				</div>
				<div class="modal-footer modal-bottom modal-actions-container">
					<p data-jcs-element="capture__modal__manual__capture__text">{{HELP_MODAL_AUTOCAPTURE_MANUAL_CAPTURE}}</p>
					<div class="actions-container">
						<div data-jcs-element="capture__modal__camera__manual__capture">
							<label class="control control--switch manual--mode--switch" aria-label="switch">
								<input type="checkbox" data-jcs-element="capture__modal__camera__manual__capture__check" checked />
								<div class="control__switch control__switch__blue">
									<div class="icon ico-checkmark-16 control__switchIcon text-black">
										<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg" class="icon ico-checkmark-16 control__switchIcon text-black"><path d="M13 4L6 12L3 9" stroke="white" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"></path></svg>
									</div>
								</div>
							</label>
							<p class="action-caption">{{HELP_MODAL_AUTOCAPTURE_TURN_MANUAL_CAPTURE}}</p>
						</div>
						<div class="spacer"></div>
						<div class="help-button-container" data-jcs-element="capture__modal__close__help">
							<svg width="24" height="24" class="help-button-modal-close" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
								<circle cx="12" cy="12" r="11" stroke-width="2" stroke-linecap="round"/>
								<path d="M16 16L8 8M8 16L16 8" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
							</svg>
							<p class="action-caption">{{REQUIRED_ACTION_Close}}</p>
						</div>
					</div>
				</div>
			</div>
			<div class="help-inner-container" data-jcs-element="capture__selfie__help__container" style="display:none">
				<h2>{{HELP_MODAL_SELFIE_TITLE}}</h2>
				<p>{{HELP_MODAL_SELFIE_SUBTITLE}}</p>
				<div class="show-mobile">
					<div class="help-info-inner-container" data-jcs-element="preview__info__container__fm">
						<span class="preview-info-title">{{PREVIEW_INFO_TITLE_ID}}</span>
						<div class="id-example-container">
							<div class="id-example-container-row">
								<div class="id-example id-example-good">
									<img class="sample-image sample-image-success" src="assets/images/selfie-good.svg" />
									<div class="separating-rectangle separating-rectangle-success"></div>
									<div class="preview-label preview-label-success">
										<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
										<span>{{PREVIEW_INFO_GOOD}}</span>
									</div>
									<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_FACEMATCH}}</span>
								</div>
								<div class="id-example id-example-angle-check">
									<img class="sample-image sample-image-error" src="assets/images/selfie-partial.svg" />
									<div class="separating-rectangle separating-rectangle-error"></div>
									<div class="preview-label preview-label-error">
										<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
										<span>{{PREVIEW_INFO_BAD}}</span>
									</div>
									<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_FACEMATCH}}</span>
								</div>
							</div>
							<div class="id-example-container-row">
								<div class="id-example id-example-cover-check">
									<img class="sample-image sample-image-error" src="assets/images/selfie-glasses.svg" />
									<div class="separating-rectangle separating-rectangle-error"></div>
									<div class="preview-label preview-label-error">
										<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
										<span>{{PREVIEW_INFO_BAD}}</span>
									</div>
									<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_FACEMATCH}}</span>
								</div>
								<div class="id-example id-example-blur-check">
									<img class="sample-image sample-image-error" src="assets/images/selfie-blurred.svg" />
									<div class="separating-rectangle separating-rectangle-error"></div>
									<div class="preview-label preview-label-error">
										<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
										<span>{{PREVIEW_INFO_BAD}}</span>
									</div>
									<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_FACEMATCH}}</span>
								</div>
							</div>
						</div>
						<div class="modal-close-button close-preview-info-button" data-jcs-element="capture__selfie__modal__close__help">
							<svg width="24" height="24" class="help-button-modal-close" viewBox="0 0 24 24" fill="none"
								xmlns="http://www.w3.org/2000/svg">
								<circle cx="12" cy="12" r="11" stroke-width="2" stroke-linecap="round" />
								<path d="M16 16L8 8M8 16L16 8" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
							</svg>
							<p class="action-caption">{{REQUIRED_ACTION_Close}}</p>
						</div>
					</div>
				</div>
				<div class="show-desktop preview-info-container">
					<div class="preview-info-title">
						<img class="preview-info-icon" src="assets/images/info-icon.svg">
						<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_ID}}</span>
					</div>
					<ul class="id-example-container">
						<li class="id-example id-example-good">
							<img class="sample-image sample-image-success" src="assets/images/selfie-good.svg">
							<div class="separating-rectangle separating-rectangle-success"></div>
							<div class="preview-label preview-label-success">
								<img class="preview-label-icon" src="assets/images/green-check-icon.svg">
								<span>{{PREVIEW_INFO_GOOD}}</span>
							</div>
							<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_FACEMATCH}}</span>
						</li>
						<li class="id-example id-example-angle-check">
							<img class="sample-image sample-image-error" src="assets/images/selfie-partial.svg">
							<div class="separating-rectangle separating-rectangle-error"></div>
							<div class="preview-label preview-label-error">
								<img class="preview-label-icon" src="assets/images/red-x-icon.svg">
								<span>{{PREVIEW_INFO_BAD}}</span>
							</div>
							<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_FACEMATCH}}</span>
						</li>
						<li class="id-example id-example-cover-check">
							<img class="sample-image sample-image-error" src="assets/images/selfie-glasses.svg">
							<div class="separating-rectangle separating-rectangle-error"></div>
							<div class="preview-label preview-label-error">
								<img class="preview-label-icon" src="assets/images/red-x-icon.svg">
								<span>{{PREVIEW_INFO_BAD}}</span>
							</div>
							<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_FACEMATCH}}</span>
						</li>
						<li class="id-example id-example-blur-check">
							<img class="sample-image sample-image-error" src="assets/images/selfie-blurred.svg">
							<div class="separating-rectangle separating-rectangle-error"></div>
							<div class="preview-label preview-label-error">
								<img class="preview-label-icon" src="assets/images/red-x-icon.svg">
								<span>{{PREVIEW_INFO_BAD}}</span>
							</div>
							<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_FACEMATCH}}</span>
						</li>
					</ul>
				</div>
			</div>
			<div class="help-inner-container" data-jcs-element="capture__covid__help__container" style="display:none">
				<h2>{{HELP_MODAL_COVID_TITLE}}</h2>
				<p>{{HELP_MODAL_COVID_SUBTITLE}}</p>
				<b>{{HELP_MODAL_COVID_TIPS}}</b>
				<div class="help-images-container">
					<div class="help-image-inner-container">
						<div class="help-image" data-jcs-element="capture__covid__modal__glare__animation__container"></div>
						<div class="help-text">
							<b>{{HELP_MODAL_COVID_GLARE_TITLE}}</b>
							<p>{{HELP_MODAL_COVID_GLARE_SUBTITLE}}</p>
						</div>
					</div>
					<div class="help-image-inner-container">
						<div class="help-image" data-jcs-element="capture__covid__modal__low__res__animation__container"></div>
						<div class="help-text">
							<b>{{HELP_MODAL_COVID_FAR_TITLE}}</b>
							<p>{{HELP_MODAL_COVID_FAR_SUBTITLE}}</p>
						</div>
					</div>
					<div class="help-image-inner-container">
						<div class="help-image" data-jcs-element="capture__covid__modal__blur__animation__container"></div>
						<div class="help-text">
							<b>{{HELP_MODAL_COVID_BLUR_TITLE}}</b>
							<p>{{HELP_MODAL_COVID_BLUR_SUBTITLE}}</p>
						</div>
					</div>
				</div>
				<div class="modal-footer modal-bottom modal-actions-container">
					<div class="actions-container">
						<div>

						</div>
						<div class="spacer"></div>
						<div class="help-button-container" data-jcs-element="capture__covid__modal__close__help">
							<svg width="24" height="24" class="help-button-modal-close" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
								<circle cx="12" cy="12" r="11" stroke-width="2" stroke-linecap="round"/>
								<path d="M16 16L8 8M8 16L16 8" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
							</svg>
							<p class="action-caption">{{REQUIRED_ACTION_Close}}</p>
						</div>
					</div>
				</div>
			</div>

			</div>
			<div class="help-inner-container" data-jcs-element="capture__covid__help__container" style="display:none">
				<h2>{{HELP_MODAL_COVID_TITLE}}</h2>
				<p>{{HELP_MODAL_COVID_SUBTITLE}}</p>
				<b>{{HELP_MODAL_COVID_TIPS}}</b>
				<div class="help-images-container">
					<div class="help-image-inner-container">
						<div class="help-image" data-jcs-element="capture__covid__modal__glare__animation__container"></div>
						<div class="help-text">
							<b>{{HELP_MODAL_COVID_GLARE_TITLE}}</b>
							<p>{{HELP_MODAL_COVID_GLARE_SUBTITLE}}</p>
						</div>
					</div>
					<div class="help-image-inner-container">
						<div class="help-image" data-jcs-element="capture__covid__modal__low__res__animation__container"></div>
						<div class="help-text">
							<b>{{HELP_MODAL_COVID_FAR_TITLE}}</b>
							<p>{{HELP_MODAL_COVID_FAR_SUBTITLE}}</p>
						</div>
					</div>
					<div class="help-image-inner-container">
						<div class="help-image" data-jcs-element="capture__covid__modal__blur__animation__container"></div>
						<div class="help-text">
							<b>{{HELP_MODAL_COVID_BLUR_TITLE}}</b>
							<p>{{HELP_MODAL_COVID_BLUR_SUBTITLE}}</p>
						</div>
					</div>
				</div>
				<div class="modal-footer modal-bottom modal-actions-container">
					<div class="actions-container">
						<div>

						</div>
						<div class="spacer"></div>
						<div class="help-button-container" data-jcs-element="capture__covid__modal__close__help">
							<svg width="24" height="24" class="help-button-modal-close" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
								<circle cx="12" cy="12" r="11" stroke-width="2" stroke-linecap="round"/>
								<path d="M16 16L8 8M8 16L16 8" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
							</svg>
							<p class="action-caption">{{REQUIRED_ACTION_Close}}</p>
						</div>
					</div>
				</div>
			</div>

			<div class="notification notification-blue" data-jcs-element="attempt__count__container">
				<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 16" fill="none">
					<path fill-rule="evenodd" clip-rule="evenodd" d="M0.25 8C0.25 3.71979 3.71979 0.25 8 0.25H8.00081C12.2787 0.254627 15.7454 3.72135 15.75 7.99919L15.75 8C15.75 12.2802 12.2802 15.75 8 15.75C3.71979 15.75 0.25 12.2802 0.25 8ZM7.99958 1.75C4.54799 1.75023 1.75 4.54836 1.75 8C1.75 11.4518 4.54822 14.25 8 14.25C11.4515 14.25 14.2496 11.4522 14.25 8.00081C14.2463 4.55026 11.4501 1.75394 7.99958 1.75ZM8 5C7.44772 5 7 4.55228 7 4C7 3.44772 7.44772 3 8 3C8.55229 3 9 3.44772 9 4C9 4.55228 8.55229 5 8 5ZM7 6.25C6.58579 6.25 6.25 6.58579 6.25 7C6.25 7.41421 6.58579 7.75 7 7.75H7.25V11.25H6.5C6.08579 11.25 5.75 11.5858 5.75 12C5.75 12.4142 6.08579 12.75 6.5 12.75H8H9.5C9.91421 12.75 10.25 12.4142 10.25 12C10.25 11.5858 9.91421 11.25 9.5 11.25H8.75V7C8.75 6.58579 8.41421 6.25 8 6.25H7Z" fill="#4A88C6"/>
				</svg>
				<p>{{ATTEMPTS_REMAINING}}<span data-jcs-element="attempt__counter"></span></p>
			</div>
		</div>
		<div data-jcs-element="view__status">
			<p data-jcs-element="view__status__message"></p>
		</div>
		<div class="actions-container">
			<div data-jcs-element="camera__manual__capture" class="item">
				<div data-jcs-element="camera__manual__capture__inner" style="display: none;">
					<label class="control control--switch manual--mode--switch" aria-label="switch">
						<input type="checkbox" checked/>
						<div class="control__switch">
							<div class="icon ico-checkmark-16 control__switchIcon text-black">
								<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg" class="icon ico-checkmark-16 control__switchIcon text-black"><path d="M13 4L6 12L3 9" stroke="rgb(0, 13, 26)" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"></path></svg>
							</div>
						</div>
					</label>
					<p class="action-caption">{{HELP_MODAL_POA_TURN_MANUAL_CAPTURE}}</p>
				</div>
			</div>
			<form class="manual-capture-button-container item">
				<div class="camera-capture--container">
					<input data-jcs-element="camera__capture" class="camera-capture--button" type="button" />
				</div>
			</form>
			<div class="item">
				<div class="help-button-container" data-jcs-element="capture__close__help">
					<svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
						<circle cx="12" cy="12" r="11" stroke="white" stroke-width="2" stroke-linecap="round"/>
						<path d="M16 16L8 8M8 16L16 8" stroke="white" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
					</svg>
					<p class="action-caption">{{REQUIRED_ACTION_Close}}</p>
				</div>
				<div class="help-button-container" data-jcs-element="capture__open__help">
					<svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
						<path fill-rule="evenodd" clip-rule="evenodd" d="M12 2C6.47715 2 2 6.47715 2 12C2 17.5228 6.47715 22 12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2ZM0 12C0 5.37258 5.37258 0 12 0C18.6274 0 24 5.37258 24 12C24 18.6274 18.6274 24 12 24C5.37258 24 0 18.6274 0 12ZM12 7C11.0445 7 10 7.68564 10 9C10 9.55228 9.55229 10 9 10C8.44771 10 8 9.55228 8 9C8 6.31436 10.227 5 12 5C14.2091 5 16 6.79086 16 9C16 10.8638 14.7252 12.4299 13 12.874V12.8746V12.8787V12.8827V12.8868V12.8909V12.895V12.8991V12.9031V12.9072V12.9113V12.9153V12.9194V12.9234V12.9275V12.9315V12.9355V12.9396V12.9436V12.9476V12.9517V12.9557V12.9597V12.9637V12.9677V12.9717V12.9757V12.9796V12.9836V12.9876V12.9915V12.9955V12.9995V13.0034V13.0073V13.0113V13.0152V13.0191V13.023V13.0269V13.0308V13.0347V13.0386V13.0425V13.0463V13.0502V13.0541V13.0579V13.0617V13.0656V13.0694V13.0732V13.077V13.0808V13.0846V13.0884V13.0922V13.0959V13.0997V13.1034V13.1071V13.1109V13.1146V13.1183V13.122V13.1257V13.1294V13.133V13.1367V13.1403V13.144V13.1476V13.1512V13.1548V13.1584V13.162V13.1656V13.1691V13.1727V13.1762V13.1797V13.1832V13.1868V13.1902V13.1937V13.1972V13.2007V13.2041V13.2075V13.211V13.2144V13.2178V13.2211V13.2245V13.2279V13.2312V13.2345V13.2379V13.2412V13.2445V13.2477V13.251V13.2542V13.2575V13.2607V13.2639V13.2671V13.2703V13.2735V13.2766V13.2797V13.2829V13.286V13.2891V13.2921V13.2952V13.2983V13.3013V13.3043V13.3073V13.3103V13.3133V13.3162V13.3191V13.3221V13.325V13.3279V13.3307V13.3336V13.3364V13.3392V13.342V13.3448V13.3476V13.3504V13.3531V13.3558V13.3585V13.3612V13.3639V13.3665V13.3691V13.3718V13.3744V13.3769V13.3795V13.382V13.3845V13.387V13.3895V13.392V13.3944V13.3969V13.3993V13.4017V13.404V13.4064V13.4087V13.411V13.4133V13.4156V13.4178V13.42V13.4223V13.4244V13.4266V13.4288V13.4309V13.433V13.4351V13.4371V13.4392V13.4412V13.4432V13.4452V13.4471V13.4491V13.451V13.4529V13.4547V13.4566V13.4584V13.4602V13.462V13.4637V13.4655V13.4672V13.4689V13.4705V13.4722V13.4738V13.4754V13.477V13.4785V13.48V13.4815V13.483V13.4845V13.4859V13.4873V13.4887V13.49V13.4914V13.4927V13.494V13.4952V13.4965V13.4977V13.4988V13.5C13 14.0523 12.5523 14.5 12 14.5C11.4477 14.5 11 14.0523 11 13.5V13.4988V13.4977V13.4965V13.4952V13.494V13.4927V13.4914V13.49V13.4887V13.4873V13.4859V13.4845V13.483V13.4815V13.48V13.4785V13.477V13.4754V13.4738V13.4722V13.4705V13.4689V13.4672V13.4655V13.4637V13.462V13.4602V13.4584V13.4566V13.4547V13.4529V13.451V13.4491V13.4471V13.4452V13.4432V13.4412V13.4392V13.4371V13.4351V13.433V13.4309V13.4288V13.4266V13.4244V13.4223V13.42V13.4178V13.4156V13.4133V13.411V13.4087V13.4064V13.404V13.4017V13.3993V13.3969V13.3944V13.392V13.3895V13.387V13.3845V13.382V13.3795V13.3769V13.3744V13.3718V13.3691V13.3665V13.3639V13.3612V13.3585V13.3558V13.3531V13.3504V13.3476V13.3448V13.342V13.3392V13.3364V13.3336V13.3307V13.3279V13.325V13.3221V13.3191V13.3162V13.3133V13.3103V13.3073V13.3043V13.3013V13.2983V13.2952V13.2921V13.2891V13.286V13.2829V13.2797V13.2766V13.2735V13.2703V13.2671V13.2639V13.2607V13.2575V13.2542V13.251V13.2477V13.2445V13.2412V13.2379V13.2345V13.2312V13.2279V13.2245V13.2211V13.2178V13.2144V13.211V13.2075V13.2041V13.2007V13.1972V13.1937V13.1902V13.1868V13.1832V13.1797V13.1762V13.1727V13.1691V13.1656V13.162V13.1584V13.1548V13.1512V13.1476V13.144V13.1403V13.1367V13.133V13.1294V13.1257V13.122V13.1183V13.1146V13.1109V13.1071V13.1034V13.0997V13.0959V13.0922V13.0884V13.0846V13.0808V13.077V13.0732V13.0694V13.0656V13.0617V13.0579V13.0541V13.0502V13.0463V13.0425V13.0386V13.0347V13.0308V13.0269V13.023V13.0191V13.0152V13.0113V13.0073V13.0034V12.9995V12.9955V12.9915V12.9876V12.9836V12.9796V12.9757V12.9717V12.9677V12.9637V12.9597V12.9557V12.9517V12.9476V12.9436V12.9396V12.9355V12.9315V12.9275V12.9234V12.9194V12.9153V12.9113V12.9072V12.9031V12.8991V12.895V12.8909V12.8868V12.8827V12.8787V12.8746V12.8705V12.8664V12.8623V12.8582V12.8541V12.85V12.8459V12.8418V12.8376V12.8335V12.8294V12.8253V12.8212V12.8171V12.8129V12.8088V12.8047V12.8006V12.7964V12.7923V12.7882V12.784V12.7799V12.7758V12.7716V12.7675V12.7634V12.7592V12.7551V12.751V12.7469V12.7427V12.7386V12.7345V12.7303V12.7262V12.7221V12.718V12.7138V12.7097V12.7056V12.7015V12.6973V12.6932V12.6891V12.685V12.6809V12.6768V12.6727V12.6686V12.6645V12.6604V12.6563V12.6522V12.6481V12.644V12.6399V12.6358V12.6317V12.6277V12.6236V12.6195V12.6155V12.6114V12.6073V12.6033V12.5992V12.5952V12.5912V12.5871V12.5831V12.5791V12.575V12.571V12.567V12.563V12.559V12.555V12.551V12.547V12.5431V12.5391V12.5351V12.5312V12.5272V12.5233V12.5193V12.5154V12.5114V12.5075V12.5036V12.4997V12.4958V12.4919V12.488V12.4841V12.4803V12.4764V12.4725V12.4687V12.4648V12.461V12.4572V12.4534V12.4496V12.4458V12.442V12.4382V12.4344V12.4306V12.4269V12.4231V12.4194V12.4157V12.4119V12.4082V12.4045V12.4008V12.3971V12.3935V12.3898V12.3862V12.3825V12.3789V12.3753V12.3716V12.368V12.3645V12.3609V12.3573V12.3538V12.3502V12.3467V12.3432V12.3396V12.3361V12.3327V12.3292V12.3257V12.3223V12.3188V12.3154V12.312V12.3086V12.3052V12.3018V12.2984V12.2951V12.2917V12.2884V12.2851V12.2818V12.2785V12.2753V12.272V12.2687V12.2655V12.2623V12.2591V12.2559V12.2527V12.2496V12.2464V12.2433V12.2402V12.2371V12.234V12.2309V12.2279V12.2248V12.2218V12.2188V12.2158V12.2128V12.2099V12.2069V12.204V12.2011V12.1982V12.1953V12.1924V12.1896V12.1867V12.1839V12.1811V12.1784V12.1756V12.1728V12.1701V12.1674V12.1647V12.162V12.1594V12.1567V12.1541V12.1515V12.1489V12.1463V12.1438V12.1413V12.1388V12.1363V12.1338V12.1313V12.1289V12.1265V12.1241V12.1217V12.1193V12.117V12.1147V12.1124V12.1101V12.1078V12.1056V12.1034V12.1012V12.099V12.0968V12.0947V12.0926V12.0905V12.0884V12.0864V12.0843V12.0823V12.0803V12.0783V12.0764V12.0745V12.0726V12.0707V12.0688V12.067V12.0652V12.0634V12.0616V12.0599V12.0581V12.0564V12.0548V12.0531V12.0515V12.0499V12.0483V12.0467V12.0452V12.0437V12.0422V12.0407V12.0393V12.0378V12.0365V12.0351V12.0337V12.0324V12.0311V12.0298V12.0286V12.0274V12.0262V12.025V12.0239V12.0227V12.0216V12.0206V12.0195V12.0185V12.0175V12.0166V12.0156V12.0147V12.0138V12.013V12.0121V12.0113V12.0105V12.0098V12.0091V12.0084V12.0077V12.007V12.0064V12.0058V12.0053V12.0047V12.0042V12.0037V12.0033V12.0029V12.0025V12.0021V12.0018V12.0015V12.0012V12.0009V12.0007V12.0005V12.0004V12.0002V12.0001V12.0001V12L12 12H11C11 11.4477 11.4477 11 12 11C13.1046 11 14 10.1046 14 9C14 7.89543 13.1046 7 12 7ZM12 18C12.5523 18 13 17.5523 13 17C13 16.4477 12.5523 16 12 16C11.4477 16 11 16.4477 11 17C11 17.5523 11.4477 18 12 18Z" fill="white"/>
					</svg>
					<p class="action-caption">{{HELP_MODAL_NEED_HELP}}</p>
				</div>
			</div>
		</div>
	</div>
</div>
```

#### Elements

| Name | Purpose | Required |
| - | - | - |
| info__journey__action__container | Container to hold the text title when camera is being used for document capture | No |
| info__journey__action__text | Displays the text for document action | No |
| info__journey__action__selfie__container | Container to hold the text title when camera is being used for selfie capture | No |
| info__journey__action__address__container | Container to hold the text title when camera is being used for poa capture | No |
| info__journey__action__covid__container | Container to hold the text title when camera is being used for covid capture | No |
| capture__help__container | Container to show help modal for document step | No |
| capture__modal__glare__animation__container | Container to hold glare animation for document step | No |
| capture__modal__low__res__animation__container | Container to hold low resolution animation for document step | No |
| capture__modal__blur__animation__container | Container to hold blur animation modal for document step | No |
| capture__modal__camera__manual__capture__check | Check to move back to smart capture is enabled | No |
| capture__modal__close__help | Button to close document help modal | No |
| capture__selfie__help__container | Container to show help modal for document step | No |
| capture__selfie__modal__close__help | Button to close selfie help modal | No |
| capture__address__help__container | Container to show help modal for document step | No |
| capture__address__modal__glare__animation__container | Container to hold glare animation for PoA step | No |
| capture__address__modal__low__res__animation__container | Container to hold low resolution animation for PoA step | No |
| capture__address__modal__blur__animation__container | Container to hold blur animation modal for PoA step | No |
| capture__address__modal__close__help | Button to close document help modal | No |
| capture__covid__help__container | Container to show help modal for document step | No |
| capture__covid__modal__glare__animation__container | Container to hold glare animation for covid step | No |
| capture__covid__modal__low__res__animation__container | Container to hold low resolution animation for covid step | No |
| capture__covid__modal__blur__animation__container | Container to hold blur animation modal for covid step | No |
| capture__covid__modal__close__help | Button to close document help modal | No |
| camera__select | Displays the select element for picking the camera to use | No |
| camera__viewfinder | Displays the preview for the image capture | Yes |
| overlay_viewfinder | Displays an overlay above image capture | No |
| camera__capture | Capture a frame | Yes |
| camera__manual__capture | Container for the smart capture check | Yes |
| camera__manual__capture__inner | Inner Container for the smart capture check | Yes |
| capture__close__help | Close help modal for desktop devices | Yes |
| capture__open__help | Open help modal for desktop devices | Yes |
| attempt__count__container | Container to hold the attempt information | No |
| attempt__counter | Display the remaining attempts | No |

### Templates.Cropper

This template is used for the image cropping utility to display its UI. Users
can use this to adjust the detected image boundaries in an uploaded image to
further assist in document extraction.

#### Configuration

| Key | Value |
| - | - |
| Type | TemplateType.String |
| Processor | PreProcessor.Mustache |

#### Default Provider

```html mustache
<h2 class="section-title">{{PROVIDER_TITLE_CROPPER}}</h2>
<div class="info-container">
	<p class="info-item journey-state">
		<span class="info-item__name">{{INFO_JOURNEY_STATE}}</span>
		<span data-jcs-element="info__journey__state" class="info-item__value">...</span>
	</p>
	<p class="info-item journey-action">
		<span class="info-item__name">{{INFO_JOURNEY_ACTION}}</span>
		<span data-jcs-element="info__journey__action" class="info-item__value">...</span>
	</p>
	<p class="info-item journey-action-attempt">
		<span class="info-item__name">{{INFO_JOURNEY_ACTION_ATTEMPT}}</span>
		<span data-jcs-element="info__journey__action__attempt" class="info-item__value">...</span>
	</p>
	<p class="info-item journey-id">
		<span class="info-item__name">{{INFO_JOURNEY_ID}}</span>
		<span data-jcs-element="info__journey__id" class="info-item__value">...</span>
	</p>
</div>
<div class="cropper--container">
	<canvas data-jcs-element="cropper__canvas" class="cropper"></canvas>
</div>
<form class="button-container">
	<input data-jcs-element="cropper__retry" class="button button--secondary" type="button" value="{{CROPPER_RETRY}}" />
	<input data-jcs-element="cropper__submit" class="button button--primary" type="button" value="{{CROPPER_UPLOAD}}" />
</form>
<div data-jcs-element="cropper__status">
	<p data-jcs-element="cropper__status__message"></p>
</div>
```

#### Elements

| Name | Purpose | Required |
| - | - | - |
| info__journey__state | Displays the current state of the journey | No |
| info__journey__action | Displays the currently required action | No |
| info__journey__action__attempt | Displays how many attempts have been taken for the currently required action | No |
| info__journey__id | Displays the current journey's ID | No |
| cropper__canvas | Displays the cropping UI | Yes |
| cropper__reset | Resets the cropping handles to the extremes of the image | No |
| cropper__submit | Submits the cropped image to the services for validation | Yes |
| cropper__status | The outer container for the status messages | No |
| cropper__status__message | The message container for the status messages | No |

### Templates.Filesystem

This template is used to present the user with a UI to select an image from
their file system either using the traditional input element approach, or using
the more modern drag and drop approach where available in the browser
environment.

#### Configuration

| Key | Value |
| - | - |
| Type | TemplateType.String |
| Processor | PreProcessor.Mustache |

#### Default Provider

```html mustache
<div class="light-inner-provider-container">
	<div>
		<div data-jcs-element="identity__document__file__upload">
			<div class="preview-title-container">
				<h2 class="preview-title">{{PROVIDER_TITLE_FILESYSTEM_ID}}</h2>
				<span class="preview-description">{{PROVIDER_SUBTITLE_FILESYSTEM_ID}}</span>
			</div>
			<div class="show-mobile">
				<div class="preview-info-mobile-button" data-jcs-element="preview__page__open__info__id">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_ID}}</span>
				</div>

				<div class="preview-info-inner-container" data-jcs-element="preview__info__container__id">
					<span class="preview-info-title">{{PREVIEW_INFO_TITLE_ID}}</span>
					<div class="id-example-container">
						<div class="id-example-container-row">
							<div class="id-example id-example-good">
								<img class="sample-image sample-image-success" src="assets/images/licence-good.svg" />
								<div class="separating-rectangle separating-rectangle-success"></div>
								<div class="preview-label preview-label-success">
									<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
									<span>{{PREVIEW_INFO_GOOD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_ID}}</span>
							</div>
							<div class="id-example id-example-angle-check">
								<img class="sample-image sample-image-error"
									src="assets/images/licence-bad-angle.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_ID}}</span>
							</div>
						</div>
						<div class="id-example-container-row">
							<div class="id-example id-example-cover-check">
								<img class="sample-image sample-image-error"
									src="assets/images/licence-bad-covered.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_ID}}</span>
							</div>
							<div class="id-example id-example-blur-check">
								<img class="sample-image sample-image-error"
									src="assets/images/licence-bad-blurred.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_ID}}</span>
							</div>
						</div>
					</div>
					<div class="modal-close-button close-preview-info-button"
						data-jcs-element="preview__page__close__info__id">
						<svg width="24" height="24" class="help-button-modal-close" viewBox="0 0 24 24" fill="none"
							xmlns="http://www.w3.org/2000/svg">
							<circle cx="12" cy="12" r="11" stroke-width="2" stroke-linecap="round" />
							<path d="M16 16L8 8M8 16L16 8" stroke-width="2" stroke-linecap="round"
								stroke-linejoin="round" />
						</svg>
						<p class="action-caption">{{REQUIRED_ACTION_Close}}</p>
					</div>
				</div>
			</div>
			<div class="show-desktop preview-info-container">
				<div class="preview-info-title">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_ID}}</span>
				</div>
				<ul class="id-example-container">
					<li class="id-example id-example-good">
						<img class="sample-image sample-image-success" src="assets/images/licence-good.svg" />
						<div class="separating-rectangle separating-rectangle-success"></div>
						<div class="preview-label preview-label-success">
							<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
							<span>{{PREVIEW_INFO_GOOD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_ID}}</span>
					</li>
					<li class="id-example id-example-angle-check">
						<img class="sample-image sample-image-error" src="assets/images/licence-bad-angle.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_ID}}</span>
					</li>
					<li class="id-example id-example-cover-check">
						<img class="sample-image sample-image-error" src="assets/images/licence-bad-covered.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_ID}}</span>
					</li>
					<li class="id-example id-example-blur-check">
						<img class="sample-image sample-image-error" src="assets/images/licence-bad-blurred.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_ID}}</span>
					</li>
				</ul>
			</div>
			<div>
				<h3>{{FILESYSTEM_IDENTITY_DOCUMENT}} - <span data-jcs-element="info__journey__action"></h3>
			</div>
		</div>
		<div data-jcs-element="facematch__file__upload">
			<div class="preview-title-container">
				<h2>{{PROVIDER_TITLE_FILESYSTEM_FACEMATCH}}</h2>
				<span class="preview-description">{{PROVIDER_SUBTITLE_FILESYSTEM_FACEMATCH}}</span>
			</div>
			<div class="show-mobile">
				<div class="preview-info-mobile-button" data-jcs-element="preview__page__open__info__fm">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_FACEMATCH}}</span>
				</div>

				<div class="preview-info-inner-container" data-jcs-element="preview__info__container__fm">
					<span class="preview-info-title">{{PREVIEW_INFO_TITLE_FACEMATCH}}</span>
					<div class="id-example-container">
						<div class="id-example-container-row">
							<div class="id-example id-example-good">
								<img class="sample-image sample-image-success" src="assets/images/selfie-good.svg" />
								<div class="separating-rectangle separating-rectangle-success"></div>
								<div class="preview-label preview-label-success">
									<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
									<span>{{PREVIEW_INFO_GOOD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_FACEMATCH}}</span>
							</div>
							<div class="id-example id-example-angle-check">
								<img class="sample-image sample-image-error" src="assets/images/selfie-partial.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_FACEMATCH}}</span>
							</div>
						</div>
						<div class="id-example-container-row">
							<div class="id-example id-example-cover-check">
								<img class="sample-image sample-image-error" src="assets/images/selfie-glasses.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_FACEMATCH}}</span>
							</div>
							<div class="id-example id-example-blur-check">
								<img class="sample-image sample-image-error" src="assets/images/selfie-blurred.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_FACEMATCH}}</span>
							</div>
						</div>
					</div>
					<div class="modal-close-button close-preview-info-button" data-jcs-element="preview__page__close__info__fm">
						<svg width="24" height="24" class="help-button-modal-close" viewBox="0 0 24 24" fill="none"
							xmlns="http://www.w3.org/2000/svg">
							<circle cx="12" cy="12" r="11" stroke-width="2" stroke-linecap="round" />
							<path d="M16 16L8 8M8 16L16 8" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
						</svg>
						<p class="action-caption">{{REQUIRED_ACTION_Close}}</p>
					</div>
				</div>
			</div>
			<div class="show-desktop preview-info-container">
				<div class="preview-info-title">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_FACEMATCH}}</span>
				</div>
				<ul class="id-example-container">
					<li class="id-example id-example-good">
						<img class="sample-image sample-image-success" src="assets/images/selfie-good.svg" />
						<div class="separating-rectangle separating-rectangle-success"></div>
						<div class="preview-label preview-label-success">
							<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
							<span>{{PREVIEW_INFO_GOOD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_FACEMATCH}}</span>
					</li>
					<li class="id-example id-example-angle-check">
						<img class="sample-image sample-image-error" src="assets/images/selfie-partial.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_FACEMATCH}}</span>
					</li>
					<li class="id-example id-example-cover-check">
						<img class="sample-image sample-image-error" src="assets/images/selfie-glasses.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_FACEMATCH}}</span>
					</li>
					<li class="id-example id-example-blur-check">
						<img class="sample-image sample-image-error" src="assets/images/selfie-blurred.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_FACEMATCH}}</span>
					</li>
				</ul>
			</div>
			<div>
				<h3><span>{{FILESYSTEM_FACEMATCH}}</span></h3>
			</div>
		</div>
		<div data-jcs-element="address__file__upload">
			<div class="preview-title-container">
				<h2>{{PROVIDER_TITLE_FILESYSTEM_ADDRESS}}</h2>
				<span class="preview-description">{{PROVIDER_SUBTITLE_FILESYSTEM_ADDRESS}}</span>
			</div>
			<div class="show-mobile">
				<div class="preview-info-mobile-button" data-jcs-element="preview__page__open__info__poa">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_ADDRESS}}</span>
				</div>

				<div class="preview-info-inner-container" data-jcs-element="preview__info__container__poa">
					<span class="preview-info-title">{{PREVIEW_INFO_TITLE_ADDRESS}}</span>
					<div class="id-example-container">
						<div class="id-example-container-row">
							<div class="id-example id-example-good">
								<img class="sample-image sample-image-success" src="assets/images/address-good.svg" />
								<div class="separating-rectangle separating-rectangle-success"></div>
								<div class="preview-label preview-label-success">
									<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
									<span>{{PREVIEW_INFO_GOOD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_ADDRESS}}</span>
							</div>
							<div class="id-example id-example-angle-check">
								<img class="sample-image sample-image-error" src="assets/images/address-angled.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_ADDRESS}}</span>
							</div>
						</div>
						<div class="id-example-container-row">
							<div class="id-example id-example-cover-check">
								<img class="sample-image sample-image-error" src="assets/images/address-covered.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_ADDRESS}}</span>
							</div>
							<div class="id-example id-example-blur-check">
								<img class="sample-image sample-image-error" src="assets/images/address-blurred.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_ADDRESS}}</span>
							</div>
						</div>
					</div>
					<div class="modal-close-button close-preview-info-button" data-jcs-element="preview__page__close__info__poa">
						<svg width="24" height="24" class="help-button-modal-close" viewBox="0 0 24 24" fill="none"
							xmlns="http://www.w3.org/2000/svg">
							<circle cx="12" cy="12" r="11" stroke-width="2" stroke-linecap="round" />
							<path d="M16 16L8 8M8 16L16 8" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
						</svg>
						<p class="action-caption">{{REQUIRED_ACTION_Close}}</p>
					</div>
				</div>
			</div>
			<div class="show-desktop preview-info-container">
				<div class="preview-info-title">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_ADDRESS}}</span>
				</div>
				<ul class="id-example-container">
					<li class="id-example id-example-good">
						<img class="sample-image sample-image-success" src="assets/images/address-good.svg" />
						<div class="separating-rectangle separating-rectangle-success"></div>
						<div class="preview-label preview-label-success">
							<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
							<span>{{PREVIEW_INFO_GOOD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_ADDRESS}}</span>
					</li>
					<li class="id-example id-example-angle-check">
						<img class="sample-image sample-image-error" src="assets/images/address-angled.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_ADDRESS}}</span>
					</li>
					<li class="id-example id-example-cover-check">
						<img class="sample-image sample-image-error" src="assets/images/address-covered.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_ADDRESS}}</span>
					</li>
					<li class="id-example id-example-blur-check">
						<img class="sample-image sample-image-error" src="assets/images/address-blurred.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_ADDRESS}}</span>
					</li>
				</ul>
			</div>
			<div>
				<h3><span>{{FILESYSTEM_ADDRESS}}</span></h3>
			</div>
		</div>

<div data-jcs-element="covid__file__upload">
			<div class="preview-title-container">
				<h2>{{PROVIDER_TITLE_FILESYSTEM_COVID}}</h2>
				<span class="preview-description">{{PROVIDER_SUBTITLE_FILESYSTEM_COVID}}</span>
			</div>
			<div class="show-mobile">
				<div class="preview-info-mobile-button" data-jcs-element="preview__page__open__info__poa">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_COVID}}</span>
				</div>

				<div class="preview-info-inner-container" data-jcs-element="preview__info__container__poa">
					<span class="preview-info-title">{{PREVIEW_INFO_TITLE_COVID}}</span>
					<div class="id-example-container">
						<div class="id-example-container-row">
							<div class="id-example id-example-good">
								<img class="sample-image sample-image-success" src="assets/images/address-good.svg" />
								<div class="separating-rectangle separating-rectangle-success"></div>
								<div class="preview-label preview-label-success">
									<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
									<span>{{PREVIEW_INFO_GOOD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_COVID}}</span>
							</div>
							<div class="id-example id-example-angle-check">
								<img class="sample-image sample-image-error" src="assets/images/address-angled.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_COVID}}</span>
							</div>
						</div>
						<div class="id-example-container-row">
							<div class="id-example id-example-cover-check">
								<img class="sample-image sample-image-error" src="assets/images/address-covered.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_COVID}}</span>
							</div>
							<div class="id-example id-example-blur-check">
								<img class="sample-image sample-image-error" src="assets/images/address-blurred.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_COVID}}</span>
							</div>
						</div>
					</div>
					<div class="modal-close-button close-preview-info-button" data-jcs-element="preview__page__close__info__poa">
						<svg width="24" height="24" class="help-button-modal-close" viewBox="0 0 24 24" fill="none"
							xmlns="http://www.w3.org/2000/svg">
							<circle cx="12" cy="12" r="11" stroke-width="2" stroke-linecap="round" />
							<path d="M16 16L8 8M8 16L16 8" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
						</svg>
						<p class="action-caption">{{REQUIRED_ACTION_Close}}</p>
					</div>
				</div>
			</div>
			<div class="show-desktop preview-info-container">
				<div class="preview-info-title">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_COVID}}</span>
				</div>
				<ul class="id-example-container">
					<li class="id-example id-example-good">
						<img class="sample-image sample-image-success" src="assets/images/address-good.svg" />
						<div class="separating-rectangle separating-rectangle-success"></div>
						<div class="preview-label preview-label-success">
							<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
							<span>{{PREVIEW_INFO_GOOD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_COVID}}</span>
					</li>
					<li class="id-example id-example-angle-check">
						<img class="sample-image sample-image-error" src="assets/images/address-angled.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_COVID}}</span>
					</li>
					<li class="id-example id-example-cover-check">
						<img class="sample-image sample-image-error" src="assets/images/address-covered.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_COVID}}</span>
					</li>
					<li class="id-example id-example-blur-check">
						<img class="sample-image sample-image-error" src="assets/images/address-blurred.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_COVID}}</span>
					</li>
				</ul>
			</div>
			<div>
				<h3><span>{{FILESYSTEM_COVID}}</span></h3>
			</div>
		</div>


		<div>
			<p class="color-gray">{{FILESYSTEM_SUPPORTED_TYPES_START}}<i>{{FILESYSTEM_SUPPORTED_TYPES_END}}</i></p>
			<p class="color-gray">{{FILESYSTEM_MAX_FILE_SIZE}}</p>
		</div>
		<div class="file-upload-container" data-jcs-element="file__drop__box">
			<form class="file-input--container">
				<input data-jcs-element="file__input" class="file-input" id="jcs__file__input" type="file" />
			</form>
			<div class="drag-and-drop-container">
				<svg width="40" height="40" viewBox="0 0 40 40" fill="none" xmlns="http://www.w3.org/2000/svg">
					<path
						d="M28.75 18.75C34.2733 18.75 38.75 23.2267 38.75 28.75C38.75 34.2733 34.2733 38.75 28.75 38.75C23.2267 38.75 18.75 34.2733 18.75 28.75C18.75 23.2267 23.2267 18.75 28.75 18.75Z"
						stroke="#7E878F" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" />
					<path d="M28.75 33.75V23.75" stroke="#7E878F" stroke-width="1.5" stroke-linecap="round"
						stroke-linejoin="round" />
					<path d="M28.75 23.75L25 27.5" stroke="#7E878F" stroke-width="1.5" stroke-linecap="round"
						stroke-linejoin="round" />
					<path d="M28.75 23.75L32.5 27.5" stroke="#7E878F" stroke-width="1.5" stroke-linecap="round"
						stroke-linejoin="round" />
					<path d="M6.25 11.25H23.75" stroke="#7E878F" stroke-width="1.5" stroke-linecap="round"
						stroke-linejoin="round" />
					<path d="M6.25 18.75H16.25" stroke="#7E878F" stroke-width="1.5" stroke-linecap="round"
						stroke-linejoin="round" />
					<path d="M6.25 26.25H12.5" stroke="#7E878F" stroke-width="1.5" stroke-linecap="round"
						stroke-linejoin="round" />
					<path
						d="M12.5 33.75H3.75C2.37 33.75 1.25 32.63 1.25 31.25V3.75C1.25 2.37 2.37 1.25 3.75 1.25H21.465C22.1283 1.25 22.7633 1.51333 23.2317 1.98167L28.0183 6.76833C28.4867 7.23667 28.75 7.87167 28.75 8.535V12.5"
						stroke="#7E878F" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" />
				</svg>
				<span class="color-gray">{{FILESYSTEM_DRAG_DROP}}</span>
				<span class="color-gray">{{FILESYSTEM_OR}}</span>
			</div>
			<button class="button button--secondary"
				onclick="document.getElementById('jcs__file__input').click()">{{FILESYSTEM_CHOOSE}}</button>
		</div>
		<div>
			<p class="check-error-color" data-jcs-element="file__too__large__error">{{FILESYSTEM_CHECK_FILE_TOO_BIG}}
			</p>
			<p class="check-error-color" data-jcs-element="invalid__format__error">
				{{FILESYSTEM_CHECK_FILE_INCORRECT_FORMAT}}</p>
			<p data-jcs-element="uploading__file__without__preview">{{FILESYSTEM_UPLOADING}}</p>
		</div>
		<div class="notification notification-blue" data-jcs-element="attempt__count__container">
			<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 16" fill="none">
				<path fill-rule="evenodd" clip-rule="evenodd"
					d="M0.25 8C0.25 3.71979 3.71979 0.25 8 0.25H8.00081C12.2787 0.254627 15.7454 3.72135 15.75 7.99919L15.75 8C15.75 12.2802 12.2802 15.75 8 15.75C3.71979 15.75 0.25 12.2802 0.25 8ZM7.99958 1.75C4.54799 1.75023 1.75 4.54836 1.75 8C1.75 11.4518 4.54822 14.25 8 14.25C11.4515 14.25 14.2496 11.4522 14.25 8.00081C14.2463 4.55026 11.4501 1.75394 7.99958 1.75ZM8 5C7.44772 5 7 4.55228 7 4C7 3.44772 7.44772 3 8 3C8.55229 3 9 3.44772 9 4C9 4.55228 8.55229 5 8 5ZM7 6.25C6.58579 6.25 6.25 6.58579 6.25 7C6.25 7.41421 6.58579 7.75 7 7.75H7.25V11.25H6.5C6.08579 11.25 5.75 11.5858 5.75 12C5.75 12.4142 6.08579 12.75 6.5 12.75H8H9.5C9.91421 12.75 10.25 12.4142 10.25 12C10.25 11.5858 9.91421 11.25 9.5 11.25H8.75V7C8.75 6.58579 8.41421 6.25 8 6.25H7Z"
					fill="#4A88C6" />
			</svg>
			<p>{{ATTEMPTS_REMAINING}}<span
					data-jcs-element="attempt__counter"></span></p>
		</div>
	</div>
</div>
```

#### Elements

| Name | Purpose | Required |
| - | - | - |
| identity__document__file__upload | Container for identity document file upload guidance | No |
| preview__page__open__info__id | Container to hold the button to open the document id file upload help modal for mobile | No |
| preview__info__container__id | Container to hold the document id help modal for mobile | No |
| preview__page__close__info__id | Container to hold the button to close the document id file upload help modal for mobile | No |
| facematch__file__upload | Container for facematch file upload guidance | No |
| preview__page__open__info__fm | Container to hold the button to open the face match file upload help modal for mobile | No |
| preview__info__container__fm | Container to hold the face match help modal for mobile | No |
| preview__page__close__info__fm | Container to hold the button to close the face match file upload help modal for mobile | No |
| address__file__upload | Container for proof of address file upload guidance | No |
| preview__page__open__info__poa | Container to hold the button to open the proof of address file upload help modal for mobile | No |
| preview__info__container__poa | Container to hold the proof of address help modal for mobile | No |
| preview__page__close__info__poa | Container to hold the button to close the proof of address file upload help modal for mobile | No |
| covid__file__upload | Container for proof of covid file upload guidance | No |
| preview__page__open__info__covid | Container to hold the button to open the covid file upload help modal for mobile | No |
| preview__info__container__covid | Container to hold the covid help modal for mobile | No |
| preview__page__close__info__covid | Container to hold the button to close the covid file upload help modal for mobile | No |
| file__drop__box | Container for file drag and drop | No |
| file__input | Input for file upload | No |
| file__too__large__error | Container for file too large error message | No |
| invalid__format__error | Container for invalid format error message | No |
| uploading__file__without__preview | Container for file pdf without preview warning | No |
| attempt__count__container | Container to hold the attempt information | No |
| attempt__counter | Display the remaining attempts | No |

### Templates.Gateway

This template is used to display the entry point to the journey where the images
of identity documents are captured. This is shown when the capture mode is
either camera of file system upload.

#### Configuration

| Key | Value |
| - | - |
| Type | TemplateType.String |
| Processor | PreProcessor.Mustache |

#### Default Provider

```html mustache
<div data-jcs-element="provider__container" class="provider-container"></div>
<form class="button-container button-cancel-container button-gateway-container">
	<button data-jcs-element="cancel__journey" class="button button--secondary-destructive button-full-xs" aria-label="Button Title">
	{{CANCEL_JOURNEY}}
	</button>
	<button data-jcs-element="gateway__upload__toggle" class="button button--tertiary button--worker" aria-label="Button Title">
		<div data-jcs-element="gateway__upload__toggle__upload__label">
			{{MANUAL_PHOTO_UPLOAD_TOGGLE_FALSE}}
		</div>
		<div data-jcs-element="gateway__upload__toggle__camera__label">
			{{MANUAL_PHOTO_UPLOAD_TOGGLE_TRUE}}
		</div>
	</button>
</form>
```

#### Elements

| Name | Purpose | Required |
| - | - | - |
| gateway__upload__toggle | Allows the user to choose to use the camera instead of file upload | No |
| gateway__upload__toggle__upload__label | Contains helpful text for the upload toggle selector | No |
| gateway__upload__toggle__camera__label | Contains helpful text for the camera toggle selector | No |
| provider__container | Used as the position to write providers to the DOM | Yes |
| cancel__journey | Cancel the current journey | No |

### Templates.Liveness

This template is used to take the user through the active liveness checking process using the underlying retina services.

#### Configuration

| Key | Value |
| - | - |
| Type | TemplateType.String |
| Processor | PreProcessor.Mustache |

#### Default Provider

```html mustache
<div class="dark-inner-provider-container">
	<div>
		<form class="camera-options--container">
			<select data-jcs-element="camera__select"
				class="camera-choices camera-choices-half-s camera-choices-full-xs control control--select"></select>
		</form>
		<div class="camera-viewfinder--container" data-fullscreen="false">
			<canvas data-jcs-element="camera__viewfinder" class="camera-viewfinder"
				style="position: relative; left: 0; top: 0; z-index: 0;"></canvas>
			<div data-jcs-element="animation__viewfinder" class="camera-viewfinder"
				style="position: absolute; left: 0; top: 0; z-index: 1; display: none; align-items: flex-end; justify-content: center;">
				<div style="display: flex; align-items: center;">
					<div class="animation-container" data-jcs-element="animation__container">
						<svg xmlns="http://www.w3.org/2000/svg" width="76" height="76" viewBox="0 0 56 56" fill="none">
							<rect x="2" y="2" width="52" height="52" rx="13" fill="#111111" fill-opacity="0.8" stroke="white"
								stroke-width="4" />
							<ellipse cx="16.3333" cy="19.9452" rx="2.33333" ry="2.30137" fill="white" />
							<ellipse cx="40.4447" cy="19.9452" rx="2.33333" ry="2.30137" fill="white" />
							<path d="M20.2222 37.5891C24.8888 40.274 33.4444 40.274 37.7222 37.5891" stroke="white" stroke-width="4" />
						</svg>
					</div>
					<div class="liveness-actions-container">
						<p data-jcs-element="animation__title">{{LIVENESS_ACTION_LOOK_STRAIGHT_TITLE}}</p>
						<p data-jcs-element="animation__text">{{LIVENESS_ACTION_LOOK_STRAIGHT_TEXT}}</p>
					</div>
				</div>
			</div>
			<div data-jcs-element="liveness__start__button__container__overlay" class="camera-viewfinder--overlay"></div>
			<div data-jcs-element="liveness__start__button__container__button" class="camera-viewfinder--overlay--transparent">
				<p class="camera-viewfinder--overlay-text">
						<button data-jcs-element="retina__start" class="button button--primary camera-button">{{LIVENESS_START}}</button>
				</p>
			</div>
		</div>
	</div>
</div>
<form class="button-container button-cancel-container">
	<button data-jcs-element="cancel__journey" class="button button--secondary-destructive button-full-xs"
		aria-label="Button Title">
		{{CANCEL_JOURNEY}}
	</button>
</form>
```

#### Elements

| Name | Purpose | Required |
| - | - | - |
| camera__select | Displays the select element for picking the camera to use | No |
| camera__viewfinder | Displays the camera viewfinder | Yes |
| animation__viewfinder | Displays the animation step guidance overlay | No |
| animation__container | Container within the overlay to hold the animation | No |
| animation__title | Animation hint title | No |
| animation__text | Animation hint description| No |
| liveness__start__button__container__overlay | Overlay position in top of image viewfinder before starting capturing | No |
| liveness__start__button__container__button | Container to hold over the camera viewfinder the retina start button | No |
| retina__start | Starts the retina service capturing images | Yes |
| cancel__journey | Cancel the current journey | No |

### Templates.PassiveLiveness

This template is used to take the user through the passive liveness checking process

#### Configuration

| Key | Value |
| - | - |
| Type | TemplateType.String |
| Processor | PreProcessor.Mustache |

#### Default Provider

```html mustache
<div data-jcs-element="provider__container" class="provider-container" style="display: none;"></div>
<div class="dark-inner-provider-container" data-jcs-element="passive__liveness__step__container">
	<div>
		<form class="camera-options--container">
			<select data-jcs-element="camera__select"
				class="camera-choices camera-choices-half-s camera-choices-full-xs control control--select"></select>
		</form>
		<div class="help-container">
			<div class="camera-viewfinder--container" data-fullscreen="false">
				<canvas data-jcs-element="camera__viewfinder" class="camera-viewfinder"></canvas>
				<div data-jcs-element="overlay_viewfinder" class="camera-viewfinder--overlay--transparent">
					<div class="loading-animation-container" data-jcs-element="loading-animation" style="display:none">
					</div>
				</div>
			</div>
			<div class="camera-capture--caption">
				{{CAMERA_CAPTURE_SELFIE_CAPTION}}
			</div>
			<div class="help-inner-container" data-jcs-element="mobile__help__container" style="display: none;">
				<div class="preview-info-mobile-button">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_FACEMATCH}}</span>
				</div>

				<div class="preview-info-inner-container" data-jcs-element="preview__info__container__fm">
					<span class="preview-info-title">{{PREVIEW_INFO_TITLE_FACEMATCH}}</span>
					<div class="id-example-container">
						<div class="id-example-container-row">
							<div class="id-example id-example-good">
								<img class="sample-image sample-image-success" src="assets/images/selfie-good.svg" />
								<div class="separating-rectangle separating-rectangle-success"></div>
								<div class="preview-label preview-label-success">
									<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
									<span>{{PREVIEW_INFO_GOOD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_FACEMATCH}}</span>
							</div>
							<div class="id-example id-example-angle-check">
								<img class="sample-image sample-image-error" src="assets/images/selfie-partial.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_FACEMATCH}}</span>
							</div>
						</div>
						<div class="id-example-container-row">
							<div class="id-example id-example-cover-check">
								<img class="sample-image sample-image-error" src="assets/images/selfie-glasses.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_FACEMATCH}}</span>
							</div>
							<div class="id-example id-example-blur-check">
								<img class="sample-image sample-image-error" src="assets/images/selfie-blurred.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_FACEMATCH}}</span>
							</div>
						</div>
					</div>
					<div class="modal-close-button close-preview-info-button" data-jcs-element="mobile__close__help">
						<svg width="24" height="24" class="help-button-modal-close" viewBox="0 0 24 24" fill="none"
							xmlns="http://www.w3.org/2000/svg">
							<circle cx="12" cy="12" r="11" stroke-width="2" stroke-linecap="round" />
							<path d="M16 16L8 8M8 16L16 8" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
						</svg>
						<p class="action-caption">{{REQUIRED_ACTION_CLOSE}}</p>
					</div>
				</div>
			</div>
			<div class="help-inner-container" data-jcs-element="desktop__help__container" style="display: none;">
				<h2>{{HELP_MODAL_SELFIE_TITLE}}</h2>
				<p>{{HELP_MODAL_SELFIE_SUBTITLE}}</p>
				<div class="preview-info-container">
					<div class="preview-info-title">
						<img class="preview-info-icon" src="assets/images/info-icon.svg" />
						<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_FACEMATCH}}</span>
					</div>
					<ul class="id-example-container">
						<li class="id-example id-example-good">
							<img class="sample-image sample-image-success" src="assets/images/selfie-good.svg" />
							<div class="separating-rectangle separating-rectangle-success"></div>
							<div class="preview-label preview-label-success">
								<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
								<span>{{PREVIEW_INFO_GOOD}}</span>
							</div>
							<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_FACEMATCH}}</span>
						</li>
						<li class="id-example id-example-angle-check">
							<img class="sample-image sample-image-error" src="assets/images/selfie-partial.svg" />
							<div class="separating-rectangle separating-rectangle-error"></div>
							<div class="preview-label preview-label-error">
								<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
								<span>{{PREVIEW_INFO_BAD}}</span>
							</div>
							<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_FACEMATCH}}</span>
						</li>
						<li class="id-example id-example-cover-check">
							<img class="sample-image sample-image-error" src="assets/images/selfie-glasses.svg" />
							<div class="separating-rectangle separating-rectangle-error"></div>
							<div class="preview-label preview-label-error">
								<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
								<span>{{PREVIEW_INFO_BAD}}</span>
							</div>
							<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_FACEMATCH}}</span>
						</li>
						<li class="id-example id-example-blur-check">
							<img class="sample-image sample-image-error" src="assets/images/selfie-blurred.svg" />
							<div class="separating-rectangle separating-rectangle-error"></div>
							<div class="preview-label preview-label-error">
								<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
								<span>{{PREVIEW_INFO_BAD}}</span>
							</div>
							<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_FACEMATCH}}</span>
						</li>
					</ul>
				</div>
			</div>
		</div>
		<div class="actions-container">
			<div data-jcs-element="camera__manual__capture__spacer" class="item">
			</div>
			<form class="manual-capture-button-container item">
				<div class="camera-capture--container">
					<input data-jcs-element="camera__capture" class="camera-capture--button" type="button" />
				</div>
			</form>
			<div class="item show-mobile">
				<div class="help-button-container show-mobile" data-jcs-element="mobile__open__help">
					<svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
						<path fill-rule="evenodd" clip-rule="evenodd"
							d="M12 2C6.47715 2 2 6.47715 2 12C2 17.5228 6.47715 22 12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2ZM0 12C0 5.37258 5.37258 0 12 0C18.6274 0 24 5.37258 24 12C24 18.6274 18.6274 24 12 24C5.37258 24 0 18.6274 0 12ZM12 7C11.0445 7 10 7.68564 10 9C10 9.55228 9.55229 10 9 10C8.44771 10 8 9.55228 8 9C8 6.31436 10.227 5 12 5C14.2091 5 16 6.79086 16 9C16 10.8638 14.7252 12.4299 13 12.874V12.8746V12.8787V12.8827V12.8868V12.8909V12.895V12.8991V12.9031V12.9072V12.9113V12.9153V12.9194V12.9234V12.9275V12.9315V12.9355V12.9396V12.9436V12.9476V12.9517V12.9557V12.9597V12.9637V12.9677V12.9717V12.9757V12.9796V12.9836V12.9876V12.9915V12.9955V12.9995V13.0034V13.0073V13.0113V13.0152V13.0191V13.023V13.0269V13.0308V13.0347V13.0386V13.0425V13.0463V13.0502V13.0541V13.0579V13.0617V13.0656V13.0694V13.0732V13.077V13.0808V13.0846V13.0884V13.0922V13.0959V13.0997V13.1034V13.1071V13.1109V13.1146V13.1183V13.122V13.1257V13.1294V13.133V13.1367V13.1403V13.144V13.1476V13.1512V13.1548V13.1584V13.162V13.1656V13.1691V13.1727V13.1762V13.1797V13.1832V13.1868V13.1902V13.1937V13.1972V13.2007V13.2041V13.2075V13.211V13.2144V13.2178V13.2211V13.2245V13.2279V13.2312V13.2345V13.2379V13.2412V13.2445V13.2477V13.251V13.2542V13.2575V13.2607V13.2639V13.2671V13.2703V13.2735V13.2766V13.2797V13.2829V13.286V13.2891V13.2921V13.2952V13.2983V13.3013V13.3043V13.3073V13.3103V13.3133V13.3162V13.3191V13.3221V13.325V13.3279V13.3307V13.3336V13.3364V13.3392V13.342V13.3448V13.3476V13.3504V13.3531V13.3558V13.3585V13.3612V13.3639V13.3665V13.3691V13.3718V13.3744V13.3769V13.3795V13.382V13.3845V13.387V13.3895V13.392V13.3944V13.3969V13.3993V13.4017V13.404V13.4064V13.4087V13.411V13.4133V13.4156V13.4178V13.42V13.4223V13.4244V13.4266V13.4288V13.4309V13.433V13.4351V13.4371V13.4392V13.4412V13.4432V13.4452V13.4471V13.4491V13.451V13.4529V13.4547V13.4566V13.4584V13.4602V13.462V13.4637V13.4655V13.4672V13.4689V13.4705V13.4722V13.4738V13.4754V13.477V13.4785V13.48V13.4815V13.483V13.4845V13.4859V13.4873V13.4887V13.49V13.4914V13.4927V13.494V13.4952V13.4965V13.4977V13.4988V13.5C13 14.0523 12.5523 14.5 12 14.5C11.4477 14.5 11 14.0523 11 13.5V13.4988V13.4977V13.4965V13.4952V13.494V13.4927V13.4914V13.49V13.4887V13.4873V13.4859V13.4845V13.483V13.4815V13.48V13.4785V13.477V13.4754V13.4738V13.4722V13.4705V13.4689V13.4672V13.4655V13.4637V13.462V13.4602V13.4584V13.4566V13.4547V13.4529V13.451V13.4491V13.4471V13.4452V13.4432V13.4412V13.4392V13.4371V13.4351V13.433V13.4309V13.4288V13.4266V13.4244V13.4223V13.42V13.4178V13.4156V13.4133V13.411V13.4087V13.4064V13.404V13.4017V13.3993V13.3969V13.3944V13.392V13.3895V13.387V13.3845V13.382V13.3795V13.3769V13.3744V13.3718V13.3691V13.3665V13.3639V13.3612V13.3585V13.3558V13.3531V13.3504V13.3476V13.3448V13.342V13.3392V13.3364V13.3336V13.3307V13.3279V13.325V13.3221V13.3191V13.3162V13.3133V13.3103V13.3073V13.3043V13.3013V13.2983V13.2952V13.2921V13.2891V13.286V13.2829V13.2797V13.2766V13.2735V13.2703V13.2671V13.2639V13.2607V13.2575V13.2542V13.251V13.2477V13.2445V13.2412V13.2379V13.2345V13.2312V13.2279V13.2245V13.2211V13.2178V13.2144V13.211V13.2075V13.2041V13.2007V13.1972V13.1937V13.1902V13.1868V13.1832V13.1797V13.1762V13.1727V13.1691V13.1656V13.162V13.1584V13.1548V13.1512V13.1476V13.144V13.1403V13.1367V13.133V13.1294V13.1257V13.122V13.1183V13.1146V13.1109V13.1071V13.1034V13.0997V13.0959V13.0922V13.0884V13.0846V13.0808V13.077V13.0732V13.0694V13.0656V13.0617V13.0579V13.0541V13.0502V13.0463V13.0425V13.0386V13.0347V13.0308V13.0269V13.023V13.0191V13.0152V13.0113V13.0073V13.0034V12.9995V12.9955V12.9915V12.9876V12.9836V12.9796V12.9757V12.9717V12.9677V12.9637V12.9597V12.9557V12.9517V12.9476V12.9436V12.9396V12.9355V12.9315V12.9275V12.9234V12.9194V12.9153V12.9113V12.9072V12.9031V12.8991V12.895V12.8909V12.8868V12.8827V12.8787V12.8746V12.8705V12.8664V12.8623V12.8582V12.8541V12.85V12.8459V12.8418V12.8376V12.8335V12.8294V12.8253V12.8212V12.8171V12.8129V12.8088V12.8047V12.8006V12.7964V12.7923V12.7882V12.784V12.7799V12.7758V12.7716V12.7675V12.7634V12.7592V12.7551V12.751V12.7469V12.7427V12.7386V12.7345V12.7303V12.7262V12.7221V12.718V12.7138V12.7097V12.7056V12.7015V12.6973V12.6932V12.6891V12.685V12.6809V12.6768V12.6727V12.6686V12.6645V12.6604V12.6563V12.6522V12.6481V12.644V12.6399V12.6358V12.6317V12.6277V12.6236V12.6195V12.6155V12.6114V12.6073V12.6033V12.5992V12.5952V12.5912V12.5871V12.5831V12.5791V12.575V12.571V12.567V12.563V12.559V12.555V12.551V12.547V12.5431V12.5391V12.5351V12.5312V12.5272V12.5233V12.5193V12.5154V12.5114V12.5075V12.5036V12.4997V12.4958V12.4919V12.488V12.4841V12.4803V12.4764V12.4725V12.4687V12.4648V12.461V12.4572V12.4534V12.4496V12.4458V12.442V12.4382V12.4344V12.4306V12.4269V12.4231V12.4194V12.4157V12.4119V12.4082V12.4045V12.4008V12.3971V12.3935V12.3898V12.3862V12.3825V12.3789V12.3753V12.3716V12.368V12.3645V12.3609V12.3573V12.3538V12.3502V12.3467V12.3432V12.3396V12.3361V12.3327V12.3292V12.3257V12.3223V12.3188V12.3154V12.312V12.3086V12.3052V12.3018V12.2984V12.2951V12.2917V12.2884V12.2851V12.2818V12.2785V12.2753V12.272V12.2687V12.2655V12.2623V12.2591V12.2559V12.2527V12.2496V12.2464V12.2433V12.2402V12.2371V12.234V12.2309V12.2279V12.2248V12.2218V12.2188V12.2158V12.2128V12.2099V12.2069V12.204V12.2011V12.1982V12.1953V12.1924V12.1896V12.1867V12.1839V12.1811V12.1784V12.1756V12.1728V12.1701V12.1674V12.1647V12.162V12.1594V12.1567V12.1541V12.1515V12.1489V12.1463V12.1438V12.1413V12.1388V12.1363V12.1338V12.1313V12.1289V12.1265V12.1241V12.1217V12.1193V12.117V12.1147V12.1124V12.1101V12.1078V12.1056V12.1034V12.1012V12.099V12.0968V12.0947V12.0926V12.0905V12.0884V12.0864V12.0843V12.0823V12.0803V12.0783V12.0764V12.0745V12.0726V12.0707V12.0688V12.067V12.0652V12.0634V12.0616V12.0599V12.0581V12.0564V12.0548V12.0531V12.0515V12.0499V12.0483V12.0467V12.0452V12.0437V12.0422V12.0407V12.0393V12.0378V12.0365V12.0351V12.0337V12.0324V12.0311V12.0298V12.0286V12.0274V12.0262V12.025V12.0239V12.0227V12.0216V12.0206V12.0195V12.0185V12.0175V12.0166V12.0156V12.0147V12.0138V12.013V12.0121V12.0113V12.0105V12.0098V12.0091V12.0084V12.0077V12.007V12.0064V12.0058V12.0053V12.0047V12.0042V12.0037V12.0033V12.0029V12.0025V12.0021V12.0018V12.0015V12.0012V12.0009V12.0007V12.0005V12.0004V12.0002V12.0001V12.0001V12L12 12H11C11 11.4477 11.4477 11 12 11C13.1046 11 14 10.1046 14 9C14 7.89543 13.1046 7 12 7ZM12 18C12.5523 18 13 17.5523 13 17C13 16.4477 12.5523 16 12 16C11.4477 16 11 16.4477 11 17C11 17.5523 11.4477 18 12 18Z"
							fill="white" />
					</svg>
					<p class="action-caption">{{HELP_MODAL_NEED_HELP}}</p>
				</div>
			</div>
			<div class="item show-desktop">
				<div class="help-button-container show-desktop" data-jcs-element="desktop__close__help" style="display: none;">
					<svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
						<circle cx="12" cy="12" r="11" stroke="white" stroke-width="2" stroke-linecap="round" />
						<path d="M16 16L8 8M8 16L16 8" stroke="white" stroke-width="2" stroke-linecap="round"
							stroke-linejoin="round" />
					</svg>
					<p class="action-caption">{{REQUIRED_ACTION_CLOSE}}</p>
				</div>
				<div class="help-button-container show-desktop" data-jcs-element="desktop__open__help">
					<svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
						<path fill-rule="evenodd" clip-rule="evenodd"
							d="M12 2C6.47715 2 2 6.47715 2 12C2 17.5228 6.47715 22 12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2ZM0 12C0 5.37258 5.37258 0 12 0C18.6274 0 24 5.37258 24 12C24 18.6274 18.6274 24 12 24C5.37258 24 0 18.6274 0 12ZM12 7C11.0445 7 10 7.68564 10 9C10 9.55228 9.55229 10 9 10C8.44771 10 8 9.55228 8 9C8 6.31436 10.227 5 12 5C14.2091 5 16 6.79086 16 9C16 10.8638 14.7252 12.4299 13 12.874V12.8746V12.8787V12.8827V12.8868V12.8909V12.895V12.8991V12.9031V12.9072V12.9113V12.9153V12.9194V12.9234V12.9275V12.9315V12.9355V12.9396V12.9436V12.9476V12.9517V12.9557V12.9597V12.9637V12.9677V12.9717V12.9757V12.9796V12.9836V12.9876V12.9915V12.9955V12.9995V13.0034V13.0073V13.0113V13.0152V13.0191V13.023V13.0269V13.0308V13.0347V13.0386V13.0425V13.0463V13.0502V13.0541V13.0579V13.0617V13.0656V13.0694V13.0732V13.077V13.0808V13.0846V13.0884V13.0922V13.0959V13.0997V13.1034V13.1071V13.1109V13.1146V13.1183V13.122V13.1257V13.1294V13.133V13.1367V13.1403V13.144V13.1476V13.1512V13.1548V13.1584V13.162V13.1656V13.1691V13.1727V13.1762V13.1797V13.1832V13.1868V13.1902V13.1937V13.1972V13.2007V13.2041V13.2075V13.211V13.2144V13.2178V13.2211V13.2245V13.2279V13.2312V13.2345V13.2379V13.2412V13.2445V13.2477V13.251V13.2542V13.2575V13.2607V13.2639V13.2671V13.2703V13.2735V13.2766V13.2797V13.2829V13.286V13.2891V13.2921V13.2952V13.2983V13.3013V13.3043V13.3073V13.3103V13.3133V13.3162V13.3191V13.3221V13.325V13.3279V13.3307V13.3336V13.3364V13.3392V13.342V13.3448V13.3476V13.3504V13.3531V13.3558V13.3585V13.3612V13.3639V13.3665V13.3691V13.3718V13.3744V13.3769V13.3795V13.382V13.3845V13.387V13.3895V13.392V13.3944V13.3969V13.3993V13.4017V13.404V13.4064V13.4087V13.411V13.4133V13.4156V13.4178V13.42V13.4223V13.4244V13.4266V13.4288V13.4309V13.433V13.4351V13.4371V13.4392V13.4412V13.4432V13.4452V13.4471V13.4491V13.451V13.4529V13.4547V13.4566V13.4584V13.4602V13.462V13.4637V13.4655V13.4672V13.4689V13.4705V13.4722V13.4738V13.4754V13.477V13.4785V13.48V13.4815V13.483V13.4845V13.4859V13.4873V13.4887V13.49V13.4914V13.4927V13.494V13.4952V13.4965V13.4977V13.4988V13.5C13 14.0523 12.5523 14.5 12 14.5C11.4477 14.5 11 14.0523 11 13.5V13.4988V13.4977V13.4965V13.4952V13.494V13.4927V13.4914V13.49V13.4887V13.4873V13.4859V13.4845V13.483V13.4815V13.48V13.4785V13.477V13.4754V13.4738V13.4722V13.4705V13.4689V13.4672V13.4655V13.4637V13.462V13.4602V13.4584V13.4566V13.4547V13.4529V13.451V13.4491V13.4471V13.4452V13.4432V13.4412V13.4392V13.4371V13.4351V13.433V13.4309V13.4288V13.4266V13.4244V13.4223V13.42V13.4178V13.4156V13.4133V13.411V13.4087V13.4064V13.404V13.4017V13.3993V13.3969V13.3944V13.392V13.3895V13.387V13.3845V13.382V13.3795V13.3769V13.3744V13.3718V13.3691V13.3665V13.3639V13.3612V13.3585V13.3558V13.3531V13.3504V13.3476V13.3448V13.342V13.3392V13.3364V13.3336V13.3307V13.3279V13.325V13.3221V13.3191V13.3162V13.3133V13.3103V13.3073V13.3043V13.3013V13.2983V13.2952V13.2921V13.2891V13.286V13.2829V13.2797V13.2766V13.2735V13.2703V13.2671V13.2639V13.2607V13.2575V13.2542V13.251V13.2477V13.2445V13.2412V13.2379V13.2345V13.2312V13.2279V13.2245V13.2211V13.2178V13.2144V13.211V13.2075V13.2041V13.2007V13.1972V13.1937V13.1902V13.1868V13.1832V13.1797V13.1762V13.1727V13.1691V13.1656V13.162V13.1584V13.1548V13.1512V13.1476V13.144V13.1403V13.1367V13.133V13.1294V13.1257V13.122V13.1183V13.1146V13.1109V13.1071V13.1034V13.0997V13.0959V13.0922V13.0884V13.0846V13.0808V13.077V13.0732V13.0694V13.0656V13.0617V13.0579V13.0541V13.0502V13.0463V13.0425V13.0386V13.0347V13.0308V13.0269V13.023V13.0191V13.0152V13.0113V13.0073V13.0034V12.9995V12.9955V12.9915V12.9876V12.9836V12.9796V12.9757V12.9717V12.9677V12.9637V12.9597V12.9557V12.9517V12.9476V12.9436V12.9396V12.9355V12.9315V12.9275V12.9234V12.9194V12.9153V12.9113V12.9072V12.9031V12.8991V12.895V12.8909V12.8868V12.8827V12.8787V12.8746V12.8705V12.8664V12.8623V12.8582V12.8541V12.85V12.8459V12.8418V12.8376V12.8335V12.8294V12.8253V12.8212V12.8171V12.8129V12.8088V12.8047V12.8006V12.7964V12.7923V12.7882V12.784V12.7799V12.7758V12.7716V12.7675V12.7634V12.7592V12.7551V12.751V12.7469V12.7427V12.7386V12.7345V12.7303V12.7262V12.7221V12.718V12.7138V12.7097V12.7056V12.7015V12.6973V12.6932V12.6891V12.685V12.6809V12.6768V12.6727V12.6686V12.6645V12.6604V12.6563V12.6522V12.6481V12.644V12.6399V12.6358V12.6317V12.6277V12.6236V12.6195V12.6155V12.6114V12.6073V12.6033V12.5992V12.5952V12.5912V12.5871V12.5831V12.5791V12.575V12.571V12.567V12.563V12.559V12.555V12.551V12.547V12.5431V12.5391V12.5351V12.5312V12.5272V12.5233V12.5193V12.5154V12.5114V12.5075V12.5036V12.4997V12.4958V12.4919V12.488V12.4841V12.4803V12.4764V12.4725V12.4687V12.4648V12.461V12.4572V12.4534V12.4496V12.4458V12.442V12.4382V12.4344V12.4306V12.4269V12.4231V12.4194V12.4157V12.4119V12.4082V12.4045V12.4008V12.3971V12.3935V12.3898V12.3862V12.3825V12.3789V12.3753V12.3716V12.368V12.3645V12.3609V12.3573V12.3538V12.3502V12.3467V12.3432V12.3396V12.3361V12.3327V12.3292V12.3257V12.3223V12.3188V12.3154V12.312V12.3086V12.3052V12.3018V12.2984V12.2951V12.2917V12.2884V12.2851V12.2818V12.2785V12.2753V12.272V12.2687V12.2655V12.2623V12.2591V12.2559V12.2527V12.2496V12.2464V12.2433V12.2402V12.2371V12.234V12.2309V12.2279V12.2248V12.2218V12.2188V12.2158V12.2128V12.2099V12.2069V12.204V12.2011V12.1982V12.1953V12.1924V12.1896V12.1867V12.1839V12.1811V12.1784V12.1756V12.1728V12.1701V12.1674V12.1647V12.162V12.1594V12.1567V12.1541V12.1515V12.1489V12.1463V12.1438V12.1413V12.1388V12.1363V12.1338V12.1313V12.1289V12.1265V12.1241V12.1217V12.1193V12.117V12.1147V12.1124V12.1101V12.1078V12.1056V12.1034V12.1012V12.099V12.0968V12.0947V12.0926V12.0905V12.0884V12.0864V12.0843V12.0823V12.0803V12.0783V12.0764V12.0745V12.0726V12.0707V12.0688V12.067V12.0652V12.0634V12.0616V12.0599V12.0581V12.0564V12.0548V12.0531V12.0515V12.0499V12.0483V12.0467V12.0452V12.0437V12.0422V12.0407V12.0393V12.0378V12.0365V12.0351V12.0337V12.0324V12.0311V12.0298V12.0286V12.0274V12.0262V12.025V12.0239V12.0227V12.0216V12.0206V12.0195V12.0185V12.0175V12.0166V12.0156V12.0147V12.0138V12.013V12.0121V12.0113V12.0105V12.0098V12.0091V12.0084V12.0077V12.007V12.0064V12.0058V12.0053V12.0047V12.0042V12.0037V12.0033V12.0029V12.0025V12.0021V12.0018V12.0015V12.0012V12.0009V12.0007V12.0005V12.0004V12.0002V12.0001V12.0001V12L12 12H11C11 11.4477 11.4477 11 12 11C13.1046 11 14 10.1046 14 9C14 7.89543 13.1046 7 12 7ZM12 18C12.5523 18 13 17.5523 13 17C13 16.4477 12.5523 16 12 16C11.4477 16 11 16.4477 11 17C11 17.5523 11.4477 18 12 18Z"
							fill="white" />
					</svg>
					<p class="action-caption">{{HELP_MODAL_NEED_HELP}}</p>
				</div>
			</div>
		</div>
	</div>
</div>
<div class="notification notification-orange" data-jcs-element="passive__liveness__message__container" style="display: none;">
	<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 16" fill="none">
		<path fill-rule="evenodd" clip-rule="evenodd"
			d="M13.9212 13.5664L7.99878 1.70494L1.99909 13.5664H13.9212ZM15.3721 13.1144L9.18082 0.71445C8.95064 0.27521 8.49571 0 7.99982 0C7.50392 0 7.04899 0.27521 6.81882 0.71445L0.546752 13.1144C0.33024 13.5277 0.345567 14.0242 0.587162 14.4234C0.828758 14.8226 1.26147 15.0665 1.72808 15.0664H14.1908C14.6574 15.0665 15.0901 14.8226 15.3317 14.4234C15.5733 14.0242 15.5886 13.5277 15.3721 13.1144ZM7.99976 4.75C8.41397 4.75 8.74976 5.08579 8.74976 5.5V9.5C8.74976 9.91421 8.41397 10.25 7.99976 10.25C7.58554 10.25 7.24976 9.91421 7.24976 9.5V5.5C7.24976 5.08579 7.58554 4.75 7.99976 4.75ZM7.99976 13C8.55204 13 8.99976 12.5523 8.99976 12C8.99976 11.4477 8.55204 11 7.99976 11C7.44747 11 6.99976 11.4477 6.99976 12C6.99976 12.5523 7.44747 13 7.99976 13Z"
			fill="#F88618" />
	</svg>
	<div>
		<p data-jcs-element="passive__liveness__message__title" style="font-weight: bold;"></p>
		<p data-jcs-element="passive__liveness__message"></p>
	</div>
</div>
<div data-jcs-element="view__status">
	<p data-jcs-element="view__status__message"></p>
</div>
<form class="button-container button-cancel-container">
	<button data-jcs-element="cancel__journey" class="button button--secondary-destructive button-full-xs"
		aria-label="Button Title">
		{{CANCEL_JOURNEY}}
	</button>
</form>
```

#### Elements

| Name | Purpose | Required |
| - | - | - |
| provider__container | Used as the position to write the preview provider to the DOM | Yes |
| passive__liveness__step__container | Used as the container to hold the passive liveness capturing view | Yes |
| camera__select | Displays the select element for picking the camera to use | No |
| camera__viewfinder | Displays the camera viewfinder | Yes |
| overlay_viewfinder | Container to overlay the camera viewfinder | No |
| loading-animation | Container to hold the loading animation | No |
| mobile__help__container | Container to hold the mobile help modal | No |
| preview__info__container__fm | Container to hold the mobile help modal guidance instructions | No |
| mobile__close__help | Container to hold the close help modal for mobile | No |
| desktop__help__container | Container to hold the desktop help modal | No |
| camera__capture | Button to capture the passive liveness image | No |
| mobile__open__help | Container to hold the open help modal for mobile | No |
| desktop__close__help | Container to hold the close help modal for desktop | No |
| desktop__open__help | Container to hold the open help modal for desktop | No |
| passive__liveness__message__container | Container to hold the liveness errors | No |
| passive__liveness__message__title | Container to hold the liveness error title | No |
| passive__liveness__message | Container to hold the liveness error description | No |
| cancel__journey | Cancel the current journey | No |

### Templates.Login

This template is used to show the user the login page where they can provide a
username and password to authenticate with the service. This is not necessary if
specifying an API key/token instead.

#### Configuration

| Key | Value |
| - | - |
| Type | TemplateType.String |
| Processor | PreProcessor.Mustache |

#### Default Provider

```html mustache
<h2 class="section-title">{{PROVIDER_TITLE_LOGIN}}</h2>
<form data-jcs-element="login__form" class="login-form">
	<div class="input--container">
		<span class="input--label">{{LOGIN_USERNAME}}</span>
		<input data-jcs-element="login__username" class="input" id="login_username" type="text" />
	</div>
	<div class="input--container">
		<span class="input--label">{{LOGIN_PASSWORD}}</span>
		<input data-jcs-element="login__password" class="input" id="login_password" type="password" />
	</div>
	<div class="login-error--container">
		<p data-jcs-element="login__errors" class="login-error"></p>
	</div>
	<div class="button-container">
		<input data-jcs-element="login__submit" class="button button--primary" type="submit" value="{{LOGIN_SUBMIT}}" />
	</div>
</form>
```

#### Elements

| Name | Purpose | Required |
| - | - | - |
| login__form | Holds the form elements and handles submission fo data to the services when submitted | Yes |
| login__username | Provider space for the user to enter their username | Yes |
| login__password | Provides space for the user to enter their password | Yes |
| login__errors | Displays any errors | No |
| login__submit | Submits the form | No |

### Templates.Result

This template is used for showing the journey results, regardless of outcome.

#### Configuration

| Key | Value |
| - | - |
| Type | TemplateType.String |
| Processor | PreProcessor.Mustache |

#### Default Provider

```html mustache
<h2 class="section-title">{{PROVIDER_TITLE_RESULTS}}</h2>
<div class="info-container">
	{{#result}}
	<p class="info-item journey-result">
		<span class="info-item__name">{{INFO_JOURNEY_RESULT}}</span>
		<span class="info-item__value">{{result}}</span>
	</p>
	{{/result}}
	{{#referenceId}}
	<p class="info-item journey-reference">
		<span class="info-item__name">{{INFO_JOURNEY_REFERENCE}}</span>
		<span class="info-item__value">{{referenceId}}</span>
	</p>
	{{/referenceId}}
	<p class="info-item journey-id">
		<span class="info-item__name">{{INFO_JOURNEY_ID}}</span>
		<span class="info-item__value">{{journeyId}}</span>
	</p>
	<h3>{{RESULTS_DOCUMENTS}}</h3>
	<ol class="documents">
		{{#documents}}
			<li class="document" data-document="{{_type}}" data-result="{{_result}}">
				<ul class="document__details">
					<li class="info-item document-details__type">
						<span class="info-item__name">{{RESULTS_DOCUMENT_TYPE}}</span>
						<span class="info-item__value">{{type}}</span>
					</li>
					<li class="info-item document-details__result">
						<span class="info-item__name">{{RESULTS_DOCUMENT_RESULT}}</span>
						<span class="info-item__value">{{result}}</span>
					</li>
				</ul>
				<ul class="document__extracted-fields">
					{{#extractedFields}}
						<li class="info-item extracted-field" data-field="{{_name}}">
							<span class="info-item__name">{{name}}</span>
							</span class="info-item__value">{{value}}</span>
						</li>
					{{/extractedFields}}
					{{^extractedFields}}
						<li class="info-item">
							<span class="info-item__value">{{RESULTS_EXTRACTED_FIELDS_NONE}}</span>
						</li>
					{{/extractedFields}}
				</ul>
			</li>
		{{/documents}}
		{{^documents}}
			<li class="info-item">
				<span class="info-item__value">{{RESULTS_DOCUMENTS_NONE}}</span>
			</li>
		{{/documents}}
	</ol>
	<h3>{{RESULTS_DETAILS}}</h3>
	<ul>
		{{#resultDetails}}
			<li class="info-item" data-result-detail="{{_name}}">
				<span class="info-item__name">{{name}}</span>
				<span class="info-item__value">{{value}}</span>
			</li>
		{{/resultDetails}}
		{{^resultDetails}}
			<li class="info-item">
				<span class="info-item__value">{{RESULTS_DETAILS_NONE}}</span>
			</li>
		{{/resultDetails}}
	</ul>
</div>
<form class="button-container">
	<input data-jcs-element="result__logout" class="button button--secondary" type="button" value="{{RESULTS_LOGOUT}}" />
	<input data-jcs-element="result__journey__restart" class="button button--primary" type="button" value="{{RESULTS_NEW_JOURNEY}}" />
</form>
```

#### Elements

| Name | Purpose | Required |
| - | - | - |
| result__logout | Invoke a log out of the currently logged in user | No |
| result__journey__restart | Invoke a new journey | No |

### Templates.Scanner

This template is used for showing the scanning UI to the user. This doesn't
require any user interaction to function as intended. Actions are triggered by
the remote scanning services.

#### Configuration

| Key | Value |
| - | - |
| Type | TemplateType.String |
| Processor | PreProcessor.Mustache |

#### Default Provider

```html mustache
<div data-jcs-element="identity__document__file__upload">
	<div class="preview-title-container">
		<h2>{{PROVIDER_TITLE_SCANNER}}</h2>
		<span class="preview-description">{{PROVIDER_SUBTITLE_SCANNER_ID}}</span>
	</div>
	<div>
		<h3>{{FILESYSTEM_IDENTITY_DOCUMENT}} - <span data-jcs-element="info__journey__action"></h3>
	</div>
</div>
<div class="scanner-container" data-jcs-element="scanner__container">
	<div class="scanner-inner-container">

		<div class="scanner-feedback-container">
			<div class="scanner-loading-container" data-jcs-element="image__processing">

			</div>
			<div class="scanner-animation-container" data-jcs-element="scanner__animation" style="display:none">

			</div>
			<div>
				<svg width="21" height="16" viewBox="0 0 21 16"  fill="none" xmlns="http://www.w3.org/2000/svg" style="display: none;margin-top:0px;margin-bottom:0px;" data-jcs-element="error__image">
					<g clip-path="url(#clip0)">
					<path fill-rule="evenodd" clip-rule="evenodd" d="M12.241 0.71445L20.0783 13.1144C20.3524 13.5277 20.333 14.0242 20.0272 14.4234C19.7213 14.8226 19.1736 15.0665 18.5829 15.0664H2.80676C2.21608 15.0665 1.66833 14.8226 1.3625 14.4234C1.05667 14.0242 1.03727 13.5277 1.31134 13.1144L9.25098 0.71445C9.54236 0.27521 10.1182 0 10.746 0C11.3737 0 11.9496 0.27521 12.241 0.71445ZM9.90174 5.61532C9.90174 5.24713 10.2796 4.94865 10.7457 4.94865C11.2117 4.94865 11.5896 5.24713 11.5896 5.61532V9.61532C11.5896 9.98351 11.2117 10.282 10.7457 10.282C10.2796 10.282 9.90174 9.98351 9.90174 9.61532V5.61532ZM10.7884 13.2887H10.7648C10.0732 13.2869 9.50657 12.8546 9.48032 12.3087C9.46804 12.0447 9.59059 11.788 9.82041 11.5961C10.0502 11.4043 10.368 11.2935 10.7023 11.2887H10.7259C11.4174 11.2893 11.9849 11.7209 12.0121 12.2667C12.0253 12.5312 11.9029 12.7888 11.6726 12.9812C11.4423 13.1735 11.1235 13.2844 10.7884 13.2887Z" fill="#F88618"/>
					</g>
					<defs>
					<clipPath id="clip0">
					<rect width="20.254" height="16" fill="white" transform="translate(0.619141)"/>
					</clipPath>
					</defs>
				</svg>
				<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg" style="display: none;margin-top:0px;margin-bottom:0px;" data-jcs-element="success__image">
					<circle cx="8" cy="8" r="8" fill="#219C4C"/>
					<path d="M11.8787 5.09131L7.03018 10.9095L4.12109 8.00039" stroke="white" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
				</svg>

				<span data-jcs-element="scanner__box__title" class="scanner-title" style="display: none;">
					{{SCANNER_TITLE_WAITING}}
				</span>
			</div>
		</div>
		<button class="button button--secondary" data-jcs-element="scanner__action">{{SCANNER_DESC_LOADING}}</button>
	</div>

</div>
<div data-jcs-element="scanner__error" style="display: none;">
	<p class="check-error-color" data-jcs-element="scanner__error__busy">{{SCANNER_DESC_BUSY}}</p>
	<p class="check-error-color" data-jcs-element="scanner__error__offline">{{SCANNER_DESC_OFFLINE}}</p>
</div>

```

#### Elements

| Name | Purpose | Required |
| - | - | - |
| info__journey__action | Displays the currently required action | No |
| scanner__container | Container to hold all the scanner content | No |
| image__processing | Container to hold loading animation while processing | No |
| scanner__animation | Container to hold scanner animation while scanning | No |
| error__image | Image to show along scanner__box__title if error state | No |
| success__image | Image to show along scanner__box__title if successful state | No |
| scanner__box__title | Displays top line text for the scanner status | No |
| scanner__action | Begin the currently displayed scanner action | No |
| scanner__error | Container to hold the scanner error messages | No |
| scanner__error__busy | Displays scanner error busy message | No |
| scanner__error__offline | Displays scanner offline busy message | No |

### Templates.View

This template is used to show the user the image which has been captured by the
camera or selected from the filesystem.

#### Configuration

| Key | Value |
| - | - |
| Type | TemplateType.String |
| Processor | PreProcessor.Mustache |

#### Default Provider

```html mustache
<div class="light-inner-provider-container">
	<div>
		<div data-jcs-element="identity__document__preview">
			<div class="preview-title-container">
				<h2 class="preview-title">{{PROVIDER_TITLE_VIEW_ID}}</h2>
				<span class="preview-description">{{PROVIDER_SUBTITLE_VIEW_ID}}</span>
			</div>
			<div class="show-mobile">
				<div class="preview-info-mobile-button" data-jcs-element="preview__page__open__info__id">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_ID}}</span>
				</div>

				<div class="preview-info-inner-container" data-jcs-element="preview__info__container__id">
					<span class="preview-info-title">{{PREVIEW_INFO_TITLE_ID}}</span>
					<div class="id-example-container">
						<div class="id-example-container-row">
							<div class="id-example id-example-good">
								<img class="sample-image sample-image-success" src="assets/images/licence-good.svg" />
								<div class="separating-rectangle separating-rectangle-success"></div>
								<div class="preview-label preview-label-success">
									<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
									<span>{{PREVIEW_INFO_GOOD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_ID}}</span>
							</div>
							<div class="id-example id-example-angle-check">
								<img class="sample-image sample-image-error"
									src="assets/images/licence-bad-angle.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_ID}}</span>
							</div>
						</div>
						<div class="id-example-container-row">
							<div class="id-example id-example-cover-check">
								<img class="sample-image sample-image-error"
									src="assets/images/licence-bad-covered.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_ID}}</span>
							</div>
							<div class="id-example id-example-blur-check">
								<img class="sample-image sample-image-error"
									src="assets/images/licence-bad-blurred.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_ID}}</span>
							</div>
						</div>
					</div>
					<div class="modal-close-button close-preview-info-button"
						data-jcs-element="preview__page__close__info__id">
						<svg width="24" height="24" class="help-button-modal-close" viewBox="0 0 24 24" fill="none"
							xmlns="http://www.w3.org/2000/svg">
							<circle cx="12" cy="12" r="11" stroke-width="2" stroke-linecap="round" />
							<path d="M16 16L8 8M8 16L16 8" stroke-width="2" stroke-linecap="round"
								stroke-linejoin="round" />
						</svg>
						<p class="action-caption">{{REQUIRED_ACTION_Close}}</p>
					</div>
				</div>
			</div>

			<div class="show-desktop preview-info-container">
				<div class="preview-info-title">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_ID}}</span>
				</div>
				<ul class="id-example-container">
					<li class="id-example id-example-good">
						<img class="sample-image sample-image-success" src="assets/images/licence-good.svg" />
						<div class="separating-rectangle separating-rectangle-success"></div>
						<div class="preview-label preview-label-success">
							<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
							<span>{{PREVIEW_INFO_GOOD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_ID}}</span>
					</li>
					<li class="id-example id-example-angle-check">
						<img class="sample-image sample-image-error" src="assets/images/licence-bad-angle.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_ID}}</span>
					</li>
					<li class="id-example id-example-cover-check">
						<img class="sample-image sample-image-error" src="assets/images/licence-bad-covered.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_ID}}</span>
					</li>
					<li class="id-example id-example-blur-check">
						<img class="sample-image sample-image-error" src="assets/images/licence-bad-blurred.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_ID}}</span>
					</li>
				</ul>
			</div>
		</div>
		<div data-jcs-element="facematch__preview">
			<div class="preview-title-container">
				<h2>{{PROVIDER_TITLE_VIEW_FACEMATCH}}</h2>
				<span class="preview-description">{{PROVIDER_SUBTITLE_VIEW_FACEMATCH}}</span>
			</div>
			<div class="show-mobile">
				<div class="preview-info-mobile-button" data-jcs-element="preview__page__open__info__fm">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_FACEMATCH}}</span>
				</div>

				<div class="preview-info-inner-container" data-jcs-element="preview__info__container__fm">
					<span class="preview-info-title">{{PREVIEW_INFO_TITLE_FACEMATCH}}</span>
					<div class="id-example-container">
						<div class="id-example-container-row">
							<div class="id-example id-example-good">
								<img class="sample-image sample-image-success" src="assets/images/selfie-good.svg" />
								<div class="separating-rectangle separating-rectangle-success"></div>
								<div class="preview-label preview-label-success">
									<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
									<span>{{PREVIEW_INFO_GOOD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_FACEMATCH}}</span>
							</div>
							<div class="id-example id-example-angle-check">
								<img class="sample-image sample-image-error" src="assets/images/selfie-partial.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_FACEMATCH}}</span>
							</div>
						</div>
						<div class="id-example-container-row">
							<div class="id-example id-example-cover-check">
								<img class="sample-image sample-image-error" src="assets/images/selfie-glasses.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_FACEMATCH}}</span>
							</div>
							<div class="id-example id-example-blur-check">
								<img class="sample-image sample-image-error" src="assets/images/selfie-blurred.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_FACEMATCH}}</span>
							</div>
						</div>
					</div>
					<div class="modal-close-button close-preview-info-button" data-jcs-element="preview__page__close__info__fm">
						<svg width="24" height="24" class="help-button-modal-close" viewBox="0 0 24 24" fill="none"
							xmlns="http://www.w3.org/2000/svg">
							<circle cx="12" cy="12" r="11" stroke-width="2" stroke-linecap="round" />
							<path d="M16 16L8 8M8 16L16 8" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
						</svg>
						<p class="action-caption">{{REQUIRED_ACTION_Close}}</p>
					</div>
				</div>
			</div>
			<div class="show-desktop preview-info-container">
				<div class="preview-info-title">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_FACEMATCH}}</span>
				</div>
				<ul class="id-example-container">
					<li class="id-example id-example-good">
						<img class="sample-image sample-image-success" src="assets/images/selfie-good.svg" />
						<div class="separating-rectangle separating-rectangle-success"></div>
						<div class="preview-label preview-label-success">
							<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
							<span>{{PREVIEW_INFO_GOOD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_FACEMATCH}}</span>
					</li>
					<li class="id-example id-example-angle-check">
						<img class="sample-image sample-image-error" src="assets/images/selfie-partial.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_FACEMATCH}}</span>
					</li>
					<li class="id-example id-example-cover-check">
						<img class="sample-image sample-image-error" src="assets/images/selfie-glasses.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_FACEMATCH}}</span>
					</li>
					<li class="id-example id-example-blur-check">
						<img class="sample-image sample-image-error" src="assets/images/selfie-blurred.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_FACEMATCH}}</span>
					</li>
				</ul>
			</div>
		</div>
		<div data-jcs-element="address__preview">
			<div class="preview-title-container">
				<h2>{{PROVIDER_TITLE_VIEW_ADDRESS}}</h2>
				<span class="preview-description">{{PROVIDER_SUBTITLE_VIEW_ADDRESS}}</span>
			</div>
			<div class="show-mobile">
				<div class="preview-info-mobile-button" data-jcs-element="preview__page__open__info__poa">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_ADDRESS}}</span>
				</div>

				<div class="preview-info-inner-container" data-jcs-element="preview__info__container__poa">
					<span class="preview-info-title">{{PREVIEW_INFO_TITLE_ADDRESS}}</span>
					<div class="id-example-container">
						<div class="id-example-container-row">
							<div class="id-example id-example-good">
								<img class="sample-image sample-image-success" src="assets/images/address-good.svg" />
								<div class="separating-rectangle separating-rectangle-success"></div>
								<div class="preview-label preview-label-success">
									<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
									<span>{{PREVIEW_INFO_GOOD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_ADDRESS}}</span>
							</div>
							<div class="id-example id-example-angle-check">
								<img class="sample-image sample-image-error" src="assets/images/address-angled.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_ADDRESS}}</span>
							</div>
						</div>
						<div class="id-example-container-row">
							<div class="id-example id-example-cover-check">
								<img class="sample-image sample-image-error" src="assets/images/address-covered.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_ADDRESS}}</span>
							</div>
							<div class="id-example id-example-blur-check">
								<img class="sample-image sample-image-error" src="assets/images/address-blurred.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_ADDRESS}}</span>
							</div>
						</div>
					</div>
					<div class="modal-close-button close-preview-info-button" data-jcs-element="preview__page__close__info__poa">
						<svg width="24" height="24" class="help-button-modal-close" viewBox="0 0 24 24" fill="none"
							xmlns="http://www.w3.org/2000/svg">
							<circle cx="12" cy="12" r="11" stroke-width="2" stroke-linecap="round" />
							<path d="M16 16L8 8M8 16L16 8" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
						</svg>
						<p class="action-caption">{{REQUIRED_ACTION_Close}}</p>
					</div>
				</div>
			</div>
			<div class="show-desktop preview-info-container">
				<div class="preview-info-title">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_ADDRESS}}</span>
				</div>
				<ul class="id-example-container">
					<li class="id-example id-example-good">
						<img class="sample-image sample-image-success" src="assets/images/address-good.svg" />
						<div class="separating-rectangle separating-rectangle-success"></div>
						<div class="preview-label preview-label-success">
							<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
							<span>{{PREVIEW_INFO_GOOD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_ADDRESS}}</span>
					</li>
					<li class="id-example id-example-angle-check">
						<img class="sample-image sample-image-error" src="assets/images/address-angled.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_ADDRESS}}</span>
					</li>
					<li class="id-example id-example-cover-check">
						<img class="sample-image sample-image-error" src="assets/images/address-covered.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_ADDRESS}}</span>
					</li>
					<li class="id-example id-example-blur-check">
						<img class="sample-image sample-image-error" src="assets/images/address-blurred.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_ADDRESS}}</span>
					</li>
				</ul>
			</div>
		</div>

<div data-jcs-element="covid__preview">
			<div class="preview-title-container">
				<h2>{{PROVIDER_TITLE_VIEW_COVID}}</h2>
				<span class="preview-description">{{PROVIDER_SUBTITLE_VIEW_COVID}}</span>
			</div>
			<div class="show-mobile">
				<div class="preview-info-mobile-button" data-jcs-element="preview__page__open__info__covid">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_COVID}}</span>
				</div>

				<div class="preview-info-inner-container" data-jcs-element="preview__info__container__covid">
					<span class="preview-info-title">{{PREVIEW_INFO_TITLE_COVID}}</span>
					<div class="id-example-container">
						<div class="id-example-container-row">
							<div class="id-example id-example-good">
								<img class="sample-image sample-image-success" src="assets/images/address-good.svg" />
								<div class="separating-rectangle separating-rectangle-success"></div>
								<div class="preview-label preview-label-success">
									<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
									<span>{{PREVIEW_INFO_GOOD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_COVID}}</span>
							</div>
							<div class="id-example id-example-angle-check">
								<img class="sample-image sample-image-error" src="assets/images/address-angled.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_COVID}}</span>
							</div>
						</div>
						<div class="id-example-container-row">
							<div class="id-example id-example-cover-check">
								<img class="sample-image sample-image-error" src="assets/images/address-covered.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_COVID}}</span>
							</div>
							<div class="id-example id-example-blur-check">
								<img class="sample-image sample-image-error" src="assets/images/address-blurred.svg" />
								<div class="separating-rectangle separating-rectangle-error"></div>
								<div class="preview-label preview-label-error">
									<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
									<span>{{PREVIEW_INFO_BAD}}</span>
								</div>
								<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_COVID}}</span>
							</div>
						</div>
					</div>
					<div class="modal-close-button close-preview-info-button" data-jcs-element="preview__page__close__info__covid">
						<svg width="24" height="24" class="help-button-modal-close" viewBox="0 0 24 24" fill="none"
							xmlns="http://www.w3.org/2000/svg">
							<circle cx="12" cy="12" r="11" stroke-width="2" stroke-linecap="round" />
							<path d="M16 16L8 8M8 16L16 8" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
						</svg>
						<p class="action-caption">{{REQUIRED_ACTION_Close}}</p>
					</div>
				</div>
			</div>
			<div class="show-desktop preview-info-container">
				<div class="preview-info-title">
					<img class="preview-info-icon" src="assets/images/info-icon.svg" />
					<span class="preview-info-main-text">{{PREVIEW_INFO_TITLE_COVID}}</span>
				</div>
				<ul class="id-example-container">
					<li class="id-example id-example-good">
						<img class="sample-image sample-image-success" src="assets/images/address-good.svg" />
						<div class="separating-rectangle separating-rectangle-success"></div>
						<div class="preview-label preview-label-success">
							<img class="preview-label-icon" src="assets/images/green-check-icon.svg" />
							<span>{{PREVIEW_INFO_GOOD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_GOOD_EXAMPLE_COVID}}</span>
					</li>
					<li class="id-example id-example-angle-check">
						<img class="sample-image sample-image-error" src="assets/images/address-angled.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_ANGLE_COVID}}</span>
					</li>
					<li class="id-example id-example-cover-check">
						<img class="sample-image sample-image-error" src="assets/images/address-covered.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_COVER_COVID}}</span>
					</li>
					<li class="id-example id-example-blur-check">
						<img class="sample-image sample-image-error" src="assets/images/address-blurred.svg" />
						<div class="separating-rectangle separating-rectangle-error"></div>
						<div class="preview-label preview-label-error">
							<img class="preview-label-icon" src="assets/images/red-x-icon.svg" />
							<span>{{PREVIEW_INFO_BAD}}</span>
						</div>
						<span class="preview-info-example-text ">{{PREVIEW_INFO_BAD_EXAMPLE_BLUR_COVID}}</span>
					</li>
				</ul>
			</div>
		</div>


		
		<div class="image-preview--container image-preview-container">
			<img data-jcs-element="view__preview" class="image-preview">
			<div data-jcs-element="overlay_viewfinder" class="camera-viewfinder--overlay--transparent">
				<div class="loading-animation-container" data-jcs-element="loading-animation">
				</div>
			</div>
		</div>
		<div class="notification notification-blue" data-jcs-element="attempt__count__container">
			<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 16" fill="none">
				<path fill-rule="evenodd" clip-rule="evenodd"
					d="M0.25 8C0.25 3.71979 3.71979 0.25 8 0.25H8.00081C12.2787 0.254627 15.7454 3.72135 15.75 7.99919L15.75 8C15.75 12.2802 12.2802 15.75 8 15.75C3.71979 15.75 0.25 12.2802 0.25 8ZM7.99958 1.75C4.54799 1.75023 1.75 4.54836 1.75 8C1.75 11.4518 4.54822 14.25 8 14.25C11.4515 14.25 14.2496 11.4522 14.25 8.00081C14.2463 4.55026 11.4501 1.75394 7.99958 1.75ZM8 5C7.44772 5 7 4.55228 7 4C7 3.44772 7.44772 3 8 3C8.55229 3 9 3.44772 9 4C9 4.55228 8.55229 5 8 5ZM7 6.25C6.58579 6.25 6.25 6.58579 6.25 7C6.25 7.41421 6.58579 7.75 7 7.75H7.25V11.25H6.5C6.08579 11.25 5.75 11.5858 5.75 12C5.75 12.4142 6.08579 12.75 6.5 12.75H8H9.5C9.91421 12.75 10.25 12.4142 10.25 12C10.25 11.5858 9.91421 11.25 9.5 11.25H8.75V7C8.75 6.58579 8.41421 6.25 8 6.25H7Z"
					fill="#4A88C6" />
			</svg>
			<p>{{ATTEMPTS_REMAINING}}<span
					data-jcs-element="attempt__counter"></span></p>
		</div>
		<div class="button-container button-actions-container">
			<button data-jcs-element="view__retry" class="button button--secondary button-full-xs"
			aria-label="{{VIEW_RETRY}}">
				{{VIEW_RETRY}}
			</button>
			<button data-jcs-element="view__upload" class="button button--primary button-full-xs"
				aria-label="{{VIEW_UPLOAD}}">
				{{VIEW_UPLOAD}}
			</button>
		</div>
		<div data-jcs-element="view__status">
			<p data-jcs-element="view__status__message"></p>
		</div>
	</div>
</div>
```

#### Elements

| Name | Purpose | Required |
| - | - | - |
| identity__document__preview | Contains most of the elements in the upper part of the preview page for document step | No |
| preview__page__open__info__id | Displays the main title text for mobile for document step | No |
| preview__info__container__id | Displays the main title text for desktop for document step | No |
| preview__page__close__info__id | Displays the 'close' button for mobile help modal for document step | No |
| facematch__preview | Contains most of the elements in the upper part of the preview page for facematch step | No |
| preview__page__open__info__fm | Displays the main title text for mobile for facematch step | No |
| preview__info__container__fm | Displays the main title text for desktop for facematch step | No |
| preview__page__close__info__fm | Displays the 'close' button for mobile help modal for facematch step | No |
| preview__page__open__info__poa | Displays the 'open' button for mobile help modal for PoA step | No |
| preview__info__container__poa | Displays the main title text for desktop for PoA step | No |
| preview__page__close__info__poa | Displays the 'close' button for mobile help modal for PoA step | No |
| view__preview | Displays the captured image | Yes |
| overlay_viewfinder | Displays an overlay above the preview with the loading animation | No |
| loading-animation | Displays the loading animation | No |
| attempt__count__container | Contains the information related to triple scan attempt counting | No |
| attempt__counter | Displays the remaining capture attempts to the user | No |
| view__retry | Retry the capture | Yes |
| view__upload | Upload the current image | Yes |
| view__status | The outer container for the status messages | No |
| view__status__message | The message container for the status messages | No |

### Templates.SmartCapture

This template is used to take the user through the smart capture process using
the underlying internal ides web assembly services.

#### Configuration

| Key | Value |
| - | - |
| Type | TemplateType.String |
| Processor | PreProcessor.Mustache |

#### Default Provider

```html mustache
<div class="dark-inner-provider-container">
	<div>
		<form class="camera-options--container">
			<select data-jcs-element="camera__select" class="camera-choices camera-choices-half-s camera-choices-full-xs control control--select"></select>
		</form>
		<div class="help-container">
			<div class="camera-viewfinder--container">
				<div data-jcs-element="camera__viewfinder__inner__container" class="camera-viewfinder--inner-container" style="position: relative;">
					<canvas data-jcs-element="camera__viewfinder" class="camera-viewfinder" style="position: relative; left: 0; top: 0; z-index: 0;"></canvas>
					<div data-jcs-element="animation__viewfinder" class="camera-viewfinder" style="position: absolute; left: 0; top: 0; z-index: 1;"></div>
					<div data-jcs-element="overlay_viewfinder" class="camera-viewfinder--overlay"></div>
					<div data-jcs-element="overlay_viewfinder" class="camera-viewfinder--overlay--transparent">
						<p class="camera-viewfinder--overlay-text">
								<button data-jcs-element="camera__capture" class="button button--primary camera-button">{{CAMERA_CAPTURE_START_BUTTON}}</button>
						</p>
					</div>
					<svg class="canvas-overlay" data-jcs-element="canvas__condition__error" width="48" height="48" viewBox="0 0 48 48" fill="none" xmlns="http://www.w3.org/2000/svg">
						<circle cx="24" cy="24" r="22" fill="white" stroke="#D87212" stroke-width="4" stroke-linecap="round"/>
						<path d="M24 14V26M24 34V33.8972" stroke="#D87212" stroke-width="4" stroke-linecap="round" stroke-linejoin="round"/>
					</svg>
				</div>
			</div>
			<div class="uploading--container">
				<span data-jcs-element="camera__condition__uploading" class="camera-condition camera-condition--uploading">{{CAMERA_CONDITION_UPLOADING}}</span>
			</div>
			<div data-jcs-element="camera__conditions" class="camera-conditions--container">
				<div class="camera-conditions--container-list show-desktop">
					<div data-jcs-element="camera__condition__glare" class="camera-conditions--container-list-item camera-condition--glare">
						<svg width="14" height="14" viewBox="0 0 14 14" fill="none" xmlns="http://www.w3.org/2000/svg">
							<path fill-rule="evenodd" clip-rule="evenodd" d="M6.65385 0C6.84323 0 7.01636 0.107001 7.10106 0.276393L13.2549 12.5841C13.3324 12.7391 13.3241 12.9231 13.233 13.0706C13.1419 13.218 12.981 13.3077 12.8077 13.3077H0.500001C0.326712 13.3077 0.165778 13.218 0.0746751 13.0706C-0.0164282 12.9231 -0.0247102 12.7391 0.0527869 12.5841L6.20663 0.276393C6.29133 0.107001 6.46446 0 6.65385 0ZM11.9987 12.3077L6.65385 1.61803L1.30902 12.3077H11.9987ZM6.65403 4.92308C6.93018 4.92308 7.15403 5.14693 7.15403 5.42308V7.88461C7.15403 8.16076 6.93018 8.38461 6.65403 8.38461C6.37789 8.38461 6.15403 8.16076 6.15403 7.88461V5.42308C6.15403 5.14693 6.37789 4.92308 6.65403 4.92308ZM6.65381 10.9615C6.99368 10.9615 7.26919 10.686 7.26919 10.3462C7.26919 10.0063 6.99368 9.73077 6.65381 9.73077C6.31394 9.73077 6.03842 10.0063 6.03842 10.3462C6.03842 10.686 6.31394 10.9615 6.65381 10.9615Z" fill="#F88618"/>
						</svg>
						<span>
							{{CAMERA_CONDITION_GLARE}}
						</span>
					</div>
					<div data-jcs-element="camera__condition__blur" class="camera-conditions--container-list-item camera-condition--blur">
						<svg width="14" height="14" viewBox="0 0 14 14" fill="none" xmlns="http://www.w3.org/2000/svg">
							<path fill-rule="evenodd" clip-rule="evenodd" d="M6.65385 0C6.84323 0 7.01636 0.107001 7.10106 0.276393L13.2549 12.5841C13.3324 12.7391 13.3241 12.9231 13.233 13.0706C13.1419 13.218 12.981 13.3077 12.8077 13.3077H0.500001C0.326712 13.3077 0.165778 13.218 0.0746751 13.0706C-0.0164282 12.9231 -0.0247102 12.7391 0.0527869 12.5841L6.20663 0.276393C6.29133 0.107001 6.46446 0 6.65385 0ZM11.9987 12.3077L6.65385 1.61803L1.30902 12.3077H11.9987ZM6.65403 4.92308C6.93018 4.92308 7.15403 5.14693 7.15403 5.42308V7.88461C7.15403 8.16076 6.93018 8.38461 6.65403 8.38461C6.37789 8.38461 6.15403 8.16076 6.15403 7.88461V5.42308C6.15403 5.14693 6.37789 4.92308 6.65403 4.92308ZM6.65381 10.9615C6.99368 10.9615 7.26919 10.686 7.26919 10.3462C7.26919 10.0063 6.99368 9.73077 6.65381 9.73077C6.31394 9.73077 6.03842 10.0063 6.03842 10.3462C6.03842 10.686 6.31394 10.9615 6.65381 10.9615Z" fill="#F88618"/>
						</svg>
						<span>
							{{CAMERA_CONDITION_BLUR}}
						</span>
					</div>
					<div data-jcs-element="camera__condition__low__resolution" class="camera-conditions--container-list-item camera-condition--low-resolution">
						<svg width="14" height="14" viewBox="0 0 14 14" fill="none" xmlns="http://www.w3.org/2000/svg">
							<path fill-rule="evenodd" clip-rule="evenodd" d="M6.65385 0C6.84323 0 7.01636 0.107001 7.10106 0.276393L13.2549 12.5841C13.3324 12.7391 13.3241 12.9231 13.233 13.0706C13.1419 13.218 12.981 13.3077 12.8077 13.3077H0.500001C0.326712 13.3077 0.165778 13.218 0.0746751 13.0706C-0.0164282 12.9231 -0.0247102 12.7391 0.0527869 12.5841L6.20663 0.276393C6.29133 0.107001 6.46446 0 6.65385 0ZM11.9987 12.3077L6.65385 1.61803L1.30902 12.3077H11.9987ZM6.65403 4.92308C6.93018 4.92308 7.15403 5.14693 7.15403 5.42308V7.88461C7.15403 8.16076 6.93018 8.38461 6.65403 8.38461C6.37789 8.38461 6.15403 8.16076 6.15403 7.88461V5.42308C6.15403 5.14693 6.37789 4.92308 6.65403 4.92308ZM6.65381 10.9615C6.99368 10.9615 7.26919 10.686 7.26919 10.3462C7.26919 10.0063 6.99368 9.73077 6.65381 9.73077C6.31394 9.73077 6.03842 10.0063 6.03842 10.3462C6.03842 10.686 6.31394 10.9615 6.65381 10.9615Z" fill="#F88618"/>
						</svg>
						<span>
							{{CAMERA_CONDITION_LOW_RESOLUTION}}
						</span>
					</div>
					<div data-jcs-element="camera__condition__alignment" class="camera-conditions--container-list-item camera-condition--alignment">
						<svg width="14" height="14" viewBox="0 0 14 14" fill="none" xmlns="http://www.w3.org/2000/svg">
							<path fill-rule="evenodd" clip-rule="evenodd" d="M6.65385 0C6.84323 0 7.01636 0.107001 7.10106 0.276393L13.2549 12.5841C13.3324 12.7391 13.3241 12.9231 13.233 13.0706C13.1419 13.218 12.981 13.3077 12.8077 13.3077H0.500001C0.326712 13.3077 0.165778 13.218 0.0746751 13.0706C-0.0164282 12.9231 -0.0247102 12.7391 0.0527869 12.5841L6.20663 0.276393C6.29133 0.107001 6.46446 0 6.65385 0ZM11.9987 12.3077L6.65385 1.61803L1.30902 12.3077H11.9987ZM6.65403 4.92308C6.93018 4.92308 7.15403 5.14693 7.15403 5.42308V7.88461C7.15403 8.16076 6.93018 8.38461 6.65403 8.38461C6.37789 8.38461 6.15403 8.16076 6.15403 7.88461V5.42308C6.15403 5.14693 6.37789 4.92308 6.65403 4.92308ZM6.65381 10.9615C6.99368 10.9615 7.26919 10.686 7.26919 10.3462C7.26919 10.0063 6.99368 9.73077 6.65381 9.73077C6.31394 9.73077 6.03842 10.0063 6.03842 10.3462C6.03842 10.686 6.31394 10.9615 6.65381 10.9615Z" fill="#F88618"/>
						</svg>
						<span>
							{{CAMERA_CONDITION_ALIGNMENT}}
						</span>
					</div>
				</div>
				<div class="camera-conditions--container-detail" data-jcs-element="camera__condition__detail">
					<div class="camera-conditions--container-detail-animation" data-jcs-element="camera__condition__detail__animation">
					</div>
					<div class="camera-conditions--container-detail-description">
						<div class="camera-conditions--container-detail-description-title">
							<svg width="22" height="22" viewBox="0 0 22 22" fill="none" xmlns="http://www.w3.org/2000/svg">
								<path fill-rule="evenodd" clip-rule="evenodd" d="M11 0C11.3788 0 11.725 0.214002 11.8944 0.552786L21.8944 20.5528C22.0494 20.8628 22.0329 21.2309 21.8507 21.5257C21.6684 21.8205 21.3466 22 21 22H1C0.653423 22 0.331557 21.8205 0.14935 21.5257C-0.0328564 21.2309 -0.0494204 20.8628 0.105574 20.5528L10.1056 0.552786C10.275 0.214002 10.6212 0 11 0ZM19.382 20L11 3.23607L2.61804 20H19.382ZM11 8C11.5523 8 12 8.44771 12 9V13C12 13.5523 11.5523 14 11 14C10.4477 14 10 13.5523 10 13V9C10 8.44771 10.4477 8 11 8ZM11 18C11.5523 18 12 17.5523 12 17C12 16.4477 11.5523 16 11 16C10.4477 16 10 16.4477 10 17C10 17.5523 10.4477 18 11 18Z" fill="#F88618"/>
							</svg>
							<span class="camera-conditions--container-detail-description-title-text" data-jcs-element="camera__condition__detail__description__title">
							</span>
						</div>
						<div class="camera-conditions--container-detail-description-hint" data-jcs-element="camera__condition__detail__description__hint">
						</div>
					</div>
				</div>
			</div	>
		<div data-jcs-element="info__journey__action__container" class="camera-capture--caption">
			<p data-jcs-element="info__journey__action__text"></p>
		</div>
		<div>

		</div>
		<form class="button-camera-container button-container" data-jcs-element="camera__capture__hint">
			<p class="camera-capture--caption">
				{{CAMERA_CAPTURE_CAPTION}}
			</p>
		</form>
		<div class="help-inner-container" data-jcs-element="capture__help__container">
			<h2>{{HELP_MODAL_AUTOCAPTURE_TITLE}}</h2>
			<p>{{HELP_MODAL_AUTOCAPTURE_SUBTITLE}}</p>
			<b>{{HELP_MODAL_AUTOCAPTURE_TIPS}}</b>
			<div class="help-images-container">
				<div class="help-image-inner-container">
					<div class="help-image" data-jcs-element="modal__glare__animation__container"></div>
					<div class="help-text">
						<b>{{HELP_MODAL_AUTOCAPTURE_GLARE_TITLE}}</b>
						<p>{{HELP_MODAL_AUTOCAPTURE_GLARE_SUBTITLE}}</p>
					</div>
				</div>
				<div class="help-image-inner-container">
					<div class="help-image" data-jcs-element="modal__low__res__animation__container"></div>
					<div class="help-text">
						<b>{{HELP_MODAL_AUTOCAPTURE_FAR_TITLE}}</b>
						<p>{{HELP_MODAL_AUTOCAPTURE_FAR_SUBTITLE}}</p>
					</div>
				</div>
				<div class="help-image-inner-container">
					<div class="help-image" data-jcs-element="modal__blur__animation__container"></div>
					<div class="help-text">
						<b>{{HELP_MODAL_AUTOCAPTURE_BLUR_TITLE}}</b>
						<p>{{HELP_MODAL_AUTOCAPTURE_BLUR_SUBTITLE}}</p>
					</div>
				</div>
			</div>
			<div class="modal-footer modal-bottom modal-actions-container">
				<p data-jcs-element="capture__modal__manual__capture__text">{{HELP_MODAL_AUTOCAPTURE_MANUAL_CAPTURE}}</p>
				<div class="actions-container">
					<div class="help-button-container" data-jcs-element="capture__modal__camera__manual__capture">
						<label class="control control--switch manual--mode--switch" aria-label="switch">
							<input type="checkbox" data-jcs-element="capture__modal__camera__manual__capture__check" />
							<div class="control__switch control__switch__blue">
								<div class="icon ico-checkmark-16 control__switchIcon text-black">
									<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg" class="icon ico-checkmark-16 control__switchIcon text-black"><path d="M13 4L6 12L3 9" stroke="white" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"></path></svg>
								</div>
							</div>
						</label>
						<p class="action-caption">{{HELP_MODAL_AUTOCAPTURE_TURN_MANUAL_CAPTURE}}</p>
					</div>
					<div class="spacer">
					</div>
					<div class="help-button-container" data-jcs-element="smart__capture__modal__close__help">
						<svg width="24" height="24" class="help-button-modal-close" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
							<circle cx="12" cy="12" r="11" stroke-width="2" stroke-linecap="round"/>
							<path d="M16 16L8 8M8 16L16 8" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
						</svg>
						<p class="action-caption">{{REQUIRED_ACTION_Close}}</p>
					</div>
				</div>
			</div>
		</div>
		<div class="notification notification-blue" data-jcs-element="attempt__count__container">
			<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 16" fill="none">
				<path fill-rule="evenodd" clip-rule="evenodd" d="M0.25 8C0.25 3.71979 3.71979 0.25 8 0.25H8.00081C12.2787 0.254627 15.7454 3.72135 15.75 7.99919L15.75 8C15.75 12.2802 12.2802 15.75 8 15.75C3.71979 15.75 0.25 12.2802 0.25 8ZM7.99958 1.75C4.54799 1.75023 1.75 4.54836 1.75 8C1.75 11.4518 4.54822 14.25 8 14.25C11.4515 14.25 14.2496 11.4522 14.25 8.00081C14.2463 4.55026 11.4501 1.75394 7.99958 1.75ZM8 5C7.44772 5 7 4.55228 7 4C7 3.44772 7.44772 3 8 3C8.55229 3 9 3.44772 9 4C9 4.55228 8.55229 5 8 5ZM7 6.25C6.58579 6.25 6.25 6.58579 6.25 7C6.25 7.41421 6.58579 7.75 7 7.75H7.25V11.25H6.5C6.08579 11.25 5.75 11.5858 5.75 12C5.75 12.4142 6.08579 12.75 6.5 12.75H8H9.5C9.91421 12.75 10.25 12.4142 10.25 12C10.25 11.5858 9.91421 11.25 9.5 11.25H8.75V7C8.75 6.58579 8.41421 6.25 8 6.25H7Z" fill="#4A88C6"/>
			</svg>
			<p>{{ATTEMPTS_REMAINING}}<span data-jcs-element="attempt__counter"></span></p>
		</div>
	</div>
	<div data-jcs-element="view__status">
			<p data-jcs-element="view__status__message"></p>
	</div>
	<div class="actions-container">
		<div data-jcs-element="camera__manual__capture">
			<label class="control control--switch manual--mode--switch" aria-label="switch">
				<input type="checkbox" data-jcs-element="camera__manual__capture__check" />
				<div class="control__switch">
					<div class="icon ico-checkmark-16 control__switchIcon text-black">
						<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg" class="icon ico-checkmark-16 control__switchIcon text-black"><path d="M13 4L6 12L3 9" stroke="rgb(0, 13, 26)" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"></path></svg>
					</div>
				</div>
			</label>
			<p class="action-caption">{{HELP_MODAL_AUTOCAPTURE_TURN_MANUAL_CAPTURE}}</p>
		</div>
		<div class="spacer">
		</div>
		<div>
			<div class="help-button-container" data-jcs-element="smart__capture__close__help">
				<svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
					<circle cx="12" cy="12" r="11" stroke="white" stroke-width="2" stroke-linecap="round"/>
					<path d="M16 16L8 8M8 16L16 8" stroke="white" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
				</svg>
				<p class="action-caption">{{REQUIRED_ACTION_Close}}</p>
			</div>
			<div class="help-button-container" data-jcs-element="smart__capture__open__help">
				<svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
					<path fill-rule="evenodd" clip-rule="evenodd" d="M12 2C6.47715 2 2 6.47715 2 12C2 17.5228 6.47715 22 12 22C17.5228 22 22 17.5228 22 12C22 6.47715 17.5228 2 12 2ZM0 12C0 5.37258 5.37258 0 12 0C18.6274 0 24 5.37258 24 12C24 18.6274 18.6274 24 12 24C5.37258 24 0 18.6274 0 12ZM12 7C11.0445 7 10 7.68564 10 9C10 9.55228 9.55229 10 9 10C8.44771 10 8 9.55228 8 9C8 6.31436 10.227 5 12 5C14.2091 5 16 6.79086 16 9C16 10.8638 14.7252 12.4299 13 12.874V12.8746V12.8787V12.8827V12.8868V12.8909V12.895V12.8991V12.9031V12.9072V12.9113V12.9153V12.9194V12.9234V12.9275V12.9315V12.9355V12.9396V12.9436V12.9476V12.9517V12.9557V12.9597V12.9637V12.9677V12.9717V12.9757V12.9796V12.9836V12.9876V12.9915V12.9955V12.9995V13.0034V13.0073V13.0113V13.0152V13.0191V13.023V13.0269V13.0308V13.0347V13.0386V13.0425V13.0463V13.0502V13.0541V13.0579V13.0617V13.0656V13.0694V13.0732V13.077V13.0808V13.0846V13.0884V13.0922V13.0959V13.0997V13.1034V13.1071V13.1109V13.1146V13.1183V13.122V13.1257V13.1294V13.133V13.1367V13.1403V13.144V13.1476V13.1512V13.1548V13.1584V13.162V13.1656V13.1691V13.1727V13.1762V13.1797V13.1832V13.1868V13.1902V13.1937V13.1972V13.2007V13.2041V13.2075V13.211V13.2144V13.2178V13.2211V13.2245V13.2279V13.2312V13.2345V13.2379V13.2412V13.2445V13.2477V13.251V13.2542V13.2575V13.2607V13.2639V13.2671V13.2703V13.2735V13.2766V13.2797V13.2829V13.286V13.2891V13.2921V13.2952V13.2983V13.3013V13.3043V13.3073V13.3103V13.3133V13.3162V13.3191V13.3221V13.325V13.3279V13.3307V13.3336V13.3364V13.3392V13.342V13.3448V13.3476V13.3504V13.3531V13.3558V13.3585V13.3612V13.3639V13.3665V13.3691V13.3718V13.3744V13.3769V13.3795V13.382V13.3845V13.387V13.3895V13.392V13.3944V13.3969V13.3993V13.4017V13.404V13.4064V13.4087V13.411V13.4133V13.4156V13.4178V13.42V13.4223V13.4244V13.4266V13.4288V13.4309V13.433V13.4351V13.4371V13.4392V13.4412V13.4432V13.4452V13.4471V13.4491V13.451V13.4529V13.4547V13.4566V13.4584V13.4602V13.462V13.4637V13.4655V13.4672V13.4689V13.4705V13.4722V13.4738V13.4754V13.477V13.4785V13.48V13.4815V13.483V13.4845V13.4859V13.4873V13.4887V13.49V13.4914V13.4927V13.494V13.4952V13.4965V13.4977V13.4988V13.5C13 14.0523 12.5523 14.5 12 14.5C11.4477 14.5 11 14.0523 11 13.5V13.4988V13.4977V13.4965V13.4952V13.494V13.4927V13.4914V13.49V13.4887V13.4873V13.4859V13.4845V13.483V13.4815V13.48V13.4785V13.477V13.4754V13.4738V13.4722V13.4705V13.4689V13.4672V13.4655V13.4637V13.462V13.4602V13.4584V13.4566V13.4547V13.4529V13.451V13.4491V13.4471V13.4452V13.4432V13.4412V13.4392V13.4371V13.4351V13.433V13.4309V13.4288V13.4266V13.4244V13.4223V13.42V13.4178V13.4156V13.4133V13.411V13.4087V13.4064V13.404V13.4017V13.3993V13.3969V13.3944V13.392V13.3895V13.387V13.3845V13.382V13.3795V13.3769V13.3744V13.3718V13.3691V13.3665V13.3639V13.3612V13.3585V13.3558V13.3531V13.3504V13.3476V13.3448V13.342V13.3392V13.3364V13.3336V13.3307V13.3279V13.325V13.3221V13.3191V13.3162V13.3133V13.3103V13.3073V13.3043V13.3013V13.2983V13.2952V13.2921V13.2891V13.286V13.2829V13.2797V13.2766V13.2735V13.2703V13.2671V13.2639V13.2607V13.2575V13.2542V13.251V13.2477V13.2445V13.2412V13.2379V13.2345V13.2312V13.2279V13.2245V13.2211V13.2178V13.2144V13.211V13.2075V13.2041V13.2007V13.1972V13.1937V13.1902V13.1868V13.1832V13.1797V13.1762V13.1727V13.1691V13.1656V13.162V13.1584V13.1548V13.1512V13.1476V13.144V13.1403V13.1367V13.133V13.1294V13.1257V13.122V13.1183V13.1146V13.1109V13.1071V13.1034V13.0997V13.0959V13.0922V13.0884V13.0846V13.0808V13.077V13.0732V13.0694V13.0656V13.0617V13.0579V13.0541V13.0502V13.0463V13.0425V13.0386V13.0347V13.0308V13.0269V13.023V13.0191V13.0152V13.0113V13.0073V13.0034V12.9995V12.9955V12.9915V12.9876V12.9836V12.9796V12.9757V12.9717V12.9677V12.9637V12.9597V12.9557V12.9517V12.9476V12.9436V12.9396V12.9355V12.9315V12.9275V12.9234V12.9194V12.9153V12.9113V12.9072V12.9031V12.8991V12.895V12.8909V12.8868V12.8827V12.8787V12.8746V12.8705V12.8664V12.8623V12.8582V12.8541V12.85V12.8459V12.8418V12.8376V12.8335V12.8294V12.8253V12.8212V12.8171V12.8129V12.8088V12.8047V12.8006V12.7964V12.7923V12.7882V12.784V12.7799V12.7758V12.7716V12.7675V12.7634V12.7592V12.7551V12.751V12.7469V12.7427V12.7386V12.7345V12.7303V12.7262V12.7221V12.718V12.7138V12.7097V12.7056V12.7015V12.6973V12.6932V12.6891V12.685V12.6809V12.6768V12.6727V12.6686V12.6645V12.6604V12.6563V12.6522V12.6481V12.644V12.6399V12.6358V12.6317V12.6277V12.6236V12.6195V12.6155V12.6114V12.6073V12.6033V12.5992V12.5952V12.5912V12.5871V12.5831V12.5791V12.575V12.571V12.567V12.563V12.559V12.555V12.551V12.547V12.5431V12.5391V12.5351V12.5312V12.5272V12.5233V12.5193V12.5154V12.5114V12.5075V12.5036V12.4997V12.4958V12.4919V12.488V12.4841V12.4803V12.4764V12.4725V12.4687V12.4648V12.461V12.4572V12.4534V12.4496V12.4458V12.442V12.4382V12.4344V12.4306V12.4269V12.4231V12.4194V12.4157V12.4119V12.4082V12.4045V12.4008V12.3971V12.3935V12.3898V12.3862V12.3825V12.3789V12.3753V12.3716V12.368V12.3645V12.3609V12.3573V12.3538V12.3502V12.3467V12.3432V12.3396V12.3361V12.3327V12.3292V12.3257V12.3223V12.3188V12.3154V12.312V12.3086V12.3052V12.3018V12.2984V12.2951V12.2917V12.2884V12.2851V12.2818V12.2785V12.2753V12.272V12.2687V12.2655V12.2623V12.2591V12.2559V12.2527V12.2496V12.2464V12.2433V12.2402V12.2371V12.234V12.2309V12.2279V12.2248V12.2218V12.2188V12.2158V12.2128V12.2099V12.2069V12.204V12.2011V12.1982V12.1953V12.1924V12.1896V12.1867V12.1839V12.1811V12.1784V12.1756V12.1728V12.1701V12.1674V12.1647V12.162V12.1594V12.1567V12.1541V12.1515V12.1489V12.1463V12.1438V12.1413V12.1388V12.1363V12.1338V12.1313V12.1289V12.1265V12.1241V12.1217V12.1193V12.117V12.1147V12.1124V12.1101V12.1078V12.1056V12.1034V12.1012V12.099V12.0968V12.0947V12.0926V12.0905V12.0884V12.0864V12.0843V12.0823V12.0803V12.0783V12.0764V12.0745V12.0726V12.0707V12.0688V12.067V12.0652V12.0634V12.0616V12.0599V12.0581V12.0564V12.0548V12.0531V12.0515V12.0499V12.0483V12.0467V12.0452V12.0437V12.0422V12.0407V12.0393V12.0378V12.0365V12.0351V12.0337V12.0324V12.0311V12.0298V12.0286V12.0274V12.0262V12.025V12.0239V12.0227V12.0216V12.0206V12.0195V12.0185V12.0175V12.0166V12.0156V12.0147V12.0138V12.013V12.0121V12.0113V12.0105V12.0098V12.0091V12.0084V12.0077V12.007V12.0064V12.0058V12.0053V12.0047V12.0042V12.0037V12.0033V12.0029V12.0025V12.0021V12.0018V12.0015V12.0012V12.0009V12.0007V12.0005V12.0004V12.0002V12.0001V12.0001V12L12 12H11C11 11.4477 11.4477 11 12 11C13.1046 11 14 10.1046 14 9C14 7.89543 13.1046 7 12 7ZM12 18C12.5523 18 13 17.5523 13 17C13 16.4477 12.5523 16 12 16C11.4477 16 11 16.4477 11 17C11 17.5523 11.4477 18 12 18Z" fill="white"/>
				</svg>
				<p class="action-caption">{{HELP_MODAL_NEED_HELP}}</p>
			</div>
		</div>
	</div>
</div>
```

#### Elements

| Name | Purpose | Required |
| - | - | - |
| info__journey__state | Displays the current state of the journey | No |
| info__journey__action | Displays the currently required action | No |
| info__journey__action__attempt | Displays how many attempts have been taken for the currently required action | No |
| info__journey__id | Displays the current journey's ID | No |
| info__journey__action__container | Contains the instructions for the current step | No
| info__journey__action__text | Displays the text of the instructions for the current step | No
| camera__status | Displays the camera permissions/usability status messages | No |
| camera__select | Displays the select element for picking the camera to use | No |
| camera__viewfinder | Displays the preview for the image capture | Yes |
| camera__viewfinder__inner__container | Contains all the elements of the viewfinder animations | Yes |
| overlay_viewfinder | Displays an overlay on top of the camera viewfinder to cover it when required | Yes |
| loading-animation | Displays an overlay on top of the camera viewfinder to show the loading icon when required | No |
| camera__conditions | Contains the overlays for camera condition alerts | No |
| camera__condition__capturing | Displays whilst the automatic capture process is running | No |
| camera__condition__alignment | Displays when the document isn't wholly in the viewfinder or is too close to the edges | No |
| camera__condition__blur | Displays when the image is too blurry | No |
| camera__condition__glare | Displays when the image has signs of glare present | No |
| camera__condition__low__resolution | Displays when the document in view isn't covering enough of the viewfinder | No |
| camera__condition__detail | Contains the information regarding the current camera condition | No |
| camera__condition__detail__animation | Contains the animation corresponding to the camera condition | No |
| camera__condition__detail__description__title | Contains the title corresponding to the current camera condition | No |
| camera__condition__detail__description__hint | Contains the hint corresponding to the current camera condition | No |
| camera__condition__uploading | Displays uploading animation | No |
| camera__capture | Begin automatic capture process | Yes |
| camera__capture__hint | Displays guidance to the user about the capture process |Yes|
| camera__manual__capture | Contains the manual capture toggle and label | Yes |
| camera__manual__capture__check | Displays the manual capture toggle | Yes |
| smart__capture__close__help | Displays the 'close' button for smart capture help modal | Yes |
| smart__capture__open__help | Displays the 'open' button for smart capture help modal | Yes |
| canvas__condition__error | Displays an error icon over the viewfinder whenever there are quality checks | No |
| capture__help__container |
| capture__modal__manual__capture__text | Displays assistance text suggesting the use of manual capture |
| capture__modal__camera__manual__capture | Contains the manual capture toggle and label for the help modal | No |
| capture__modal__camera__manual__capture__check |  Displays the manual capture toggle within the help modal | No |
| modal__glare__animation__container | Displays the glare check animation | No |
| modal__low__res__animation__container | Displays the low-resolution check animation | No |
| modal__blur__animation__container | Displays the blur check animation | No |
| smart__capture__modal__close__help | Displays the 'close' button for the manual capture help modal | No |
| attempt__count__container | Contains the information related to triple scan attempt counting| Yes |
| attempt__counter | Displays the remaining capture attempts to the user | Yes |

### Templates.JourneySelect

This template is used when presenting the user with a list of the available
journeys. The user will be able to see a list of the journeys which are
configured in the back office, and pick from one. If there is only one journey
available, this screen will be bypassed. If there are none configured, an error
message will be shown.

#### Configuration

| Key | Value |
| - | - |
| Type | TemplateType.String |
| Processor | PreProcessor.Mustache |

#### Default Provider

```html mustache
<h2 class="section-title">{{PROVIDER_TITLE_JOURNEY_SELECT}}</h2>
<div class="journey-select--choices">
	<select data-jcs-element="journey-select__choices" class="journey-choice"></select>
</div>
<form class="button-container">
	<input data-jcs-element="journey-select__continue" class="button button--primary" type="button" value="{{JOURNEY_SELECT_CONTINUE}}" />
</form>
```

#### Elements

| Name | Purpose | Required |
| - | - | - |
| journey-select__choices | Populated with a list of available journeys | Yes |
| journey-select__continue | Continues the process with the selected journey | Yes |

## Animations

For the animations mentioned in the default templates above, the animation
framework `lottie` has been used, available at [lottie.js](https://airbnb.io/lottie/#/).

To use lottie animations you need to add the reference to the package, load the
animation and play it:

```typescript
import * as lottie from 'lottie-web/build/player/lottie';
lottiePlayer.loadAnimation({
	renderer: 'svg',
	container: htmlContainer,
	loop,
	autoplay,
	path: animationPath,
	rendererSettings: settings
}).play();
```

The settings above indicate which kind of renderer is used to display the
animation in the provided container using the json lottie animation file indicated.

To update the animation, the json file can be modified or replaced. If it uses
the same name, no update will be needed in the templates nor providers.
