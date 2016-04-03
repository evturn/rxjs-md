## Canceling Sequences

Other asynchronous forms of communication, such as callbacks and promises, can’t be directly canceled once they’re called.

In RxJS we can cancel a running Observable two ways: *implicitly* and *explicitly*.

### Explicit Cancelation

Observables don’t have a method to get canceled.

Although, when we subscribe to an Observable we get a `Disposable` object that represents that particular subscription. We can then call the method `dispose` in that object, and that subscription will stop receiving notifications from the Observable.

```javascript
const counter = Rx.Observable.interval(1000);
const subscription1 = counter.subscribe(i => console.log('Subscription 1:', i));
const subscription2 = counter.subscribe(i => console.log('Subscription 2:', i));

setTimeout(() => {
  console.log('Canceling subscription2!'); 
  subscription2.dispose();
}, 2000);
// Subscription 1: 0 
// Subscription 2: 0 
// Subscription 1: 1 
// Subscription 2: 1 
// Canceling subscription2! 
// Subscription 1: 2 
// Subscription 1: 3 
// Subscription 1: 4
```

### Implicit Cancelation

Most cancelations are handled implicitly.

Operators such as `range` or `take` will automatically cancel the subscription when the sequence finishes or when the operator conditions are met. More advanced operators that handle several Observables in motion, such as `withLatestFrom` or `flatMapLatest`, internally create and destroy subscriptions as needed.

### External APIs and Cancelation

When using an Observable that wraps a promise, the Observable will stop emitting when canceled, but the underlying promise will not be canceled. Canceling the subscription to the Observable effectively stops it from receiving the notification but any calls to the promise’s `then` method that occur after will still run.

```javascript
const p = new Promise((resolve, reject) => window.setTimeout(resolve, 5000));

p.then(() => console.log('Potential side effect!'));

const subscription = Rx.Observable
  .fromPromise(p)
  .subscribe(msg => console.log('Observable resolved!'));

subscription.dispose();
```

And after 5 seconds, we see:
```javascript
// Potential side effect!
```