We'd like to build on top of the experimental type support that IPython has started submitting as metadata to allow the Scala kernel almond to showcase additional type information.

* Original motivating issue: https://github.com/jupyter/jupyter_client/issues/51
* Matthias' initial ipykernel support https://github.com/ipython/ipykernel/pull/222
* [`complete_request` and `complete_reply` in the proper jupyter protocol](https://jupyter-client.readthedocs.io/en/stable/messaging.html#completion)


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

Questions raised:

> For cursor position, would the start ever be different between matches?

While `pd.Da` will complete with `DataFrame`, `DateOffset`, and `DatetimeIndex`, they all start with the prefix of `Da`. In some cases though you might replace a whole token. One example of this is replacing `\phi` in IPython and IJulia with `ϕ`. 

> Could we get type info from ammonite's `complete` method?

No, it appears we'll need to update Ammonite's [prefixed method](https://github.com/lihaoyi/Ammonite/blob/7e088bbd9c7c1c4c3bcf714beee2263e055af2f6/amm/interp/src/main/scala/ammonite/interp/Pressy.scala#L155) to extract and return this information. [Ammonite's complete method](https://github.com/lihaoyi/Ammonite/blob/7e088bbd9c7c1c4c3bcf714beee2263e055af2f6/amm/interp/src/main/scala/ammonite/interp/Pressy.scala#L283) returns a tuple: ```(position: Int, allNames: Seq[String], signatures: Seq[String])```. Almond consumes the position and allNames fields. The **signatures**  field returned by Ammonite is empty in the following test cases.

Example 1:
```
input:
  Futu^

output:
  allNames=[
  com.sun.corba.se.impl.orbutil.closure.Future
  java.util.concurrent.Future
  java.util.concurrent.FutureTask
  java.util.concurrent.FutureTask$WaitNode
  scala.collection.parallel.FutureTasks
  scala.collection.parallel.FutureThreadPoolTasks
  scala.concurrent.Future
  scala.concurrent.Future$InternalCallbackExecutor
  scala.concurrent.Future$never
  ]
  signatures=[]
```

Example 2:
```
input:
  case class Person(name: String)
  Pers^

output:
  allNames=[Person]
  signatures=[]
```

> Ammonite's complete returns all names and signatures? Could we return the list of signatures in the experimental payloads above?

> What are the limitations of Ammonite's autocomplete?

https://github.com/lihaoyi/Ammonite/issues/275


> For each completion, is there a way to get information about the type (instance, method, class, primitive, etc.)?
