### Sequences

A standard visual representation for sequences, called *marble diagrams*.

![image](https://raw.githubusercontent.com/staltz/rxmarbles/master/dist/img/example_merge.png)

* A long arrow represents the an Observable over time.
* Each circle represents a value the Observable emits by internally calling `onNext()`.
* And optionally a call to `onCompleted` would be at the end.
