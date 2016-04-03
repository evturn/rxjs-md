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