We'd like to build on top of the experimental type support that IPython has started submitting as metadata to allow the Scala kernel almond to showcase additional type information.

* Original motivating issue: https://github.com/jupyter/jupyter_client/issues/51
* Matthias' initial ipykernel support https://github.com/ipython/ipykernel/pull/222


Currently `nteract` and `jupyterlab` support a field in `metadata` for a `complete_reply` called `_jupyter_types_experimental` with the following structure:

```ts
type ExperimentalTypeCompletion =  {
  start: number, // cursor_start
  end: number,   // cursor_end
  text: string,  // code string to replace with
  type: string,  // string for the type
}
```

One caveat about the `type` is that frontends are expected to use the types to display different icons (and colors) per type, currently coded to respond well to:

```ts
    | "module"
    | "keyword"
    | "statement"
    | "function"
    | "instance"
    | "null"
    | "class"
    | string; // Others are allowd
}
```
