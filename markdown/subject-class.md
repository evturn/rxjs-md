## Subject Class

A type that implements both Observer and Observable types. As an Observer, it can subscribe to Observables, and as an Observable it can produce values and have Observers subscribe to it.

* Here we create a new Subject and a source Observable that emits an integer every second.
* Then we subscribe the Subject to the Observable.
* Then we subscribe an Observer to the Subject itself (The Subject will now behave as an Observable).
* Then we make the Subject emit values of its own ('Our message #1', 'Our message #2').
* Finally, we get the Subject’s own messages followed by the proxied values from the source Observable.

```javascript
const subject = new Rx.Subject();

const source = Rx.Observable
  .interval(300)
  .map(v => 'Interval message #' + v)
  .take(5);

source.subscribe(subject);

const subscription = subject.subscribe(
  x  => console.log('onNext: ' + x),
  e  => console.log('onError: ' + e.message), 
  () => console.log('onCompleted');
);

subject.onNext('Our message #1');
subject.onNext('Our message #2');
setTimeout(() => subject.onCompleted(), 1000);
// onNext: Our message #1 
// onNext: Our message #2 
// onNext: Interval message #0 
// onNext: Interval message #1 
// onNext: Interval message #2 
// onCompleted
```


The values from the Observable come later since they are asynchronous, whereas the Subject’s own values were made immediate.

After one second a call to `onCompleted` on the Subject finishes the notifications to all subscriptions and overrides the `take` operator.

### `AsyncSubject`

Emits the last value of a sequence (only if the sequence completes) then caches it forever. Any Observer that subscribes after the value has been emitted will receive it right away. `AsyncSubject` is convenient for asynchronous operations that return a single value, such as Ajax requests.

```javascript
const delayedRange = Rx.Observable
  .range(0, 5)
  .delay(1000); 

const subject = new Rx.AsyncSubject();

delayedRange.subscribe(subject);
subject.subscribe(
  item => console.log('Value:', item), 
  err  => console.log('Error:', err), 
  ()   => console.log('Completed.');
);
// Value: 4 
// Completed.
```

`delayedRange` emits the values 0 to 4 after a 1 second delay and only the last value emits.

An example using `AsyncSubject` to fetch remote content:

* A function, `getProducts`, returns an Observable sequence.
* The function creates an `AsyncSubject` and subscribes it to the Observable that `Rx.DOM.Request.get(url)` returns.
* Then we subscribe the Observer to the `AsyncSubject`. 
* **Every time an Observer subscribes to the Observable, that Observer will actually be subscribed to the `AsyncSubject`, which is acting as a proxy between the Observable retrieving the URL and the Observers.**
* Then we create the Observable and store it in a `products` variable.
* Then we subscribe to the Observable (`products`) which only then kicks off the URL retrieval and will log results when retrieved.
* Then we make a second subscription in the `setTimeout` which runs five seconds after the first subscription. 
* Finally, the second subsription immediately receives the result of the request has already been retrieved and stored in the AsyncSubject subject.

```javascript
function getProducts(url) {
  let subject;

  return Rx.Observable.create(observer => {
    if (!subject) {
      subject = new Rx.AsyncSubject();
      Rx.DOM.get(url).subscribe(subject);
    }
    
    return subject.subscribe(observer);
  });
}

const products = getProducts('/products');

products.subscribe(
  result => console.log('Result 1:', result.response),
  error  => console.log('ERROR', error);
);

setTimeout(() => {
  products.subscribe(
    result => console.log('Result 2:', result.response),
    error  => console.log('ERROR', error);
  );
}, 5000);
```

Being that `AsyncSubject` caches the last result any subsequent subscriptions to `products` receive the result right away.

Use `AsyncSubject` whenever expecting a single result that you want to hold onto.

##### Note: `AsyncSubject` represents the result of an asynchronous action and can be used to substitute for a promise.

### `Behavior Subject`

A `Behavior Subject` requires a starting value so that all Observers will always receive a value when they subscribe.

An example using `BehaviorSubject` to fetch remote content:

* A new `BehaviorSubject` is initialized with placeholder content. 
* Then we subscribe to it and change the HTML body content in both `onNext` and `onError` (which depends on the result).
* The placeholder text is now contained inside the HTML body where it will stay until a new value is emitted by the Subject .
* Finally, the request is made and we subscribe our Subject to the resulting Observer.

```javascript
const subject = new Rx.BehaviorSubject('Please wait while content is loaded');

subject.subscribe(
  result => document.body.textContent = result.response || result,
  error  => document.body.textContent = 'Oops, there was an error retrieving content';
);

Rx.DOM.get('/remote/content').subscribe(subject);
```

`BehaviorSubject` guarantees that there will always be at least one value emitted. Once completed no more values will be emitted, freeing the memory used by the cached value.

### `ReplaySubject`

A `ReplaySubject` caches its values and re-emits them to any Observer that subscribes late to it.

Unlike with `AsyncSubject`, the sequence doesn’t need to be completed for this to happen.

Comparison between a `Subject` and a `ReplaySubject`:

Example of a `Subject`:

```javascript
const subject = new Rx.Subject();

subject.onNext(1);

subject.subscribe(
  n => console.log('Received value:', n);
);

subject.onNext(2);
subject.onNext(3);
// Received value: 2 
// Received value: 3
```

Example of a `ReplaySubject`:

```javascript
const subject = new Rx.ReplaySubject();

subject.onNext(1);

subject.subscribe(
  n => console.log('Received value:', n);
);

subject.onNext(2);
subject.onNext(3);
// Received value: 1
// Received value: 2 
// Received value: 3
```

`ReplaySubject` is useful to make sure that Observers get all the values emitted by an Observable from the start.

This is only accomplished because `ReplaySubject` caches all values in memory. To limit the amount of data it stores  can be done by buffer size or window of time, or by passing particular parameters to the constructor.

```javascript
const subject = new Rx.ReplaySubject(2); // Buffer size of 2

subject.onNext(1);

subject.subscribe(
  n => console.log('Received value:', n);
);

subject.onNext(2);
subject.onNext(3);
// Received value: 2 
// Received value: 3
```

The second parameter represents the time in milliseconds during which we want to buffer values.

Here we a buffer is set based on time instead of the number of values.

* `ReplaySubject` caches values that were emitted up to 200 milliseconds ago.
* Then we emit three values, each separated by 100 milliseconds.
* Then after 350 milliseconds we subscribe an Observer and we emit yet another value.

```javascript
const subject = new Rx.ReplaySubject(null, 200); // Buffer size of 200ms

setTimeout(() => subject.onNext(1), 100); 
setTimeout(() => subject.onNext(2), 200); 
setTimeout(() => subject.onNext(3), 400); 
setTimeout(() => {
  subject.subscribe(n => console.log('Received value:', n));
  subject.onNext(4);
}, 350);
```

 At the moment of the subscription only items 2 and 3 are cached because 1 happened too long ago (around 250 milliseconds ago).