구문 문법
==============

스칼라 프로그램은 유니코드 기본 다국어 플레인(Unicode Basic 
Multilingual Plane, _BMP_) 문자 집합을 사용해 작성된다. 유니코드 
부가 문자들은 현재 지원되지 않는다. 이번장에서는 스칼라가 현재 
지원하는 두 구문문법에 대해 정의한다. 그 둘은 스칼라 모드와 _XML_ 
모드이다. 따로 언급하지 않는다면 다음에 설명하는 스칼라 토큰들은 
스칼라 모드에만 해당된다. 또한 리터럴 문자 `c'는 아스키 부분인 
\\u0000-\\u007F를 의미한다.

스칼라 모드에서, _유니코드 이스케이프(Unicode escapes)_ 는 주어진 16진 코드에 대응되는 
유니코드 문자로 치환된다.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.grammar}
UnicodeEscape ::= \{\\}u{u} hexDigit hexDigit hexDigit hexDigit
hexDigit      ::= ‘0’ | … | ‘9’ | ‘A’ | … | ‘F’ | ‘a’ | … | ‘f’
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

토큰을 만들 때 문자는 다음과 같은 종류로 구별된다(유니코드 일반 카테고리는 괄호 
안에 표시했다). 

#. 공백문자(Whitespace characters). `\u0020 | \u0009 | \u000D | \u000A`{.grammar}
#. 문자. 소문자(lower case letters, Ll), 대문자(upper case letters, Lu), 
   타이틀케이스 문자(titlecase letters, Lt), 다른 문자(Lo), 순자 문자(letter numerals, Nl),
   그리고 대문자로 취급되는 두 문자 \\u0024 ‘\\$’ 와 \\u005F ‘_’.
#. 숫자들(Digits) ` ‘0’ | … | ‘9’ `{.grammar}
#. 괄호들(Parentheses) ` ‘(’ | ‘)’ | ‘[’ | ‘]’ | ‘{’ | ‘}’ `{.grammar}
#. 구분 문자들(Delimiter characters) `` ‘`’ | ‘'’ | ‘"’ | ‘.’ | ‘;’ | ‘,’ ``{.grammar}
#. 연산자 문자들(Operator characters). \\u0020-\\u007F 에 속한 모든 화면표시 가능한 문자 중에,
   위에 정의한 집합에 속하지 않는 문자들과 수학기호(mathematical symbols, Sm) 및 기타기호(other 
   symbols, So).

\pagebreak[1]

식별자(Identifiers)
-----------

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.grammar}
op       ::=  opchar {opchar} 
varid    ::=  lower idrest
plainid  ::=  upper idrest
           |  varid
           |  op
id       ::=  plainid
           |  ‘`’ stringLit ‘`’
idrest   ::=  {letter | digit} [‘_’ op]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

식별자를 만드는 방법은 세가지가 있다. 우선, 문자로 시작하고 그 뒤에 
임의의 문자와 숫자가 연속해서 따라오면 이를 식별자로 취급한다. `_' 다음에 
임의의 숫자나 문자가 연속해서 와도 식별자이다. 두번째로, 연산자 문자 뒤에 
임의의 연산자 문자가 연속해서 오는 경우도 식별자이다. 이 두가지를 일컬어 
_보통(plain)_ 식별자라 한다. 마지막으로 두 역따옴표(back-quotes) 
사이에 임의의 문자열이 있는 경우도 식별자이다. (호스트 시스템에 따라 
사용 가능한 문자열에는 제한이 있을 수 있다.) 이 경우 식별자는 역따옴표는 
제외한 나머지 문자열로 구성된다.
 
보통 가장 긴 매치 규칙이 사용된다. 예들 들어 다음을 보자.

~~~~~~~~~~~~~~~~ {.scala}
big_bob++=`def`
~~~~~~~~~~~~~~~~

위 문장은 세 식별자 `big_bob`, `++=`, `def`로 구분된다. 패턴 매치를 위한 
추가 규칙은 소문자로 시작하는 _변수 식별자_ 와 그렇지 않은 _상수 식별자_ 를 
구분한다. 

‘\$’문자는 컴파일러가 만든 식별자를 위해 예약되어 있다. 사용자 프로그램에서는 
‘\$’ 문자를 포함한 식별자를사용해서는 안된다. 

다음 이름들은 별도로 예약되어 있으며,식별자의 집합인 문법 클래스 `id`에 
포함되지 않는다.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
abstract    case        catch       class       def
do          else        extends     false       final
finally     for         forSome     if          implicit
import      lazy        match       new         null
object      override    package     private     protected
return      sealed      super       this        throw       
trait       try         true        type        val         
var         while       with        yield
_    :    =    =>    <-    <:    <%     >:    #    @
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

유니코드 연산자 \\u21D2 ‘$\Rightarrow$’와 \\u2190 ‘$\leftarrow$’는 각각 아스키 
문자열 ‘=>’와 ‘<-’과 동일하며, 예약어이다.

(@) 다음은 식별자의 예이다:

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        x         Object        maxIndex   p2p      empty_?
        +         `yield`       αρετη     _y       dot_product_*
        __system  _MAX_LEN_     
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

(@) 역따옴표로 둘러싼 문자열은 스칼라에서 예약어인 자바 식별자를 
    사용해야 하는 경우를 위한 해결책이다. 예를 들어 `Thread.yield()`는 
    스칼라에서 `yield`가 예약어라서 사용할 수 없는 식이다. 하지만, 
    `` Thread.`yield`() ``{.scala} 과 같은 형태로는 사용할 수 있다.


Newline Characters
------------------

~~~~~~~~~~~~~~~~~~~~~~~~ {.grammar}
semi ::= ‘;’ |  nl {nl}
~~~~~~~~~~~~~~~~~~~~~~~~

Scala is a line-oriented language where statements may be terminated by
semi-colons or newlines. A newline in a Scala source text is treated
as the special token “nl” if the three following criteria are satisfied:

#. The token immediately preceding the newline can terminate a statement.
#. The token immediately following the newline can begin a statement.
#. The token appears in a region where newlines are enabled.

The tokens that can terminate a statement are: literals, identifiers
and the following delimiters and reserved words:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
this    null    true    false    return    type    <xml-start>    
_       )       ]       }
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The tokens that can begin a statement are all Scala tokens _except_
the following delimiters and reserved words:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
catch    else    extends    finally    forSome    match        
with    yield    ,    .    ;    :    =    =>    <-    <:    <%    
>:    #    [    )    ]    }
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A `case`{.scala} token can begin a statement only if followed by a
`class`{.scala} or `object`{.scala} token.

Newlines are enabled in:

#. all of a Scala source file, except for nested regions where newlines
   are disabled, and
#. the interval between matching `{` and `}` brace tokens,
   except for nested regions where newlines are disabled.

Newlines are disabled in:

#. the interval between matching `(` and `)` parenthesis tokens, except for 
   nested regions where newlines are enabled, and
#. the interval between matching `[` and `]` bracket tokens, except for nested 
   regions where newlines are enabled.
#. The interval between a `case`{.scala} token and its matching
   `=>`{.scala} token, except for nested regions where newlines are
   enabled.
#. Any regions analyzed in [XML mode](#xml-mode).

Note that the brace characters of `{...}` escapes in XML and
string literals are not tokens, 
and therefore do not enclose a region where newlines
are enabled.

Normally, only a single `nl` token is inserted between two
consecutive non-newline tokens which are on different lines, even if there are multiple lines
between the two tokens. However, if two tokens are separated by at
least one completely blank line (i.e a line which contains no
printable characters), then two `nl` tokens are inserted.

The Scala grammar (given in full [here](#scala-syntax-summary))
contains productions where optional `nl` tokens, but not
semicolons, are accepted. This has the effect that a newline in one of these
positions does not terminate an expression or statement. These positions can
be summarized as follows:

Multiple newline tokens are accepted in the following places (note
that a semicolon in place of the newline would be illegal in every one
of these cases):

- between the condition of a 
  [conditional expression](#conditional-expressions)
  or [while loop](#while-loop-expressions) and the next
  following expression,
- between the enumerators of a 
  [for-comprehension](#for-comprehensions-and-for-loops)
  and the next following expression, and
- after the initial `type`{.scala} keyword in a 
  [type definition or declaration](#type-declarations-and-type-aliases).

A single new line token is accepted

- in front of an opening brace ‘{’, if that brace is a legal
  continuation of the current statement or expression,
- after an [infix operator](#prefix-infix-and-postfix-operations), 
  if the first token on the next line can start an expression,
- in front of a [parameter clause](#function-declarations-and-definitions), and
- after an [annotation](#user-defined-annotations).

(@) The following code contains four well-formed statements, each
    on two lines. The newline tokens between the two lines are not
    treated as statement separators.

    ~~~~~~~~~~~~~~~~~~~~~~ {.scala}
    if (x > 0)
      x = x - 1

    while (x > 0)
      x  = x / 2

    for (x <- 1 to 10)
      println(x)

    type
      IntList = List[Int]
    ~~~~~~~~~~~~~~~~~~~~~~

(@) The following code designates an anonymous class:

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.scala}
    new Iterator[Int]
    {
      private var x = 0
      def hasNext = true
      def next = { x += 1; x }
    }
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~

    With an additional newline character, the same code is interpreted as
    an object creation followed by a local block:

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.scala}
    new Iterator[Int] 

    {
      private var x = 0
      def hasNext = true
      def next = { x += 1; x }
    }
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~

(@) The following code designates a single expression:

    ~~~~~~~~~~~~ {.scala}
      x < 0 ||
      x > 10
    ~~~~~~~~~~~~

    With an additional newline character, the same code is interpreted as
    two expressions:

    ~~~~~~~~~~~ {.scala}
      x < 0 ||

      x > 10
    ~~~~~~~~~~~

(@) The following code designates a single, curried function definition:

    ~~~~~~~~~~~~~~~~~~~~~~~~~ {.scala}
    def func(x: Int)
            (y: Int) = x + y
    ~~~~~~~~~~~~~~~~~~~~~~~~~

    With an additional newline character, the same code is interpreted as
    an abstract function definition and a syntactically illegal statement:

    ~~~~~~~~~~~~~~~~~~~~~~~~~ {.scala}
    def func(x: Int)

            (y: Int) = x + y
    ~~~~~~~~~~~~~~~~~~~~~~~~~

(@) The following code designates an attributed definition:

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.scala}
    @serializable
    protected class Data { ... }
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

    With an additional newline character, the same code is interpreted as
    an attribute and a separate statement (which is syntactically
    illegal).

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.scala}
    @serializable

    protected class Data { ... }
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Literals
----------

There are literals for integer numbers, floating point numbers,
characters, booleans, symbols, strings.  The syntax of these literals is in
each case as in Java.

<!-- TODO 
  say that we take values from Java, give examples of some lits in
  particular float and double. 
-->

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.grammar}
Literal  ::=  [‘-’] integerLiteral
           |  [‘-’] floatingPointLiteral
           |  booleanLiteral
           |  characterLiteral
           |  stringLiteral
           |  symbolLiteral
           |  ‘null’
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


### Integer Literals

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.grammar}
integerLiteral  ::=  (decimalNumeral | hexNumeral | octalNumeral) 
                       [‘L’ | ‘l’]
decimalNumeral  ::=  ‘0’ | nonZeroDigit {digit}
hexNumeral      ::=  ‘0’ ‘x’ hexDigit {hexDigit}
octalNumeral    ::=  ‘0’ octalDigit {octalDigit}
digit           ::=  ‘0’ | nonZeroDigit
nonZeroDigit    ::=  ‘1’ | … | ‘9’
octalDigit      ::=  ‘0’ | … | ‘7’
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Integer literals are usually of type `Int`{.scala}, or of type
`Long`{.scala} when followed by a `L` or
`l` suffix. Values of type `Int`{.scala} are all integer
numbers between $-2^{31}$ and $2^{31}-1$, inclusive.  Values of
type `Long`{.scala} are all integer numbers between $-2^{63}$ and
$2^{63}-1$, inclusive. A compile-time error occurs if an integer literal
denotes a number outside these ranges.

However, if the expected type [_pt_](#expression-typing) of a literal
in an expression is either `Byte`{.scala}, `Short`{.scala}, or `Char`{.scala}
and the integer number fits in the numeric range defined by the type,
then the number is converted to type _pt_ and the literal's type
is _pt_. The numeric ranges given by these types are:

---------------  -----------------------
`Byte`{.scala}   $-2^7$ to $2^7-1$
`Short`{.scala}  $-2^{15}$ to $2^{15}-1$
`Char`{.scala}   $0$ to $2^{16}-1$
---------------  -----------------------

(@) Here are some integer literals:

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    0          21          0xFFFFFFFF       0777L
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


### Floating Point Literals

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.grammar}
floatingPointLiteral  ::=  digit {digit} ‘.’ {digit} [exponentPart] [floatType]
                        |  ‘.’ digit {digit} [exponentPart] [floatType]
                        |  digit {digit} exponentPart [floatType]
                        |  digit {digit} [exponentPart] floatType
exponentPart          ::=  (‘E’ | ‘e’) [‘+’ | ‘-’] digit {digit}
floatType             ::=  ‘F’ | ‘f’ | ‘D’ | ‘d’
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Floating point literals are of type `Float`{.scala} when followed by
a floating point type suffix `F` or `f`, and are
of type `Double`{.scala} otherwise.  The type `Float`{.scala}
consists of all IEEE 754 32-bit single-precision binary floating point
values, whereas the type `Double`{.scala} consists of all IEEE 754
64-bit double-precision binary floating point values.

If a floating point literal in a program is followed by a token
starting with a letter, there must be at least one intervening
whitespace character between the two tokens.

(@) Here are some floating point literals:

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    0.0        1e30f      3.14159f      1.0e-100      .1
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

(@) The phrase `1.toString`{.scala} parses as three different tokens:
    `1`{.scala}, `.`{.scala}, and `toString`{.scala}. On the
    other hand, if a space is inserted after the period, the phrase
    `1. toString`{.scala} parses as the floating point literal
    `1.`{.scala} followed by the identifier `toString`{.scala}.


### Boolean Literals

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.grammar}
booleanLiteral  ::=  ‘true’ | ‘false’
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The boolean literals `true`{.scala} and `false`{.scala} are
members of type `Boolean`{.scala}.


### Character Literals

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.grammar}
characterLiteral  ::=  ‘'’ printableChar ‘'’
                    |  ‘'’ charEscapeSeq ‘'’
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A character literal is a single character enclosed in quotes.
The character is either a printable unicode character or is described
by an [escape sequence](#escape-sequences).

(@) Here are some character literals:

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    'a'    '\u0041'    '\n'    '\t'
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Note that `'\u000A'` is _not_ a valid character literal because
Unicode conversion is done before literal parsing and the Unicode
character \\u000A (line feed) is not a printable
character. One can use instead the escape sequence `'\n'` or
the octal escape `'\12'` ([see here](#escape-sequences)).


### String Literals

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.grammar}
stringLiteral  ::=  ‘\"’ {stringElement} ‘\"’
stringElement  ::=  printableCharNoDoubleQuote  |  charEscapeSeq
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A string literal is a sequence of characters in double quotes.  The
characters are either printable unicode character or are described by
[escape sequences](#escape-sequences). If the string literal
contains a double quote character, it must be escaped,
i.e. `"\""`. The value of a string literal is an instance of
class `String`{.scala}. 

(@) Here are some string literals:

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.scala}
    "Hello,\nWorld!"       
    "This string contains a \" character."
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#### Multi-Line String Literals

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
stringLiteral   ::=  ‘"""’ multiLineChars ‘"""’
multiLineChars  ::=  {[‘"’] [‘"’] charNoDoubleQuote} {‘"’}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A multi-line string literal is a sequence of characters enclosed in
triple quotes `""" ... """`{.scala}. The sequence of characters is
arbitrary, except that it may contain three or more consuctive quote characters
only at the very end. Characters
must not necessarily be printable; newlines or other
control characters are also permitted.  Unicode escapes work as everywhere else, but none
of the escape sequences [here](#escape-sequences) are interpreted.

(@) Here is a multi-line string literal:

    ~~~~~~~~~~~~~~~~~~~~~~~~ {.scala}
      """the present string
         spans three 
         lines."""
    ~~~~~~~~~~~~~~~~~~~~~~~~

    This would produce the string:

    ~~~~~~~~~~~~~~~~~~~
    the present string
         spans three 
         lines.
    ~~~~~~~~~~~~~~~~~~~

The Scala library contains a utility method `stripMargin`
which can be used to strip leading whitespace from multi-line strings.
The expression

~~~~~~~~~~~~~~~~~~~~~~~~~~ {.scala}
 """the present string
    spans three 
    lines.""".stripMargin
~~~~~~~~~~~~~~~~~~~~~~~~~~

evaluates to

~~~~~~~~~~~~~~~~~~~~ {.scala}
the present string
spans three 
lines.
~~~~~~~~~~~~~~~~~~~~

Method `stripMargin` is defined in class
[scala.collection.immutable.StringLike](http://www.scala-lang.org/api/current/index.html#scala.collection.immutable.StringLike). 
Because there is a predefined
[implicit conversion](#implicit-conversions) from `String`{.scala} to
`StringLike`{.scala}, the method is applicable to all strings.


### Escape Sequences

The following escape sequences are recognized in character and string
literals.

------  ------------------------------
`\b`    `\u0008`: backspace BS
`\t`    `\u0009`: horizontal tab HT
`\n`    `\u000a`: linefeed LF
`\f`    `\u000c`: form feed FF
`\r`    `\u000d`: carriage return CR
`\"`    `\u0022`: double quote "
`\'`    `\u0027`: single quote '
`\\`    `\u005c`: backslash `\`
------  -------------------------------

A character with Unicode between 0 and 255 may also be represented by
an octal escape, i.e. a backslash ‘\’ followed by a
sequence of up to three octal characters.

It is a compile time error if a backslash character in a character or
string literal does not start a valid escape sequence.


### Symbol literals

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.grammar}
symbolLiteral  ::=  ‘'’ plainid
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A symbol literal `'x`{.scala} is a shorthand for the expression
`scala.Symbol("x")`{.scala}. `Symbol` is a [case class](#case-classes), 
which is defined as follows.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.scala}
package scala
final case class Symbol private (name: String) {
  override def toString: String = "'" + name
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The `apply`{.scala} method of `Symbol`{.scala}'s companion object
caches weak references to `Symbol`{.scala}s, thus ensuring that
identical symbol literals are equivalent with respect to reference
equality.


Whitespace and Comments
-----------------------

Tokens may be separated by whitespace characters
and/or comments. Comments come in two forms:

A single-line comment is a sequence of characters which starts with
`//` and extends to the end of the line.

A multi-line comment is a sequence of characters between
`/*` and `*/`. Multi-line comments may be nested,
but are required to be properly nested.  Therefore, a comment like
`/* /* */` will be rejected as having an unterminated
comment.


XML mode
--------

In order to allow literal inclusion of XML fragments, lexical analysis
switches from Scala mode to XML mode when encountering an opening
angle bracket '<' in the following circumstance: The '<' must be
preceded either by whitespace, an opening parenthesis or an opening
brace and immediately followed by a character starting an XML name.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.grammar}
 ( whitespace | ‘(’ | ‘{’ ) ‘<’ (XNameStart | ‘!’ | ‘?’)

  XNameStart ::= ‘_’ | BaseChar | Ideographic // as in W3C XML, but without ‘:’
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The scanner switches from XML mode to Scala mode if either

- the XML expression or the XML pattern started by the initial ‘<’ has been 
  successfully parsed, or if
- the parser encounters an embedded Scala expression or pattern and 
  forces the Scanner 
  back to normal mode, until the Scala expression or pattern is
  successfully parsed. In this case, since code and XML fragments can be
  nested, the parser has to maintain a stack that reflects the nesting
  of XML and Scala expressions adequately.

Note that no Scala tokens are constructed in XML mode, and that comments are interpreted
as text.

(@) The following value definition uses an XML literal with two embedded
Scala expressions

    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.scala}
    val b = <book>
              <title>The Scala Language Specification</title>
              <version>{scalaBook.version}</version>
              <authors>{scalaBook.authors.mkList("", ", ", "")}</authors>
            </book>
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

