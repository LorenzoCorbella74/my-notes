---
applyTo: "**"
---
# RxJS and Angular Best Practices

## Core Concepts
- **Observables:** The fundamental building block of RxJS. Represents a stream of data over time.
- **Operators:** Functions that transform, filter, combine, and manipulate observables. Pipe operators together for complex data flows.
- **Subjects:** A special type of observable that allows multicasting values to many observers.
- **Subjects:** A special type of observable that allows multicasting values to many observers.

## Best Practices

### Subscription Management
- **`takeUntil`:** Use a `Subject` and the `takeUntil` operator to automatically unsubscribe from observables when a component is destroyed.
- **`take(1)`:** If you only need the first value from an observable, use `take(1)` to automatically complete the observable after the first emission.
- **Async Pipe:** In Angular, use the `async` pipe in templates to let the framework handle subscriptions and unsubscriptions automatically.

### Error Handling
- **`catchError`:** Use the `catchError` operator to handle errors within an observable stream. Return a new observable or throw an error.

### Choosing the Right Operator
- **`map` vs. `tap`**: Use `map` to transform data. Use `tap` for side effects like logging, without modifying the stream.
- **Filtering:** Use `filter` to selectively emit values from a source Observable.
- **Debouncing and Throttling:** Use `debounceTime` for input fields to wait for a pause in typing. Use `throttleTime` to limit the rate of emissions.

### Higher-Order Mapping Operators
- **`switchMap`:** For scenarios where you want to cancel previous inner observables when a new outer observable emits. Ideal for search-as-you-type features.
- **`mergeMap` (or `flatMap`):** When you want to handle all inner observables concurrently.
- **`concatMap`:** When you need to process inner observables in sequence, waiting for each one to complete before starting the next.
- **`exhaustMap`:** Ignores new inner observables while the current one is still active. Useful for handling rapid clicks on a button.

### Creating Observables
- Prefer factory functions like `of`, `from`, `fromEvent`, `interval`, and `timer` over creating observables with `new Observable()`.

