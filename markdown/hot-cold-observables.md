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