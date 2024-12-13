# Advanced React Integration with JSS (via Aphrodite)

Throughout this document we will show developers how to integrate WJCS within a React component, we will be using react version 16.8 and typescript 3.3 (latest versions are recommended) to demonstrate the rich features of the WJCS library.

## Initialising

To initialise WJCS, a new instance of the `JourneyContainer` must be created and the initialise function called on the instance within the `componentDidMount` function of your React component.

 **_Javascript Implementation:_**

```javascript
componentDidMount() {
	this.journeyContainer = new JourneyContainer({
		auth: false,
		backendUrl: 'https://poc.idscan.cloud:443',
		container: this.idScanDisplayContainer,
		templates: this.templates,
		dictionary: this.dictionary,
		onJourneyEvent: this.onJourneyEvent,
		token: YOUR_API_TOKEN
	});

	this.container.initialize();
}
```

**_Typescript Implementation:_**

```typescript
componentDidMount(): void {
	this.container = new JourneyContainer({
		auth: false,
		backendUrl: 'https://poc.idscan.cloud:443',
		container: this.idScanDisplayContainer,
		templates: this.templates,
		dictionary: this.dictionary,
		onJourneyEvent: this.onJourneyEvent,
		token: YOUR_API_TOKEN
	});

	this.container.initialize();
}
```

## HTML

WJCS will be rendered into the element referenced in the `container` property of the `JourneyContainer` constructor. In this case we will be setting this to `this.idScanDisplayContainer` as referenced in the HTML below.

```javascript
render() {
	return (
		<div className="id-scan-content" ref={ref => this.idScanDisplayContainer = ref}>
			Loading...
		</div>
	);
}
```

## Terminating

 To terminate the `JourneyContainer` instance the developer must call the terminate method on the `journeyContainer` variable created in the previous steps. It is recommended that the WJCS library is terminated when the `componentWillUnmount` function is triggered in the React components life cycle.

 **_Javascript Implementation:_**

```javascript
componentWillUnmount() {
	this.journeyContainer.terminate();
}
```

**_Typescript Implementation:_**

```typescript
componentWillUnmount(): void {
	this.journeyContainer.terminate();
}
```
