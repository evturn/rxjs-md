## Observable Pipeline

An Observable pipeline is a group of operators chained together. Each one takes an Observable as input and returns an Observable as output.

```javascript
Rx.Observable
  .from(1, 2, 3, 4, 5, 6, 7, 8) 
  .filter(val => val % 2) 
  .map(val => val * 10);
```

Pipelines are self-contained where state flows from one operator to the next, eliminating the need for any external variables.

To avoid storing state outside the Observable pipeline (and potentially introducing side effects), operators in a pipeline should always use pure functions which always return the same output given the same input.

### Encapulating State

Shared external state example:

```javascript
let evenTicks = 0;

function updateDistance(i) { 
  if (i % 2 === 0) {
    evenTicks += 1;
  }
  
  return evenTicks; 
}

const ticksObservable = Rx.Observable 
  .interval(1000) 
  .map(updateDistance)

ticksObservable.subscribe(() => console.log('Subscriber 1: ', evenTicks));
ticksObservable.subscribe(() => console.log('Subscriber 2: ', evenTicks));
// Subscriber 1: 1
// Subscriber 2: 2
// Subscriber 1: 2
// Subscriber 2: 2
// Subscriber 1: 3
// Subscriber 2: 4
// Subscriber 1: 4
// Subscriber 2: 4
```

Avoiding external state:

```javascript
function updateDistance(acc, i) { 
  if (i % 2 === 0) {
    acc += 1;
  }
  
  return acc; 
}

const ticksObservable = Rx.Observable 
  .interval(1000) 
  .map(updateDistance, 0)

ticksObservable.subscribe(evenTicks => console.log('Subscriber 1: ', evenTicks));
ticksObservable.subscribe(evenTicks => console.log('Subscriber 2: ', evenTicks));
// Subscriber 1: 1
// Subscriber 2: 1
// Subscriber 1: 1
// Subscriber 2: 1
// Subscriber 1: 2
// Subscriber 2: 2
// Subscriber 1: 2
// Subscriber 2: 2
```

### Array Chaining vs Observable Chaining

Arrays

* Array methods create a new array as a result of each operation. 
* That new array is traversed entirely by the next operation.  

Observables

* Observables don’t create intermediate Observables. 
* Operations are applyed to each element in one go, traversed only once.

```javascript
anArrayOfOneThousandStrings
  .map(str => str.toUpperCase()) 
  .filter(str => /^[A-Z]+$/.test(str))
  .forEach(str => console.log(str));
```

The preceding executes as following:

* Iterate through the array and create a new array with all items uppercase.
* Iterate through the uppercase array, creating another array with 1,000 elements.
* Iterate through the filtered array and log each result to the console.

This process iterated an array containing 1000 items three times and created two completely new arrays containing 1000 items.
