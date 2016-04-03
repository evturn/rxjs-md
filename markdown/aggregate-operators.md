## Aggregate Operators

Aggregate operators process a sequence and return a single value.

Being that base implementation of aggregate operators uses `reduce`, every aggregate operator can also be implemented by using only reduce.

`reduce` is called with an initial value,`initialValue`, an object with two properties, `sum` and `count`. Since `reduce` doesn’t provide us with the total number of elements in the sequence we will also use that object to store the sum and total. Each new value is added to the previous one and every new element will return the same object with updated values.

```javascript
const initialValue = { 
  sum: 0, 
  count: 0 
};
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