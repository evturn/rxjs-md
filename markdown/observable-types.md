## Observable Types

In an RxJS program, one should strive to have all data in Observables, not just data that comes from asynchronous sources. 

Doing that makes it easy to combine data from different origins (like an existing array with the result of a callback).

RxJS provides operators to create Observables from most JavaScript data types. 

The most common are which are used all the time are arrays, events, and callbacks.

### Observables from Arrays

An array-like or iterable object into an Observable by using the versatile `from` operator.

`from` takes an array as a parameter and returns an Observable that emits each of its elements.

```javascript
Rx.Observable
  .from(['Sergio', 'Tyrone', 'Choiman']) 
  .subscribe(
    x   => console.log('Next: ' + x), 
    err => console.log('Error:', err),
    ()  => console.log('Completed')
  );
```

`from`, along with `fromEvent`, is one of the most frequently used RxJS operators.

### Observables from Events

When an event is transformed into an Observable it becomes a first-class value that can be combined and passed around.

Transforming an event into an Observable unleashes the event from its natural constraints.

```javascript
const allMoves = Rx.Observable.fromEvent(document, 'mousemove'); 

allMoves.subscribe(e => console.log(e.clientX, e.clientY));
```

One can create new Observables based on the original Observables which are independent and can be used for different tasks.

```javascript
const movesOnTheRight = allMoves.filter(e => e.clientX > window.innerWidth / 2);
const movesOnTheLeft = allMoves.filter(e => e.clientX < window.innerWidth / 2);

movesOnTheRight.subscribe(e => console.log('Mouse is on the right', e.clientX));
movesOnTheLeft.subscribe(e => console.log('Mouse is on the left', e.clientX));
```

Two Observables are created from the original, `allMoves`. These specialized Observables contain only filtered items from the original one and neither of modify the original while `allMoves` will keep emitting all mouse moves.

##### Note: Observables are immutable, and every operator applied to them creates a new Observable.

### Observables from Callback Functions

Callbacks can be transformed into Observables using two functions, `fromCallback` and `fromNodeCallback`.

`fromNodeCallback` creates Observables specifically from Node.js-style (error first) callbacks.

Node's `fs.readdir` accepts a directory path and a callback function which is called once the contents are retrieved.

Here an Observable is created out of the `readdir` method and used without providing a callback.

```javascript
const Rx = require('rx');
const fs = require('fs');

const readdir = Rx.Observable.fromNodeCallback(fs.readdir);
const source = readdir('/Users/tyrone');
const subscription = source.subscribe(
  res => console.log('List of directories: ' + res), 
  err => console.log('Error: ' + err),
  ()  => console.log('Done!')
);
```

This now returns an Observable that will properly use `onNext`, `onError`, and `onCompleted` when we subscribe an Observer to it.