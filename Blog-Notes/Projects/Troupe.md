Date: 2024-01-23 14:09

type: #project

# About
Troupe is an actor model framework built on top of Tokio. It sprung from my work on Squire as a way of modelling both the front and back ends of that project. Because of this, Troupe is designed to work well for both native and WASM targets.

For WASM, the tokio runtime isn't used. Instead, the browser-produced runtime (accessible via [wasm-bindgen-futures](https://rustwasm.github.io/wasm-bindgen/api/wasm_bindgen_futures/index.html)) is used, but tokio channels are still used for message passing.

You can check out the project repo [here](https://github.com/TylerBloom/troupe).