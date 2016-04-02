### Sequences

A standard visual representation for sequences, called *marble diagrams*.

![image](https://github.com/evturn/rxjs-md/blob/master/assets/images/001.png)

* A long arrow represents the an Observable over time.
* Each circle represents a value the Observable emits by internally calling `onNext()`.
* And a call to `onCompleted` represented by a vertical line.

### Using `merge` and `interval`

The `merge` operator takes two different Observables and returns a new one with the merged values.

The `interval` operator returns an Observable that yields incremental numbers at a given interval of time in milliseconds.

```javascript
const a = Rx.Observable.interval(200).map(i => 'A' + i);
const b = Rx.Observable.interval(100).map(i => 'B' + i);

Rx.Observable.merge(a, b).subscribe(x => console.log(x));
// B0, A0, B1, B2, A1, B3, B4
```

The marble diagram for the `merge` operator:

![image](https://github.com/evturn/rxjs-md/blob/master/assets/images/002.png)

The dotted arrows along the y-axis point to the final result of the transformation applied to each element in sequences A and B. The resulting Observable containing the merged elements of A and B is represented by C. 

##### Note: If elements of different Observables are emitted at the same time, the order of these elements in the merged sequence is random.