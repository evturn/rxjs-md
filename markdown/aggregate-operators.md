## Aggregate Operators

Aggregate operators process a sequence and return a single value.

Being that base implementation of aggregate operators uses `reduce`, every aggregate operator can also be implemented by using only reduce.

`reduce` is called with an initial value,`initialValue`, an object with two properties, `sum` and `count`. Since `reduce` doesn’t provide us with the total number of elements in the sequence we will also use that object to store the sum and total. Each new value is added to the previous one and every new element will return the same object with updated values.

```javascript
const initialValue = { sum: 0, count: 0 };
const avg = Rx.Observable.range(0, 5)
  .reduce((prev, cur) => {
    return { 
      sum: prev.sum + cur, 
      count: prev.count + 1 
    }; 
  }, initialValue).map(o => o.sum / o.count);

const subscription = avg.subscribe(x => console.log('Average is: ', x));
// Average is: 2
```

When the sequence ends, `reduce` will call `onNext` with the object containing the final `sum` and the final `count`. 

At that point `map` then returns the result of dividing the sum by the count.

### `scan`

The problem with logging the average of an infinite sequence in real time is that the sequence never ends.
An aggregate operator like `reduce` will never call its Observers’ `onNext` operator.

The `scan` operator acts like `reduce` but emits each intermediate result allowing us to aggregate sequences that take a long time to complete or are infinite.

```javascript
const initialValue = { sum: 0, count: 0 };
const avg = Rx.Observable.interval(1000)
  .scan((prev, cur) => {
    return {
      sum: prev.sum + cur, 
      count: prev.count + 1
    };
  }, initialValue).map(o => o.sum / o.count);

const subscription = avg.subscribe(x => console.log(x));
```

The preceding generated an incremental integer every second which called `scan` to get the average of values generated so far, every second.

### `flatMap`

If you have an Observable whose results are more Observables, `flatMap` will unify items in those nested Observables in a single sequence.

Takes an Observable A whose elements are also Observables.
It returns an Observable with the flattened values of A’s child Observables.

Below each of the elements in A (A1, A2, A3) are also Observable sequences. Applying `flatMap` to A with a transformation function returns an Observable with all the elements from the different children of A.

![image](https://github.com/evturn/rxjs-md/blob/master/assets/images/006.png)

`flatMap` takes a source Observable and a function that returns a new Observable and applies that function to each element in the source Observable, like `map` does. 

The difference between `map` and `flatMap` is that `map` would return an Observable that emits Observables while `flatMap` emits to the main sequence the values emitted by each new Observable, "flattening" all the Observables into one main sequence.