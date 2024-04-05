# Hilbert Curve Transform with WASM bindings

This is a fork of [Paul Chernoch](https://github.com/paulchernoch)'s rust implementation of Skilling's Algorithms for the Hilbert Curve.
I added WASM bindings to use it in a web project.
The solution is quite hacky, and there might be better ways to do this, especially since I did this a while ago.
Basically I added wrappers around the core methods of the library in order to parse BigUint into strings and vice versa.
At the time I found it was the best "quick and dirty" solution around the typesystem bottleneck between Rust and JavaScript.

Clone this repo, build it with cargo, then run 
```
> wasm-pack build
```

Then in the startup script of your app, add the following
```js
import init, { 
  hilbert_axes_wasm_from_str, 
  hilbert_index_wasm_to_str, 
  rescale_hilbert_from_str, 
} from './pkg/hilbert.js';
import './app.css'
import App from './App.svelte'

//create an objet that stores the declaration of js bindings for exported wasm functions
export let wasm_functions = {
  forward: undefined,
  inverse: undefined,
  rescale_index: undefined,
}

async function loadApp(){
  await init()
  wasm_functions.forward = hilbert_axes_wasm_from_str
  wasm_functions.inverse = hilbert_index_wasm_to_str
  wasm_functions.rescale_index = rescale_hilbert_from_str
  const app = new App({
    target: document.getElementById('app')
  })
}

loadApp();
```

Then import these functions in your code like so

```ts
import { wasm_functions as W } from '../main.js';
export const HilbertForward = (p: BigInt, b: number, d: number) => W.forward(p.toString(), b, d)
export const HilbertInverse = (X: Uint32Array, b: number) => BigInt(W.inverse(X, b))
```


