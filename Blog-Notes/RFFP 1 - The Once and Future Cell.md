Date: 2024-02-28 11:44

type: #post

# TL;DR


# Citations
Needed: What's the difference between the `once_cell` crate's structures and the ones in std?

# Notes
I want to talk about `static`s, how they differ from other languages, and how you could implement your own. For many new Rust users, this is their first real exposure to Rust's desire for you to codify your habits into the language, and it can be annoying at first.

Also part of this discussion will be the wild unsafety of static mutable variables.

# Draft


## What are statics
Throughout the rest of this post, I'm going to refer to statics as "global". This is not completely accurate as you can define a static that is bound to a particular scope (like in a function). Still, there is no way to communicate to the Rust compiler that a particular function is bound to a single thread, so it will treat statics, no matter where they are defined as being accessible from any thread. For that reason, I'm going to discuss statics as being accessible from any thread, at any time.



# Write Up
