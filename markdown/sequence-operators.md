## Sequence Operators

RxJS follows JavaScript conventions where the syntax for the operators are the same for array operators.

This function will be used to display output:

```javascript
const logValue = val => console.log(val);
```

### `map`

`map` is the most used transformation operator.

Takes an Observable and a function. 
It applies that function to each of the values in the source Observable. 
It returns a new Observable with the transformed values.

With JS Arrays:

```javascript
const src = [1, 2, 3, 4, 5];
const upper = src.map(name => name * 2);

upper.forEach(logValue);
```

With Observables:

```javascript
const src = Rx.Observable.range(1, 5);
const upper = src.map(name => name * 2);

upper.forEach(logValue);
```

![image](https://github.com/evturn/rxjs-md/blob/master/assets/images/003.png)

Both do not mutate `src`.

### `filter`

Takes an Observable and a function. 
It tests each element in the Observable using that function. 
It returns an Observable sequence of all the elements for which the function returned `true`.

With JS Arrays:

```javascript
const src = [1, 2, 3, 4, 5];
const even = src.filter(isEven);
const isEven = val => val % 2 !== 0;

even.forEach(logValue);
```

With Observables:

```javascript
const src = Rx.Observable.range(1, 5);
const even = src.filter(isEven);
const isEven = val => val % 2 !== 0;

even.subscribe(logValue);
```

![image](https://github.com/evturn/rxjs-md/blob/master/assets/images/004.png)

### `reduce`

Also known as `fold`.

Takes an Observable.
It applies a function that receives the current element and the result of the function’s previous invocation.
Returns a new Observable that always contains a single item which is the result of applying that function over each element.

With JS Arrays:

```javascript
const src = [1, 2, 3, 4, 5];
const sum = src.reduce((a, b) => a + b);

console.log(sum);
```

With Observables:

```javascript
const src = Rx.Observable.range(1, 5);
const sum = src.reduce((a, b) => a + b);

sum.subscribe(logValue);
```

![image](https://github.com/evturn/rxjs-md/blob/master/assets/images/005.png)

`reduce` is the base implementation for a whole subset of methods called *aggregate operators*.