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