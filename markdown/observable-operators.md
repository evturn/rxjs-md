## Observable Operators

### `from`

Default behavior: *Synchronous*

Creates Observables out of the data sources that come from arrays, array-like objects (`arguments`, DOM NodeLists), and types that implement the iterable protocol, such as `String`, `Map`, and `Set`.

### `range`

Default behavior: *Synchronous*

Generates finite Observables that emit integers in a particular range

```javascript
const source = Rx.Observable.range(0, 3);

const subscription = source.subscribe(
  x   => console.log('Next: ' + x),
  err => console.log('Error: ' + err),
  ()  => console.log('Completed')
);
// => Next: 0
// => Next: 1
// => Next: 2
// => Completed
```

### `interval`

Default behavior: *Asynchronous*

Used when you need to generate values spaced in time. `interval` emits sequential integers every *x* milliseconds.

```javascript
const source = Rx.Observable
  .interval(500)
  .timeInterval()
  .take(3);

const subscription = source.subscribe(
  x   => console.log('Next: ' + x),
  err => console.log('Error: ' + err),
  ()  => console.log('Completed')
);
// => Next: {value: 0, interval: 500}
// => Next: {value: 1, interval: 500}
// => Next: {value: 2, interval: 500}
// => Completed
```

This example used `timeInterval` which records the time interval between consecutive values in an observable sequence.

### `distinct`

Default behavior: *Same as the Observable it filters*

Filters out of the sequence any value that has already been emitted. This keeps from tracking previously emitted results and comparing them against incoming results.

![image](https://github.com/evturn/rxjs-md/blob/master/assets/images/008.png)

We pass a function that specifies the comparison method. If no arguments are passed it will use strict comparison to compare primitives such as numbers or strings, and run deep comparisons in case of more complex objects