## Using Schedulers

A Scheduler is a mechanism to "schedule" an action to happen in the future. They provide fine-grained control over how an Observable emits notifications by allowing you to change their concurrency model as you go.

`from` uses `Rx.Scheduler.currentThread` internally:

```javascript

let arr = [];
for (let i = 0; i < 1000; i++) {
  arr.push(i);
}

let timeStart = Date.now(); 
Rx.Observable.from(arr).subscribe(
  () => '', 
  err => '', 
  () => console.log('Total time: ' + (Date.now() - timeStart) + 'ms'); 
);
// Total time: 6ms
```


Now with `Rx.Scheduler.default`;

```javascript

let arr = [];
for (let i = 0; i < 1000; i++) {
  arr.push(i);
}

let timeStart = Date.now(); 
Rx.Observable
  .from(arr, null, null, Rx.Scheduler.default)
  .subscribe(
    () => '', 
    err => '', 
    () => console.log('Total time: ' + (Date.now() - timeStart) + 'ms'); 
  );
// Total time: 5337ms
```

This is much slower because `default` Scheduler runs each notification asynchronously which means it emits its items asynchronously while the `console.log` statement which is synchronous is executed before the Observable even starts emitting any notification.

Using the `currentThread` Scheduler, all notifications happen synchronously causing the `console.log` statement gets executed only when the Observable has emitted all its notifications.