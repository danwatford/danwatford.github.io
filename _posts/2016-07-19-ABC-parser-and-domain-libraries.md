---
layout: post
---

Find the sources on Github: <https://github.com/danwatford/abc>

## Parsing ABC Notation

ABC Notation (<https://en.wikipedia.org/wiki/ABC_notation>) is a way to encode music notation in simple text. There are many sources of folk/tradition tunes given in ABC Notation.

As part of a project to find shared sequences of notes in traditional tunes (see <http://abc.foomoo.com>) I wanted to parse the content of ABC tune files into objects that could be readily processed.

This parser only converts ABC content into case classes that represent the various ABC notation elements used in the input. Trying to go directly from the ABC notation to tune objectts would have introduced a lot of rules/complexity into the parser which would be better handled in a separate processing phase.

## Using the Parser

The parser is made available in module com.foomoo.abc:abc-parser is available on jcenter maven repository (<https://jcenter.bintray.com/>).

To use the parser wrap the content to be parsed in a Reader (e.g. CharSequenceReader) and call the relevant method for the type of object to be parsed on class AbcNotationParser.
For example, to parse the entire contents of an ABC notation file use something similar to:
```scala
  private def parseFileContent(fileContent: String): Try[AbcNotationFile] =
    AbcNotationParser.file(new CharSequenceReader(fileContent)) match {
      case AbcNotationParser.NoSuccess(msg, next) =>
        Failure(new IllegalArgumentException(msg + "\nNext is: " + next.pos))
      case AbcNotationParser.Success(ts, _) =>
        Success(ts)
    }
```
See the abc-app module in the sources on github for examples of how to call the parser.

Alongside the abs-parser module is the abc-domain module which provides the case classes for ABC notation elements and for ABC tune elements. This module also includes a processor for conversion of Abc notation objects into tune objects.

## Implementation Notes

The parser is implemented using Scala Parser Combinators. Scala Parser Combinators are no longer part of the scala standard library the following dependency was added to the parser project's build.sbt:
  `"org.scala-lang.modules" %% "scala-parser-combinators" % "1.0.4"`
  
The parser extends the RegExParsers trait as it provides convenient ways to convert from Strings and regular expressions into Parser objects which operate on input.

Since whitespace is an important component of the ABC notation it is important to prevent RegexParsers from skipping over it by setting the skipWhitespace method to return false.

To enable parsing of notation elements in String literals where terminating newlines might be missing, the end of input parser was defined. This parser is tested in many of the same places that tests are made for line breaks in input.
This was important to simplify building of test strings in the test specs.


