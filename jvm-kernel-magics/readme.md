## Background

IPython has a set of predefined ‘magic functions’ that you can call with a command line style syntax. There are two kinds of magics, line-oriented and cell-oriented. 
* Line magics are prefixed with the % character and work much like OS command-line calls: they get as an argument the rest of the line, where arguments are passed without parentheses or quotes. 
* Cell magics are prefixed with a double %%, and they are functions that get as an argument not only the rest of the line, but also the lines below it in a separate argument.

Magics currently are implemented specific to the kernel. We would like to define a specification to support magics across different interpreters in the JVM

## Goals
* Independent of interpreter implementation: we provide a clean interface that can be used by different interpreters in the JVM (Scala, Clojure) to support magic
* Well-defined registration mechanism
* Language agonistic: similiar to the first goal, the implementation should be language agnostic so that different languages can implement their own magics
