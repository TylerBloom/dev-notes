Date: 2024-01-20 10:24

type: #post

# TL;DR
This is my introductory post for my Rust education series, Rust from First Principles. I dig into what it means for a language to be "hard to learn" and introduce a framework for reasoning about Rust code to deepen your understanding.

# Citations
Need to read and find:
- The registers of Rust (without Boats): https://without.boats/blog/the-registers-of-rust/
- The levels of language acquisition (the Bloom Taxonomy): https://en.wikipedia.org/wiki/Bloom%27s_taxonomy
- Plain text by Dylan Beattie: https://www.youtube.com/watch?v=gd5uJ7Nlvvo

# Notes
There are different levels to "learning". I want to point out the ways that you can scale those levels.

Computer science is just abstractions all the way down. An abstraction is just a story we tell ourselves to avoid looking at all of the gory detail, but stories need told. I want to point out the ways in which Rust gives you to tools to quickly become familiar with an abstraction, learn how to use it, or "just get by". That is, Rust lets you pick the level of granularity that you want/need to think at.

Think of mental models as a graph where the nodes abstractions or general ideas. From afar, ideas like, say, borrowing and lock-based concurrency are distant, but zooming in reveals a connection between the nodes. Zooming in further might also reveal that individual nodes might be smaller substructures (after all, there's a difference between a `Mutex` and an `RwLock`). The process of exploring this graph, adjusting zoom levels, and collecting ideas is language agnostic (yet another abstraction ;) ). Moreover, learning how this network gets rendered into a given language is part of the learning process for that language, and that's the rub. Rust gives you excellent tools for learning to navigate this graph (enforced ownership and borrowing rules, enums (ENUMS!!!), traits, generics, and much much more). This is a good time to pull from the "mode"/"level" of Rust written by Nico and Yats(?).

Vast swaths of this learning can happen just through the type, function, and trait signatures.

I want to make a strong connection between levels of learning and mental model granularity. These two things are somewhat co-variant. Having a strong/deep understanding begets a solid mental model, but that tells us nothing of the process of acquisition. This is kind of a chicken and egg situation, but, luckily, we can pick which comes first.

I want to give a short example of this. A singular example like "Why does `String` not have a mutable iterator, i.e. `String::chars_mut`?" Let's start with "what is a string"? Isn't it "just", conceptually, a `Vec<char>`? Well, yes... but also no. What is a `char`? Answering this answers the original question, but there are multiple answers. The simple answer is "not all chars need to take amount of space" or "the standard Rust uses to encode strings compacts the representation of a `char` to help minimize space." Obviously, the abstractions go deeper, and you can continue to traverse this rabbit hole, but, importantly, you don't need to. This answers our question. "Not all chars take up the same amount of space in a string" means that `Vec<char>` and `String` are not the same (a string is actually a `Vec<u8>`). In-place, char by char manipulation of a string would require that every char going in is the same size as a char coming out (or, in a slightly different implementation, every char coming out needs to be replaced by a set of chars that are represented by the same number of bytes, but that's a different story). And that's it. That's the reason, but I want to point something out. Depending on the level of knowledge that need to know about this or what the effect you would like to have is, you don't need to go down this series of questions. Learning to use Rust is learning to ask the right questions and the correct follow up questions. The answer to "Does `String` allow for in-place manipulation of `char`s?" is "no". You can see that in the methods list on String. If your goal was to replace all "-" with " ", there are methods for that, but they aren't in-place.

This is my final point of the example. I want to approach this question from a place of continued learning. You've asked the question, got a simple answer, and started using `String::replace` in order to just get something that works. If your happy with this, excellent. Continue on, but you might find yourself asking "But why?". And this is where I think Rust becomes extremely powerful. You can arrive at the same realization of "Not all chars take up the same amount of space in a string" from where you already are, i.e. from the function signature of `String::replace` (it returns a new String, so a new allocation, which means that the String might take up different amounts of space, but why is there not a `replace_char` method? Because "not all chars take up the same amount of space in a string"). That, to me, is the beauty of Rust.

Tied in here somewhere is likely the question "why can't this be simple?" The graph I mentioned before exists (well, it "exists" as much as an idea can "exist") irrespective of language; therefore, in order for a language to be "simple" it must hide that complexity from you. Maybe that's what you want, but obscuring complexity can hinder your learning. Obscuring rules requires learning the language's implementation of those rules and makes navigating between general ideas more difficult.

This graph is the graph of "first principles". Computer science, being a derivative of math, is based on rules, axioms. The "mental model" graph is derived from those axioms and is then rendered by a given language. My goal is to explain how you can quickly switch between the abstract idea to Rust code quickly and then use that to become a "Rust expert".

# Draft

v

### Abstractions and mental models
I want to discuss the mental framework that I've adopted during my Rust journey. Hopefully, you will find it helpful throughout this series. Put another way, we need to spend a short time thinking about thinking.

Computer science is just abstractions on abstractions on abstractions... After all, ones and zeros do not actually exist on your computer (well, "one"s and "zero"s don't really exist...). An abstraction is just a story we tell ourselves to avoid looking at all of the gory detail, but stories need to be told and you're the storyteller.

A particularly useful story about mental models and abstractions that I find helpful is the idea that knowledge is a graph (a network) where the nodes are abstractions and general ideas. From afar, ideas like, say, borrowing and lock-based concurrency are distant. One is about ensuring the validity of pointers and the other is about data being accessed across threads. Zooming in reveals a connection between the nodes. Zooming in further might also reveal that individual nodes might be smaller substructures, e.g. the differences between a `Mutex` and an `RwLock` mirrors the difference between raw pointers and references. The process of exploring this graph, adjusting zoom levels, and collecting ideas is language agnostic (yet another abstraction ;) ). In more concrete terms, this idea is more generally known as [Bloom's taxonomy](https://en.wikipedia.org/wiki/Bloom%27s_taxonomy). 

 Learning how this network of ideas gets rendered into a given language is part of the learning process for that language, and this is where I think Rust shines. Rust gives you excellent tools for learning to navigate this graph, such as enforced ownership and borrowing rules, enums (ENUMS!!!), traits, generics, and much more. These are the building blocks for the language and they communicate so much. (Bit about co variance?)

### Editing strings
Ok, enough thinking about thinking. Let's get into a short, but hopefully enlightening example. Why does `Sting` (or `&mut str`) not have a mutable iterator over chars?

Let's start with "what is a string?". Isn't it "just" a continuous sequence of characters? Basically making a `String` roughly equivalent to a `Vec<char>` but with nicer formatting and such. Well, yes... but also no. Certainly on some level, this is true. Strings do hold everything in a single, contiguous chunk like a vector. But, what is a `char`? Answering this will answer the original question, but there are multiple answers.

Rust uses UTF-8 encoding for its string, and, according to the wiki page for UTF-8, "UTF-8 is a variable-length character encoding standard used for electronic communication." Of course, the abstractions go deeper. There is an entire interesting history about the representation of in computers (see Dylan Beattie's talk [Plain Text](https://www.youtube.com/watch?v=gd5uJ7Nlvvo)), but that's not needed. A much simpler answer is "inside a `String`, not all characters need to take amount of space." While not as deeply informative

Working backwards, a `char` is 4 bytes in a particular pattern, but `String`s aren't `Vec<char>` in order to minimize space.


```
The simple answer is "not all chars need to take amount of space" or "the standard Rust uses to encode strings compacts the representation of a `char` to help minimize space." Obviously, the abstractions go deeper, and you can continue to traverse this rabbit hole, but, importantly, you don't need to. This answers our question. "Not all chars take up the same amount of space in a string" means that `Vec<char>` and `String` are not the same (a string is actually a `Vec<u8>`). In-place, char by char manipulation of a string would require that every char going in is the same size as a char coming out (or, in a slightly different implementation, every char coming out needs to be replaced by a set of chars that are represented by the same number of bytes, but that's a different story). And that's it. That's the reason, but I want to point something out. Depending on the level of knowledge that need to know about this or what the effect you would like to have is, you don't need to go down this series of questions. Learning to use Rust is learning to ask the right questions and the correct follow up questions. The answer to "Does `String` allow for in-place manipulation of `char`s?" is "no". You can see that in the methods list on String. If your goal was to replace all "-" with " ", there are methods for that, but they aren't in-place.

This is my final point of the example. I want to approach this question from a place of continued learning. You've asked the question, got a simple answer, and started using `String::replace` in order to just get something that works. If your happy with this, excellent. Continue on, but you might find yourself asking "But why?". And this is where I think Rust becomes extremely powerful. You can arrive at the same realization of "Not all chars take up the same amount of space in a string" from where you already are, i.e. from the function signature of `String::replace` (it returns a new String, so a new allocation, which means that the String might take up different amounts of space, but why is there not a `replace_char` method? Because "not all chars take up the same amount of space in a string"). That, to me, is the beauty of Rust.

```


# Write Up
## TL;DR
This is my introductory post for my Rust education series, Rust from First Principles. I dig into what it means for a language to be "hard to learn" and introduce a framework for reasoning about Rust code to deepen your understanding.
## Intro
Rust is generally considered a hard language to learn. This is true, to a degree. There are certainly many challenges to learning the language because it forces you to solve problems that you would normally put off until testing (or see a bug report for). Still, I think the label of "hard language to learn" misses a lot of context and is only true for certain definitions of what it means to "learn" something.

Learning is a constant, active process. The degree to which a language is "hard to learn" should take into account someone's entire career with the language. This requires us to take a step back. For Rust, there is a steep initial climb no matter what your background is; but, after you learn the building blocks, Rust becomes significantly easier to grow with. This differs from most other languages that seems to present a series of "fun" and "unique" problems at every stage of growth.

This is the first post in what I hope to be this blog's flagship series. I want to explore different areas of the standard library and the broader ecosystem in order to explain how they work from the ground up. But, this will not be a code review. In fact, most of the code will just be function and type signatures. My goal is to provide a functional explanation by reasoning with the fundamentals to quickly arrive at different levels of actionable understanding. I will not be focusing on any particular level of understanding, though some topics will naturally skew more beginner or advanced. Instead, I intend to provide a framework for you to quickly finding the level of understanding you need/want while keeping the ability to progress as needed.

So, join me was we learn Rust from First Principles.

## Abstractions all the way down
Computer science is just abstractions on abstractions on abstractions... After all, ones and zeros do not actually exist on your computer (well, "one"s and "zero"s don't really exist...). There are two aspects of an abstraction that determine its quality, completeness and being "leak proof". That is, a good abstraction covers everything you want it to cover and nothing that you do not.

The primary goal of any programming language is to provide you with a set of core abstractions so you don't need to think about the nitty gritty details. There are times where you need to dive into the weeds, but the type and quality of abstractions dictate the frequency that you must do that. This is where Rust excels. It's core abstractions combine to provide a near complete and mostly leak proof foundation on which to build. Moreover, by understanding a few of them, you can gain an understanding of the others.

## Oh look, threads
As an example, let's take one of the foundation rules of Rust: "any instance of type `T` must be valid." This is where we get ideas like "there are no null values", but this is also where we get the idea of lifetimes (among many others). References are just types, but they must point to a valid instance of `T`. Therefore, the language must track when that instance might be moved or deconstructed in order to ensure the validity of a `&T`. This is gets particularly tricky once threads enter the picture.

If two threads are sharing data, and that value lives on one thread's stack, the owning thread needs to not end while the other thread is running. If it does, that value will no longer exist, making the borrowing thread's reference invalid. So, the language needs to be able to describe that a value doesn't live elsewhere on a stack and isn't owned by anything elsewhere on a stack. And it does, that value must be `'static`! The standard library's thread spawn function even points this out:
```rust
// From std::thread::spawn
pub fn spawn<F, T>(f: F) -> JoinHandle<T>
where
    F: FnOnce() -> T + Send + 'static,
    T: Send + 'static,
```

For this conversation, we can ignore all bounds except lifetimes. The function that we give the other thread needs to not borrow anything from the caller's scope, and the value that is return (from the other thread) needs to not borrow anything from the thread's scope. This is where Rust gets must of its utility over other languages. These rules allow you to think about much higher level details without concerns of data validity.

## Level up (or not)
Through a short chain of questions, we can explain why `std::thread::spawn`'s argument needs to be `'static`. While this is a nice tool for recalling details, it is not necessary to go through. If you just needed to get something working, you don't need to go down that rabbit hole. You can simply work with the requirement as given by putting a `move` in from of the closure you give. Then, if you want, you can return to more deeply understand why. In this way, Rust clearly signposts its rabbit holes to let you focus on key details of your project instead of the minutia.

Anecdotally, this has been my experience with learning Rust. You have an idea, you hit a bump, you work within the given constraints, and then, after you have a working product, you can return to improve your understanding (and maybe even your system). Over the course of my knowing how to code, my experience with Rust is an anomaly. None of the other languages that I have used (Python, C++, and some JS) have given me the tools to better understand the language. Moreover, none of those languages have made me feel secure leaving the details un-investigated until later.

Working with excellent abstractions like this allows you to easily render mental models of systems into code (and visa versa). Let's expand on the thread example from before. Maybe we have a large data structure that would be expensive to move to another thread or to clone, but we still need data to be shared. Well, you could reach for an `Arc`, but that would require learning a new mental model around "shared ownership" as well as moving this large object onto the heap. The rule from before is that the thread owning the data can not end before the other thread is done. And the standard library has something for this, a thread scope!
```rust
// From std::thread::{scope, Scope}
pub fn scope<'env, F, T>(f: F) -> T
where
    F: for<'scope> FnOnce(&'scope Scope<'scope, 'env>) -> T, { .. }

pub struct Scope<'scope, 'env: 'scope> { /* private fields */ }
```

Again, if you just wanted to get something working that shared data between threads, you can end things here. Start with an example in the docs and look at the methods on the `Scope` struct, and you're off to the races!

If you want to learn a bit more, this one is a bit harder to parse. Since we care about certain threads living longer than others, that should provide a clue that we're mostly interested in the lifetimes. The `scope` function takes a function that lives at least as long as the `'scope` lifetime, and that function takes a single argument, a `Scope` struct. In the `Scope` struct's definition, we can find the rest of the information we need. The `Scope` struct captures two lifetimes, `'scope` and `'env`. We can see that `'env` must live for at least as long as `'scope` (i.e. `'env: 'scope`). This maps fairly cleanly onto the mental model: that the owning thread needs to live as long as the spawned (borrowing) thread. This allows the spawned thread to capture references to the surrounding environment and ensures that its inner scope is not longer than the outer scope.

## Wrap-up
This series will be centered on this process. Given a subject, we will formulate a simple mental model and then either build a system to match that model or see how that model gets translated into code. Then, we can work these two in tandem to improve both the overall system and our understanding to whatever level you need/desire.

Beyond bringing particular concepts into sharper focus, my larger goal is to make Rust code that is not your own more approachable. The standard library is (mostly) not magic. Tokio is not magic. And nearly all other code you will encounter is not magic (except for macros... they are kind of magic). You too have the ability to reason about such esteemed crates.

Next time, I think I'll discuss statics. If that sounds interesting, I'll see you there!