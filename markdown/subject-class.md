## Subject Class

A type that implements both Observer and Observable types. As an Observer, it can subscribe to Observables, and as an Observable it can produce values and have Observers subscribe to it.

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