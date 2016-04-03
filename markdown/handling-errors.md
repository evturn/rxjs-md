## Handling Errors

In RxJS we can’t use `try`/`catch` because it is synchronous causing it to run before any asynchronous code.

With callbacks, this is usually dealt with by passing the error as a parameter to the callback function.

### `onError`

`onError` is used for handling errors in Observable sequences. It is one of three methods (`onNext`, `onCompleted`, `onError`) we can call on an Observer.

```javascript
function getJSON(arr) {
  return Rx.Observable.from(arr).map(str => JSON.parse(str));
}

const data = [
  '{"1": 1, "2": 2}',
  '{"success: true}', // Invalid JSON string 
  '{"enabled": true}'
];

getJSON(data).subscribe(
  json => console.log('Parsed JSON: ', json),
  err  => console.log(err.message)
);
// ParsedJSON: { 1: 1, 2: 2 }
// JSON.parse: unterminated string at line 1 column 8 of the JSON data
```

The parsed JSON for the first result is emitted by the Observable. Then the Observable throws an exception when trying to parse the second result which the `onError` handler catches this and prints it out. 

##### Note: The default behavior is that whenever an error happens, the Observable stops emitting items, and onCompleted is not called.

### `catch`

Allows us to react to an error in the Observable and continue with another Observable.

Takes either an Observable or a function that receives the error as a parameter. 
It returns another Observable.

We create a new Observable, `caught`, that uses the `catch` operator to catch errors in the original Observable. If there’s an error it will continue the sequence with an Observable that emits only one item with an error property describing the error.

```javascript
function getJSON(arr) {
  return Rx.Observable.from(arr).map(str => JSON.parse(str));
}

const data = [
  '{"1": 1, "2": 2}', 
  '{"1: 1}'  // Invalid JSON string 
];

const caught = getJSON(data).catch(
  Rx.Observable.return({
    error: 'There was an error parsing JSON' 
  })
);

getJSON(data).subscribe(
  json => console.log('Parsed JSON: ', json),
  // Using `catch` means `onError` will not be executed
  e    => console.log('ERROR', e.message) 
);
// Parsed JSON: Object { 1: 1, 2: 2 }
// Parsed JSON: Object { error: "There was an error parsing JSON" }
```

![image](https://github.com/evturn/rxjs-md/blob/master/assets/images/007.png)

* X indicates the sequence experienced an error.
* Observable values represented as triangles are values coming from another Observable, in this example that's the Observable we return in case of an error.

### `retry`

`retry` is used to ignore an error that happens with an item in the Observable in order to let the sequence continue.

Here we create a function that returns an Observable that retrieves contents from a URL using `XMLHttpRequest`.

```javascript
Rx.DOM.get('/products').retry(5)
  .subscribe(
    xhr => console.log(xhr),
    err => console.error('ERROR: ', err)
  );
```

By adding `retry(5)` before subscribing to it we ensure that in the case of an error, it will try up to five times before giving up and showing an error.

* Without passing any parameters it will retry indefinitely until the sequence is finished with no errors which is dangerous for performance and can cause the same effect as an infinite loop.
* Even if some of the items didn’t error, it will always retry the whole Observable sequence again meaning that any side effects you're causing when processing items will be reapplied as well with every retry.