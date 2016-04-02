### Data Types

In an RxJS program, one should strive to have all data in Observables, not just data that comes from asynchronous sources. 

Doing that makes it easy to combine data from different origins (like an existing array with the result of a callback).

RxJS provides operators to create Observables from most JavaScript data types. 

The most common are which are used all the time are arrays, events, and callbacks.

### Observables from Arrays

An array-like or iterable object into an Observable by using the versatile `from` operator.

`from` takes an array as a parameter and returns an Observable that emits each of its elements.

```javascript
Rx.Observable
  .from(['Adrià', 'Jen', 'Sergi']) 
  .subscribe(
    x   => console.log('Next: ' + x), 
    err => console.log('Error:', err),
    ()  => console.log('Completed')
  );
```

`from`, along with `fromEvent`, is one of the most frequently used RxJS operators.