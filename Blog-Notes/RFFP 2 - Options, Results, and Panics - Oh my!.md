Date: 2024-02-28 12:10

type: #post

# TL;DR


# Citations


# Notes
I want to discuss the important for options and results. In particular, I want to point how have a lack of exceptions is required by the rules of Rust, how Results are an altogether better alternative, and how panics are not exceptions. This will require discussions around `UnwindSafe` and `RefUnwindSafe`.

I think it will be fruitful to build up to these auto traits from the ground up. Leading with this discussion will make a good case for why Results and Options are so helpful. Sentinel values also have a place in this discuss, but they are simple to ignore via the "correct by construction" argument.

# Draft


# Write Up
