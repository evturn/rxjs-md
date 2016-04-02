### Creating Observables

 The create operator in the Rx.Observable object takes a callback that accepts an Observer as a parameter. That function defines how the Observable will emit values

```javascript
 var observable = Rx.Observable.create(function(observer) {
  observer.onNext('Sergio');
  observer.onNext('Tyrone');
  observer.onNext('Choiman');
  observer.onCompleted(); // We are done
});
```

### Observers listen to Observables

Whenever an event happens in an Observable, it calls the related method in all of its Observers

Observers have three methods:

* `onNext` The equivalent of `Update` in the Observer pattern. It is called when the Observable emits a new value. Notice how the name reflects the fact that we’re subscribed to sequences, not only to discrete values.

* `onCompleted` Signals that there is no more data available. After `onCompleted` is called, further calls to `onNext` will have no effect.

* `onError` Called when an error occurs in the Observable. After it is called, further calls to `onNext` will have no effect.

### Creating Observers

The create method in the Rx.Observer object takes functions for the onNext, onCompleted, and onError cases and returns an Observer instance. These three functions are optional, and you can decide which ones to include.

```javascript
var observer = Rx.Observer.create(function onNext(x) {
    console.log('Next: ' + x);
  }, function onError(err) {
    console.log('Error: ' + err);
  }, function onCompleted() {
    console.log('Completed');
});
```