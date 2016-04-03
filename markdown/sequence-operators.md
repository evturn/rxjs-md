## Sequence Operators

RxJS follows JavaScript conventions where the syntax for the operators are the same for array operators.

### `map`

`map` is the most used transformation operator.

Takes an Observable and a function. 
It applies that function to each of the values in the source Observable. 
It returns a new Observable with the transformed values.

![image](https://github.com/evturn/rxjs-md/blob/master/assets/images/003.png)

With JS Arrays:

```javascript
const src = [1, 2, 3, 4, 5];
const upper = src.map(name => name * 2);
const logValue = val => console.log(val);

upper.forEach(logValue);
```

With Observables:

```javascript
const src = Rx.Observable.range(1, 5);
const upper = src.map(name => name * 2);
const logValue = val => console.log(val);

upper.forEach(logValue);
```

Both do not mutate `src`.

### `filter`

Takes an Observable and a function. 
It tests each element in the Observable using that function. 
It returns an Observable sequence of all the elements for which the function returned `true`.

![image](https://github.com/evturn/rxjs-md/blob/master/assets/images/003.png)