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