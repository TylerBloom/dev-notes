Date: 2023-07-18 10:05

type: #construct

link: https://doc.rust-lang.org/std/future/trait.Future.html

# Source Code
Below is the trait definition for the standard library's `Future` trait.
```rust
pub trait Future {
    type Output;

    // Required method
    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output>;
}
```
