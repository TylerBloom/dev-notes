Date: 2023-07-18 09:54

type: #concept


# Diagram
(Insert state machine diagram)

# Explanation
A future (in the context of Rust) is an object that can compute a value and whose computation can be paused. They can be thought of as [[state machines]] whose various states are locations in the code where the computation can be paused. In Rust, these locations are marked by a `.await`.

# Constructs
The quentessential Rust future is the standard library's [[Future trait]]. The [[futures crate]] [[FutureExt trait]] is also worth noting as it naturally extends what a future can do.