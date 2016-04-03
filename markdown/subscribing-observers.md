## Subscribing Observers

### Ajax and an Observable

This `get` function wraps the `XMLHttpRequest` object using `Rx.Observable.create` and if successful, emits its contents and completes the sequence (the Observable will only ever emit one result). Otherwise, it emits an error.

```javascript
function get(url) {
  return Rx.Observable.create(function(observer) {
    var req = new XMLHttpRequest(); req.open('GET', url);
    
    req.onload = function() { 
      if (req.status == 200) {
        observer.onCompleted();
      } else {
        observer.onError(new Error(req.statusText)); 
      }
    };
    
    req.onerror = function() {
      observer.onError(new Error("Unknown Error"));
    };
    req.send();
  });
}

var test = get('/api/contents.json');
```
##### Note: Observables don’t do anything until at least one Observer subscribes to them.

The Observer is not being explicitly created like the Observable.

Instead we call the `subscribe` operator in the Observable with the three functions for the Observer cases: `onNext`, `onCompleted`, and `onError`.

```javascript
test.subscribe(
  function onNext(x) { 
    console.log('Result: ' + x); 
  }, 
  function onError(err) { 
    console.log('Error: ' + err); 
  }, 
  function onCompleted() { 
    console.log('Completed'); 
  }
);
```

### Operators

Methods that transform or query sequences are called *operators*.

Operators are found in the static `Rx.Observable` object and in Observable instances. RxJS `DOM` library provides several ways to create Observables from DOM-related sources.

This bit of code does the same as the code above without having to create a wrapper around `XMLHttpRequest`:

```javascript
Rx.DOM.get('/api/contents.json').subscribe(
  function onNext(data) { 
    console.log(data.response); 
  }, 
  function onError(err) { 
    console.error(err); 
  }
);
```

The `onCompleted` callback is omitted because we know that it will yield only one result and we are already using it in the `onNext` callback.