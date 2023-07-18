Date: 2023-07-18 09:47

type: #topic


# TL;DR
A dive into how to think about async concurrency in Rust and how it differs from other models. Covers ideas like [[future]]s, [[async runtime]]s (with an emphasis on [[tokio]]), how it differs from [[multi-threading]], and useful patterns for designing async systems.

The emphasis will be on usability and readability. We will not discuss things like how to build an async runtime. Rather, the focus will be on how to reason about async code and some pitfalls associated therein.

# About

