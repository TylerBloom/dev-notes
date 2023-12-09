Date: 2023-12-05 17:21

type: #idea #rffp
# TL;DR
The intro post to a mini-series on interior mutability.

# About

`RefCell::get_borrow` will panic if there is another lock guard out already. This holdholds the borrow-checker's invariant at runtime and allows for [[interior mutability]]. However, `RefCell::get_mut` will not. How can this be the case? Walk through how you can arrive at that conclusion naturally.

If you just need to use it: "One takes a shared reference. The other takes an exclusive reference". If you want to know more about the inner workings: "The second one requires that there isn't an reference to that object already." Use the docs strings to verify your answer.