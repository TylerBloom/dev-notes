Date: 2023-07-18 09:53

type: #example

link: https://docs.rs/tokio/latest/tokio/task/fn.spawn.html

# Source/Signature
Below is the signature for `tokio::task::spawn` which creates a task inside the [[tokio]] runtime and returns a handle to the value that will be returned.

```rust
pub fn spawn<T>(future: T) -> JoinHandle<T::Output> ⓘwhere
    T: Future + Send + 'static,
    T::Output: Send + 'static,
```

# Context
A [[tokio task]] is very similar to the idea of a [[green thread]]. That is, it performs computation as soon as it starts and will yield its value to the `JoinHandle` at some point in the future. This differs from a [[future]], which implements the logic of pause-ible computation but must be acted upon in order to eventually yield a value.

# Breakdown


## Conceptual
(Insert diagram: left side is "compute resources", middle is "task manager", right side is "list of futures")
(Insert state-transition diagram of futures moving from "queued" to "in process" to "queued" and/or "completed")

## Plain English
Move from conceptual images/diagrams into plain English.

## Code Functions
How does each part of the code bring us closer to the above ideas?