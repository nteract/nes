## Magic registration process

### Interpreter interface
The interpreter will have to implement the following interface work with our magic registry
```
interface Interpreter {
   void display(Map);
   Object get(String varName);
   void set(String varName, Object obj);
   void interpret(String str);
}
```

### Magic registry
`Magics` class is implemented outside the interpereter. We follow the IPython convention and support three types of magic:
* Line magic
* Cell magic
* Line cell magic

```
// single-method interface so users can use Java's lambda
interface LineFunc {
   public call(intp: Interpreter, line: String): Optional<Object>
}

interface CellFunc {
   public call(intp: Interpreter, cell: String, line: String): Optional<Object>
}

static class Magics {
   bool registerLine(name: String, lineFunc: LineFunc)
   bool registerCell(name: String, cellFunc: CellFunc)
   bool registerLineCell(name: String, lineCellFunc: CellFunc)
}
```

## Invoking a magic
The responsibility of invoking magic belongs to the interpreter. Most magics rely on the `%` character to signify the start of a magic, but it's up to the interpreter and the language to process this. The interpreter invokes the correct method line or cell) to get the implementation back.

```
static class Magics {
   CellFunc call(name: String, line: String, cell: String, interp: Interperter)
   LineFunc call(name: String, line: String, interp: Interperter)
}
```

`Magics` class also performs some basic formatting (trimming spaces in line magics) and validation:

| No | Content | Cell (no trim) | Line (trim) | Invalid? |
|----|---------|----------------|-------------|----------|
| 1 | %x blah | None | "blah" |  |
| 2| %x | None | "" | |
| 3| %%x | None | "" | "" | INVALID |
| 4| %%x blah | None | "" | "blah" | INVALID |
| 5| %%x </br> blah | "blah | "" | |
| 6| %x blah </br> cellBlah | "cellBlah" | "blah" | |
| 7| %x [space] | None | "" | |

## Parsing util
It's open to the question whether `Magics` utility should provide parsing support or not.

Use case:
```
val x = 12;
%tail_log
x
```

Current proposed API:

```
Iterable<???> Magics.parse(str)
```

## Next steps
* jvm-repr is a good place to start introducing this API: https://github.com/jupyter/jvm-repr
* POC for a simple magic with Almond and jvm-repr
  * Possibly via `predef` approach
