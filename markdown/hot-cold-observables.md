## Hot and Cold Observables

* "Hot" Observables emit values regardless of Observers being subscribed to them. 
* "Cold" Observables emit the entire sequence of values from the start to every Observer.

### Hot Observables

Similar to how JavaScript events work, an Observer subscribed to a hot Observable receives values emitted from that exact moment.

```javascript
const onMove = Rx.Observable.fromEvent(document, 'mousemove'); 

const subscriber1 = onMove.subscribe(e => console.log('Subscriber1:', e.clientX, e.clientY));
const subscriber2 = onMove.subscribe(e => console.log('Subscriber2:', e.clientX, e.clientY));
// Subscriber1: 23 24
// Subscriber2: 23 24
// Subscriber1: 34 37
// Subscriber2: 34 37
// Subscriber1: 46 49
// Subscriber2: 46 49
```

Both subscribers receive the same values from the Observable as they are emitted (as would be the case with JavaScript events).

### Cold Observables

A cold Observable emits values only when Observers subscribe to it.

```javascript
function printValue(value) { 
  console.log(value);
}

const rangeToFive = Rx.Observable.range(1, 5);

const obs1 = rangeToFive.subscribe(printValue);      // 1, 2, 3, 4, 5
const obs2 = Rx.Observable
  .delay(2000) 
  .flatMap(() => rangeToFive.subscribe(printValue)); // 1, 2, 3, 4, 5
```

### Hot vs Cold

`Rx.Observable.interval` which is a cold Observable emits the same sequence of values to all Observers.

```javascript
const source = Rx.Observable.interval(1000);

const observer1 = source.subscribe(x => console.log('Observer 1: ' + x));

setTimeout(() => {
  const observer2 = source.subscribe(x => console.log('Observer 2: ' + x));
}, 3000);

// Observer 1: 0 
// Observer 1: 1 
// Observer 1: 2 
// Observer 1: 3
// Observer 2: 0
// Observer 1: 4
// Observer 2: 1
```

Instead of starting with the current value and continuing from there, Observers listening to a cold Observable will receive copies of the same sequence of values.

##### Note: Even though the Observers are sharing the same Observable, they are not sharing the same *exact* sequence of values.

A cold Observable emits the same sequence of values, yet depending on when certain Observers subscribe, they won't necessarily share the same sequence.

In order for Observers to share the same sequence, they need to be subscribed to a hot Observable.
