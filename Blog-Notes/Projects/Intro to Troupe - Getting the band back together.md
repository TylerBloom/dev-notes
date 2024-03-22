Date: 2024-03-20 09:56

type: #post

# TL;DR
I want to share my thoughts around my actor library, troupe, as I seek to improve it.

# Citations


# Notes

### Foundations
For as much as "actor models" are discussed in language spaces, an "actor" is not a broadly coherent concept beyond its theoretic definition. Actors have different implementations and semantic meanings between languages that support them as first-class constructs (e.g. Erlang and other beam-base languages). Moreover, in Rust, they have even less of a concrete 
definition and vary greatly between crates.

To start, let's (briefly) discuss the theoretical foundations of actors. From the Wikipedia page from "Actor model":
> The **actor model** in [computer science](https://en.wikipedia.org/wiki/Computer_science "Computer science") is a [mathematical model](https://en.wikipedia.org/wiki/Mathematical_model "Mathematical model") of [concurrent computation](https://en.wikipedia.org/wiki/Concurrent_computation "Concurrent computation") that treats an _actor_ as the basic building block of concurrent computation. In response to a [message](https://en.wikipedia.org/wiki/Message_(computing) "Message (computing)") it receives, an actor can: make local decisions, create more actors, send more messages, and determine how to respond to the next message received. Actors may modify their own [private state](https://en.wikipedia.org/wiki/Private_state "Private state"), but can only affect each other indirectly through messaging (removing the need for [lock-based synchronization](https://en.wikipedia.org/wiki/Lock_(computer_science) "Lock (computer science)")).

Despite being a "building block of concurrent computation", this definition says nothing about the low-level details of how these actor run and for good reason. Much of the early work done with actors was done with large numbers of low-power microprocessors. In a certain sense, this is similar to the nowadays-popular microservice model for larger services. Key differences being scale (a circuit board vs a data center), power (a small chip vs a virtualized machine with access to many modern CPU cores), and message transfer process (a wire vs HTTP). Like the evolution of many species into crab-like creatures, the evolution of service and application architectures well illustrates the utility of thinking about systems as being made up of discreet units of work that are, largely, independent. Still, I think we should be careful in taking this pattern as descended-from-on-high. We are working with a different set of tools today, so the model needs adapting.

To render the idea of a "actor" into Rust, I want to consider the fundamental requirements of an actor. Again, from the Wiki page:
>An actor is a computational entity that, in response to a message it receives, can concurrently:
> - send a finite number of messages to other actors
> - create a finite number of new actors
> - designate the behavior to be used for the next message it receives

The first rule is straightforward: an actor can talk to a non-infinite number of actors in response to a message. The second rule clues into the fact that a system modeled with actors is not static. It can grow (and presumably shrink), but it does not tell us what can limit that growth. But, the last rule is the most important: an actor does not have to respond the same way to a given message. In other words, an actor is a state machine.

Also in the fundamental concepts section of the wiki is a discussion around "mailing addresses", which are ways for actors to "look up" other actors. I discuss an alternate approach that troupe takes to this later on, but the crux of this requirement is that actors need to have some amount of knowledge of other actors and where they are in order to send messages to them.

So, let's recap. An system modeled with actors is a collection (or contains a collection) of independent state machines that sit and wait for a message, can spawn other actors, and have the ability to send messages between each other. Cool. That seems extremely doable in Rust.

TODO: I want to draw a connection between the scale of different systems that can be modeled with actors. My end goal is to allude to the idea that you can make "nanoservices" (vs microservices), but I also want to point out that any application or sub-system of an application can be implemented with actors. Part of the overarching thesis of this is to show that actors are scale agnostic.

TODO: Another key point in the thesis is that an actor looks extremely different between the inside view (a collection of streams and futures, a runner, a state machine, etc) and the outside view (a think that consumes a specific type of message and/or emits a specific type of message).
### First draft
There are many open questions around implementing an application (a single executable) using actors. I want to sidestep the question about orchestration and how actors actually run for a bit. Instead, I want to focus on how we might render an actor, the state machine, in Rust as it will provide insight to how we can implement the plumbing of message passing and orchestration.

Actor sit and wait for messages. Every actor needs to have some source of messages for it `await`. This sounds like a `Stream` (or a collection of them). Notably, there is no restriction on where messages need to come from. This means that an actor could "send" a message to itself or somehow queue a message to be sent to itself, sort of like a loopback. The combination of merging a stream with a loopback queue will be a key part of the troupe actor design that will be discussed further on. For now, let's consider what kind of stream might lead themselves to this kind of application. 

An actor might need to silent to multiple types of streams, such as a TCP and/or Websocket stream, a stream of data coming from a database, or a stream of messages coming from other actors running in the same process. Communication between threads (or async tasks) is often done with channels in Rust. While not all channels fit the mold of a `Stream`, many do. 


# Draft


# Write Up
