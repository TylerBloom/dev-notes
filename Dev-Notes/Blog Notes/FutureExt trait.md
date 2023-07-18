Date: 2023-07-18 10:03

type: #construct

link: https://docs.rs/futures/latest/futures/future/trait.FutureExt.html

# Source Code
Below is the trait for the futures crate `FutureExt` trait. Note that all methods are provided, but their internals are hidden.
```rust
pub trait FutureExt: Future {
    // Provided methods
    fn map<U, F>(self, f: F) -> Map<Self, F> ⓘ
       where F: FnOnce(Self::Output) -> U,
             Self: Sized { ... }
    fn map_into<U>(self) -> MapInto<Self, U> ⓘ
       where Self::Output: Into<U>,
             Self: Sized { ... }
    fn then<Fut, F>(self, f: F) -> Then<Self, Fut, F> ⓘ
       where F: FnOnce(Self::Output) -> Fut,
             Fut: Future,
             Self: Sized { ... }
    fn left_future<B>(self) -> Either<Self, B> ⓘ
       where B: Future<Output = Self::Output>,
             Self: Sized { ... }
    fn right_future<A>(self) -> Either<A, Self> ⓘ
       where A: Future<Output = Self::Output>,
             Self: Sized { ... }
    fn into_stream(self) -> IntoStream<Self>
       where Self: Sized { ... }
    fn flatten(self) -> Flatten<Self> ⓘ
       where Self::Output: Future,
             Self: Sized { ... }
    fn flatten_stream(self) -> FlattenStream<Self>
       where Self::Output: Stream,
             Self: Sized { ... }
    fn fuse(self) -> Fuse<Self> ⓘ
       where Self: Sized { ... }
    fn inspect<F>(self, f: F) -> Inspect<Self, F> ⓘ
       where F: FnOnce(&Self::Output),
             Self: Sized { ... }
    fn catch_unwind(self) -> CatchUnwind<Self> ⓘ
       where Self: Sized + UnwindSafe { ... }
    fn shared(self) -> Shared<Self> ⓘ
       where Self: Sized,
             Self::Output: Clone { ... }
    fn remote_handle(self) -> (Remote<Self>, RemoteHandle<Self::Output>)
       where Self: Sized { ... }
    fn boxed<'a>(
        self
    ) -> Pin<Box<dyn Future<Output = Self::Output> + Send + 'a, Global>>
       where Self: Sized + Send + 'a { ... }
    fn boxed_local<'a>(
        self
    ) -> Pin<Box<dyn Future<Output = Self::Output> + 'a, Global>>
       where Self: Sized + 'a { ... }
    fn unit_error(self) -> UnitError<Self> ⓘ
       where Self: Sized { ... }
    fn never_error(self) -> NeverError<Self> ⓘ
       where Self: Sized { ... }
    fn poll_unpin(&mut self, cx: &mut Context<'_>) -> Poll<Self::Output>
       where Self: Unpin { ... }
    fn now_or_never(self) -> Option<Self::Output>
       where Self: Sized { ... }
}
```

