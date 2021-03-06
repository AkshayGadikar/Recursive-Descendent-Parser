# Recursive-Descendent-Parser
Recursive Descendant Parser in Java

A primitive ugly regexp is chars(x_1, ..., x_n) with n >= 1.  'chars',
the parentheses and comma occur literally whereas x_1, ... x_n can be
any single characters.  chars(x1, ...) will match any of the
characters x_1, ..., x_n, ...

If R1 and R2 are regexp's, then so are:

  + R1 . R2 which will match the concatenation of any string matched by
    R1 with any string matched by R2.

  + R1 + R2 which match any of the strings matched by either R1 or R2.

  * R1 which will match 0-or-more strings matched by R1.

The precedence of the operators are ordered from lowest ., +, *
highest.  . and + are left-associative.  Parentheses may be used in
the usual way to override the default associativity and precedence.  *
is allowed to nest, i.e., * * R1 is legal.

A regexp is expected to be contained within a single line but may
contain arbitrary linear whitespace which should be ignored.

Examples of regexp's and their translations using traditional regexp
syntax:

chars(a, 1, 2) . chars(4, 2) + *chars(a, b)
[a12]([42]|[ab]*)

(chars(a, 1, 2) . chars(4, 2)) + *chars(a, b)
[a12][42]|[ab]*

chars(,, \,) + chars(1, 2)
[\,\\]|[12]

                         Grammar

Grammar which enforces associativity and precedence.
Terminals: '.', '+', '*', '(', ')', CHARS, CHAR

Operator . at highest level in grammar because lowest precedence.
Left associative => left recursive rules.

regexp
  : regexp '.' term
  | term
  ;
term
  : term '+' factor
  | factor
  ;
factor
  : '*' factor
  | '(' regexp ')'
  | CHARS '(' CHAR chars ')'
  ;
chars
  : CHAR ',' chars
  | /* empty */
  ;

Removing left-recursion, we have:
regexp
  : term regexpRest
  ;
regexpRest
  : '.' term regexpRest
  | /* empty */
  ;
term
  : factor termRest
  ;
termRest
  : '+' factor termRest
  | /* empty */
  ;
factor
  : '*' factor
  | '(' regexp ')'
  | CHARS '(' CHAR chars ')'
  ;
chars
  : CHAR ',' chars
  | /* empty */
  ;