## Observable Pipeline

An Observable pipeline is a group of operators chained together. Each one takes an Observable as input and returns an Observable as output.

```javascript
Rx.Observable
  .from(1, 2, 3, 4, 5, 6, 7, 8) 
  .filter(val => val % 2) 
  .map(val => val * 10);
```