## Using Schedulers

A Scheduler is a mechanism to "schedule" an action to happen in the future. They provide fine-grained control over how an Observable emits notifications by allowing you to change their concurrency model as you go.

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
