# Advanced Angular Integration with Typescript

Throughout this document we will show developers how to integrate WJCS within an Angular component, we will be using Angular version 7.2 and typescript 3.2.2 (latest versions are recommended) to demonstrate the rich features of the WJCS library. We have used the `ng new` function from the Angular CLI to get started and therefore the Angular code demonstrated is not production ready. We would recommend using the sample code as a guide to import the WJCS library into your code.

## Initialising

To initialise WJCS, a new instance of the `JourneyContainer` must be created and the initialise function called on the instance within the `ngOnInit` function of your Angular component.

```typescript
ngOnInit() {
	this.container = new JourneyContainer({
		auth: false,
		backendUrl: 'https://poc.idscan.cloud:443',
		container: '#gbgIdScan',
		templates: angularTemplates,
		dictionary: textTranslation,
		onJourneyEvent: this.onJourneyEvent
		token: YOUR_API_TOKEN
});

this.container.initialize();
}
```

## HTML

WJCS will be rendered into the element referenced in the `container` property of the `JourneyContainer` constructor. In this case we will be setting this to `#gbgIdScan` as referenced in id property  of the div element in the below code snippet.

```html
<div class="content" id="gbgIdScan">
    Loading...
</div>
```

## Terminating

 To terminate the `JourneyContainer` instance the developer must call the terminate method on the `journeyContainer` variable created in the previous steps. It is recommended that the WJCS library is terminated when the `ngOnDestroy` function is triggered in the Angular components life cycle.

```typescript
 ngOnDestroy() {
    this.container.terminate();
 }
```
