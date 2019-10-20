# Kayak Lang

To use this language to parse a file, run
```$xslt
$ ./kayak.sh <filename>
```
This language consists of three parts: The lexer, the parser, and the interpreter. The lexer is what converts plaintext into objects, the parser organizes those objects using Backus-Naur form, and the interpreter uses the objects to run the program.

A basic example would be 2+2. 
The BNF for this form would be 
```
e ::= n | n + e
n ::= 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 
```

The lexer and parser will convert this into an object which can then be read. 

```$xslt
2 + 2 ==> Binary(Plus,N(2),N(2))
```

The ANTLR lexer and parser are defined in KayakLexer.g4 and KayakParser.g4. These are the only ANTLR files that should be modified. 
To generate the other files in the `grammar` package, run the following commands in the `grammar` folder:

```$xslt
$ antlr4 -visitor -package kayak.grammar KayakLexer.g4
$ antlr4 -visitor -package kayak.grammar KayakParser.g4  
```


The ANTLR grammar then has to be converted into a Scala Abstract Syntax Tree (AST) for use in the interpreter. This conversion happens in the ast package, in `ParseTreeConverter.scala`, `Parser.scala` and `ast.scala`. 

The ast case classes (such as `N` for number and `Plus` for addition) are defined in ast.scala. `ParseTreeConverter` is a class that extends the KayakParserBaseVisitor generated class and overrides the methods in that class to convert the objects given by ANTLR into a Scala AST. 
Finally, `Parser` will be the file that takes in a file name of Kayak code, runs it through the Lexer and Parser, converts it to a Scala AST, and then runs it through the interpretor. Currently, it uses a dictionary of example expressions for testing.

The interpretor exists in `interpreter/SmallStepInterpreter`. It evaluates on the Program object in `Parser`.

To run this code, first run the ANTLR generation code using the commands above. Then, run the Parser object (In Intellij, this can be done by clicking the "plus" sign next to the object definition.)

### Things that don't work
- Order of operations in variable assignment
- scopes/variable definition and use in ifelse statments
- Expressions after an if statment don't use the environment defined in the if statement