Date: 2024-02-28 11:44

type: #post

# TL;DR


# Citations
Needed: What's the difference between the `once_cell` crate's structures and the ones in std?

# Notes
I want to talk about `static`s, how they differ from other languages, and how you could implement your own. For many new Rust users, this is their first real exposure to Rust's desire for you to codify your habits into the language, and it can be annoying at first.

Also part of this discussion will be the wild unsafety of static mutable variables.

There are (generally) three different ways that folks use statics: data that's known (i.e. string literals), data that needs to be constructed on start up, and (global) mutable variables. Put another way, data that is immutable, mutable only once, and generally mutable. Depending on your usecase, you will have a very different initial interaction with statics.



# Draft

## Introduction
Statics are not a very exciting topic. Honestly, they are not very commonly used in the code that I write. Especially compared to other topics, the idea of them is pretty "meh", but I think they are a good microcosm of the pattern of thinking that Rust asks you to adopt. That adoption can often cause friction and annoyance for folks that want things to "just work".

Define what a static is

As much as possible, Rust wants you encode conventions and "best practices" around state management into the type system because it is very good at helping you manage state. As they relate to the state of your program, there are three different ways that statics are used. They are either some known quantity as compile time (e.g. a string literal), a value that needs to be constructed and then set (e.g. application configuration), or they are (global) mutable variable. Put another way, they are either immutable, mutable once, or generally mutable.

The first usage pattern is not all that interesting. Maybe its some string or the contents of a file, but it nicely follows Rust's rules of borrowing and object being in a valid state. The other two are much more interesting. I want to explore the issues around managing mutable statics, how your first encounter with them might affect your view of the language (and its rules more generally), and common ways to use mutable statics safely, and how you could go about doing it ourself.

## Mutations everywhere
While it is generally considered ill-advised in the Rust ecosystem, global mutable variables are still sometimes the best solution you have in some case, like memoization and dynamic programming. Let's use a common example of calculating the nth element in the Fibonacci sequence. To start, we can define a function that returns an iterator over the sequence and a naive function that returns the nth element:
```rust
pub fn fib_iter() -> impl Iterator<Item = usize> {
	let (mut a, mut b) = (1, 1);
	std::iter::repeat(()).map(move |()| {
		let next = a + b;
		std::mem::swap(&mut a, &mut b);
		std::mem::replace(&mut b, next)
	})
}

pub fn get_nth_fib(n: usize) -> usize {
	fib_iter().take(n)
}
```

Simple... but expensive. Its possible to instead cache the values as you calculate them. Then, both the iterator and `get_nth` function can use that data. But, where can `fib_iter` and `get_nth_fib` be called from? There is nothing stopping you from creating two iterators, so they can't contain a mutable reference the cache. So, some kind of synchronization is needed, especially because those two iterators could exist in different threads.

To build a naive cache, given a shared reference to the cache, we need to be able to be able to
- Iterate over the cached values
- Insert a value into the sequence and maintain its order
- Index into the cache, calculating and filling if needed

The synchronization can be solved with a lock, probably an `RwLock`.
## What are statics
Throughout the rest of this post, I'm going to refer to statics as "global". This is not completely accurate as you can define a static that is bound to a particular scope (like in a function). Still, there is no way to communicate to the Rust compiler that a particular function is bound to a single thread, so it will treat statics, no matter where they are defined as being accessible from any thread. For that reason, I'm going to discuss statics as being accessible from any thread, at any time.



# Write Up
