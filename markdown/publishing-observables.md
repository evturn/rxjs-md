## Publishing Observables

Calling `publish` creates a new Observable that acts as a proxy to the original Observable. This makes a cold Observable become a hot Observable.

In order to achieve that, the new Observable subscribes itself to the original and pushes the values it receives to its subscribers.

A *published* Observable becomes what is called a `ConnectableObservable` which has a `connect` method that is called in order to start receiving values.

```javascript
const source = Rx.Observable.interval(1000); 
const publisher = source.publish();

// No values have been pushed yet
const observer1 = publisher.subscribe(x => console.log('Observer 1: ' + x));

// Starts publishing values
publisher.connect();

setTimeout(() => {
  // Receiving current values and not the entire sequence
  const observer2 = publisher.subscribe(x => console.log('Observer 2: ' + x));
}, 5000);
```

