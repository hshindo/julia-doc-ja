.. 
 .. _man-strings:

 .. currentmodule:: Base

 *********
  Strings
 *********

 Strings are finite sequences of characters. Of course, the real trouble
 comes when one asks what a character is. The characters that English
 speakers are familiar with are the letters ``A``, ``B``, ``C``, etc.,
 together with numerals and common punctuation symbols. These characters
 are standardized together with a mapping to integer values between 0 and
 127 by the `ASCII <https://en.wikipedia.org/wiki/ASCII>`_ standard. There
 are, of course, many other characters used in non-English languages,
 including variants of the ASCII characters with accents and other
 modifications, related scripts such as Cyrillic and Greek, and scripts
 completely unrelated to ASCII and English, including Arabic, Chinese,
 Hebrew, Hindi, Japanese, and Korean. The
 `Unicode <https://en.wikipedia.org/wiki/Unicode>`_ standard tackles the
 complexities of what exactly a character is, and is generally accepted
 as the definitive standard addressing this problem. Depending on your
 needs, you can either ignore these complexities entirely and just
 pretend that only ASCII characters exist, or you can write code that can
 handle any of the characters or encodings that one may encounter when
 handling non-ASCII text. Julia makes dealing with plain ASCII text
 simple and efficient, and handling Unicode is as simple and efficient as
 possible. In particular, you can write C-style string code to process
 ASCII strings, and they will work as expected, both in terms of
 performance and semantics. If such code encounters non-ASCII text, it
 will gracefully fail with a clear error message, rather than silently
 introducing corrupt results. When this happens, modifying the code to
 handle non-ASCII data is straightforward.

 There are a few noteworthy high-level features about Julia's strings:

 -  The built-in concrete type used for strings (and string literals) in Julia is :obj:`String`.
    This supports the full range of `Unicode <https://en.wikipedia.org/wiki/Unicode>`_ characters
    via the `UTF-8 <https://en.wikipedia.org/wiki/UTF-8>`_ encoding.
    (A :func:`transcode` function is provided to convert to/from other Unicode encodings.)
 -  All string types are subtypes of the abstract type :obj:`AbstractString`,
    and external packages define additional :obj:`AbstractString` subtypes
    (e.g. for other encodings).  If you define a function expecting
    a string argument, you should declare the type as :obj:`AbstractString` in
    order to accept any string type.
 -  Like C and Java, but unlike most dynamic languages, Julia has a
    first-class type representing a single character, called :obj:`Char`.
    This is just a special kind of 32-bit bitstype whose numeric value
    represents a Unicode code point.
 -  As in Java, strings are immutable: the value of an :obj:`AbstractString` object
    cannot be changed. To construct a different string value, you
    construct a new string from parts of other strings.
 -  Conceptually, a string is a *partial function* from indices to
    characters: for some index values, no character value is returned,
    and instead an exception is thrown. This allows for efficient
    indexing into strings by the byte index of an encoded representation
    rather than by a character index, which cannot be implemented both
    efficiently and simply for variable-width encodings of Unicode
    strings.

 .. _man-characters:

 Characters
 ----------

 A :obj:`Char` value represents a single character: it is just a 32-bit
 bitstype with a special literal representation and appropriate arithmetic
 behaviors, whose numeric value is interpreted as a `Unicode code
 point <https://en.wikipedia.org/wiki/Code_point>`_. Here is how :obj:`Char`
 values are input and shown:

 .. doctest::

     julia> 'x'
     'x'

     julia> typeof(ans)
     Char

 You can convert a :obj:`Char` to its integer value, i.e. code point,
 easily:

 .. doctest::

     julia> Int('x')
     120

     julia> typeof(ans)
     Int64

 On 32-bit architectures, :func:`typeof(ans) <typeof>` will be ``Int32``. You can
 convert an integer value back to a :obj:`Char` just as easily:

 .. doctest::

     julia> Char(120)
     'x'

 Not all integer values are valid Unicode code points, but for
 performance, the :func:`Char` conversion does not check that every character
 value is valid. If you want to check that each converted value is a
 valid code point, use the :func:`isvalid` function:

 .. doctest::

     julia> Char(0x110000)
     '\U110000'

     julia> isvalid(Char, 0x110000)
     false

 As of this writing, the valid Unicode code points are ``U+00`` through
 ``U+d7ff`` and ``U+e000`` through ``U+10ffff``. These have not all been
 assigned intelligible meanings yet, nor are they necessarily
 interpretable by applications, but all of these values are considered to
 be valid Unicode characters.

 You can input any Unicode character in single quotes using ``\u``
 followed by up to four hexadecimal digits or ``\U`` followed by up to
 eight hexadecimal digits (the longest valid value only requires six):

 .. doctest::

     julia> '\u0'
     '\0'

     julia> '\u78'
     'x'

     julia> '\u2200'
     '∀'

     julia> '\U10ffff'
     '\U10ffff'

 Julia uses your system's locale and language settings to determine which
 characters can be printed as-is and which must be output using the
 generic, escaped ``\u`` or ``\U`` input forms. In addition to these
 Unicode escape forms, all of `C's traditional escaped input
 forms <https://en.wikipedia.org/wiki/C_syntax#Backslash_escapes>`_ can
 also be used:

 .. doctest::

     julia> Int('\0')
     0

     julia> Int('\t')
     9

     julia> Int('\n')
     10

     julia> Int('\e')
     27

     julia> Int('\x7f')
     127

     julia> Int('\177')
     127

     julia> Int('\xff')
     255

 You can do comparisons and a limited amount of arithmetic with
 :obj:`Char` values:

 .. doctest::

     julia> 'A' < 'a'
     true

     julia> 'A' <= 'a' <= 'Z'
     false

     julia> 'A' <= 'X' <= 'Z'
     true

     julia> 'x' - 'a'
     23

     julia> 'A' + 1
     'B'

 String Basics
 -------------

 String literals are delimited by double quotes or triple double quotes:

 .. doctest::

     julia> str = "Hello, world.\n"
     "Hello, world.\n"

     julia> """Contains "quote" characters"""
     "Contains \"quote\" characters"

 If you want to extract a character from a string, you index into it:

 .. doctest::

     julia> str[1]
     'H'

     julia> str[6]
     ','

     julia> str[end]
     '\n'

 All indexing in Julia is 1-based: the first element of any
 integer-indexed object is found at index 1, and the last
 element is found at index ``n``, when the string has
 a length of ``n``.

 In any indexing expression, the keyword ``end`` can be used as a
 shorthand for the last index (computed by :func:`endof(str) <endof>`).
 You can perform arithmetic and other operations with ``end``, just like
 a normal value:

 .. doctest::

     julia> str[end-1]
     '.'

     julia> str[end÷2]
     ' '

 Using an index less than 1 or greater than ``end`` raises an error::

     julia> str[0]
     ERROR: BoundsError: attempt to access 14-element Array{UInt8,1} at index [0]

     julia> str[end+1]
     ERROR: BoundsError: attempt to access 14-element Array{UInt8,1} at index [15]

 You can also extract a substring using range indexing:

 .. doctest::

     julia> str[4:9]
     "lo, wo"

 Notice that the expressions ``str[k]`` and ``str[k:k]`` do not give the same result:

 .. doctest::

     julia> str[6]
     ','

     julia> str[6:6]
     ","

 The former is a single character value of type :obj:`Char`, while the
 latter is a string value that happens to contain only a single
 character. In Julia these are very different things.

 Unicode and UTF-8
 -----------------

 Julia fully supports Unicode characters and strings. As `discussed
 above <#characters>`_, in character literals, Unicode code points can be
 represented using Unicode ``\u`` and ``\U`` escape sequences, as well as
 all the standard C escape sequences. These can likewise be used to write
 string literals:

 .. doctest::

     julia> s = "\u2200 x \u2203 y"
     "∀ x ∃ y"

 Whether these Unicode characters are displayed as escapes or shown as
 special characters depends on your terminal's locale settings and its
 support for Unicode. String literals are encoded using the UTF-8
 encoding. UTF-8 is a variable-width encoding, meaning that not all
 characters are encoded in the same number of bytes. In UTF-8, ASCII
 characters — i.e. those with code points less than 0x80 (128) — are
 encoded as they are in ASCII, using a single byte, while code points
 0x80 and above are encoded using multiple bytes — up to four per
 character. This means that not every byte index into a UTF-8 string is
 necessarily a valid index for a character. If you index into a string at
 such an invalid byte index, an error is thrown:

 .. doctest::

     julia> s[1]
     '∀'

     julia> s[2]
     ERROR: UnicodeError: invalid character index
      in slow_utf8_next(::Array{UInt8,1}, ::UInt8, ::Int64) at ./strings/string.jl:67
      in next at ./strings/string.jl:92 [inlined]
      in getindex(::String, ::Int64) at ./strings/basic.jl:70
      ...

     julia> s[3]
     ERROR: UnicodeError: invalid character index
      in slow_utf8_next(::Array{UInt8,1}, ::UInt8, ::Int64) at ./strings/string.jl:67
      in next at ./strings/string.jl:92 [inlined]
      in getindex(::String, ::Int64) at ./strings/basic.jl:70
      ...

     julia> s[4]
     ' '

 In this case, the character ``∀`` is a three-byte character, so the
 indices 2 and 3 are invalid and the next character's index is 4; this
 next valid index can be computed by :func:`nextind(s,1) <nextind>`,
 and the next index after that by ``nextind(s,4)`` and so on.

 Because of variable-length encodings, the number of characters in a
 string (given by :func:`length(s) <length>`) is not always the same as the last index.
 If you iterate through the indices 1 through :func:`endof(s) <endof>` and index
 into ``s``, the sequence of characters returned when errors aren't
 thrown is the sequence of characters comprising the string ``s``.
 Thus we have the identity that ``length(s) <= endof(s)``, since each
 character in a string must have its own index. The following is an
 inefficient and verbose way to iterate through the characters of ``s``:

 .. doctest::

     julia> for i = 1:endof(s)
                try
                    println(s[i])
                catch
                    # ignore the index error
                end
            end
     ∀
     <BLANKLINE>
     x
     <BLANKLINE>
     ∃
     <BLANKLINE>
     y

 The blank lines actually have spaces on them. Fortunately, the above
 awkward idiom is unnecessary for iterating through the characters in a
 string, since you can just use the string as an iterable object, no
 exception handling required:

 .. doctest::

     julia> for c in s
                println(c)
            end
     ∀
     <BLANKLINE>
     x
     <BLANKLINE>
     ∃
     <BLANKLINE>
     y

 Julia uses the UTF-8 encoding by default, and support for new encodings can
 be added by packages. For example, the `LegacyStrings.jl
 <https://github.com/JuliaArchive/LegacyStrings.jl>`_ package implements
 ``UTF16String`` and ``UTF32String`` types. Additional discussion of other
 encodings and how to implement support for them is beyond the scope of this
 document for the time being. For further discussion of UTF-8 encoding issues,
 see the section below on `byte array literals <#Byte+Array+Literals>`_.
 The :func:`transcode` function is provided to convert data between
 the various UTF-xx encodings, primarily for working with external
 data and libraries.

 .. _man-string-interpolation:

 Interpolation
 -------------

 One of the most common and useful string operations is concatenation:

 .. doctest::

     julia> greet = "Hello"
     "Hello"

     julia> whom = "world"
     "world"

     julia> string(greet, ", ", whom, ".\n")
     "Hello, world.\n"

 Constructing strings like this can become a bit cumbersome, however. To
 reduce the need for these verbose calls to :func:`string`, Julia allows
 interpolation into string literals using ``$``, as in Perl:

 .. doctest::

     julia> "$greet, $whom.\n"
     "Hello, world.\n"

 This is more readable and convenient and equivalent to the above string
 concatenation — the system rewrites this apparent single string literal
 into a concatenation of string literals with variables.

 The shortest complete expression after the ``$`` is taken as the
 expression whose value is to be interpolated into the string. Thus, you
 can interpolate any expression into a string using parentheses:

 .. doctest::

     julia> "1 + 2 = $(1 + 2)"
     "1 + 2 = 3"

 Both concatenation and string interpolation call
 :func:`string` to convert objects into string form. Most
 non-:obj:`AbstractString` objects are converted to strings closely
 corresponding to how they are entered as literal expressions:

 .. doctest::

     julia> v = [1,2,3]
     3-element Array{Int64,1}:
      1
      2
      3

     julia> "v: $v"
     "v: [1,2,3]"

 :func:`string` is the identity for :obj:`AbstractString` and :obj:`Char`
 values, so these are interpolated into strings as themselves, unquoted
 and unescaped:

 .. doctest::

     julia> c = 'x'
     'x'

     julia> "hi, $c"
     "hi, x"

 To include a literal ``$`` in a string literal, escape it with a
 backslash:

 .. doctest::

     julia> print("I have \$100 in my account.\n")
     I have $100 in my account.

 Triple-Quoted String Literals
 -----------------------------

 When strings are created using triple-quotes (``"""..."""``) they have some
 special behavior that can be useful for creating longer blocks of text. First,
 if the opening ``"""`` is followed by a newline, the newline is stripped from
 the resulting string.

 ::

     """hello"""

 is equivalent to

 ::

     """
     hello"""

 but

 ::

     """

     hello"""

 will contain a literal newline at the beginning. Trailing whitespace is left
 unaltered. They can contain ``"`` symbols without escaping. Triple-quoted strings
 are also dedented to the level of the least-indented line. This is useful for
 defining strings within code that is indented. For example:

 .. doctest::

     julia> str = """
                Hello,
                world.
              """
     "  Hello,\n  world.\n"

 In this case the final (empty) line before the closing ``"""`` sets the
 indentation level.

 Note that line breaks in literal strings, whether single- or triple-quoted,
 result in a newline (LF) character ``\n`` in the string, even if your
 editor uses a carriage return ``\r`` (CR) or CRLF combination to end lines.
 To include a CR in a string, use an explicit escape ``\r``; for example,
 you can enter the literal string ``"a CRLF line ending\r\n"``.

 Common Operations
 -----------------

 You can lexicographically compare strings using the standard comparison
 operators:

 .. doctest::

     julia> "abracadabra" < "xylophone"
     true

     julia> "abracadabra" == "xylophone"
     false

     julia> "Hello, world." != "Goodbye, world."
     true

     julia> "1 + 2 = 3" == "1 + 2 = $(1 + 2)"
     true

 You can search for the index of a particular character using the
 :func:`search` function:

 .. doctest::

     julia> search("xylophone", 'x')
     1

     julia> search("xylophone", 'p')
     5

     julia> search("xylophone", 'z')
     0

 You can start the search for a character at a given offset by providing
 a third argument:

 .. doctest::

     julia> search("xylophone", 'o')
     4

     julia> search("xylophone", 'o', 5)
     7

     julia> search("xylophone", 'o', 8)
     0

 You can use the :func:`contains` function to check if a substring is
 contained in a string:

 .. doctest::

     julia> contains("Hello, world.", "world")
     true

     julia> contains("Xylophon", "o")
     true

     julia> contains("Xylophon", "a")
     false

     julia> contains("Xylophon", 'o')
     ERROR: MethodError: no method matching contains(::String, ::Char)
     Closest candidates are:
       contains(!Matched::Function, ::Any, !Matched::Any) at reduce.jl:682
       contains(::AbstractString, !Matched::AbstractString) at strings/search.jl:366
      ...

 The last error is because ``'o'`` is a character literal, and :func:`contains`
 is a generic function that looks for subsequences. To look for an element in a
 sequence, you must use :func:`in` instead.

 Two other handy string functions are :func:`repeat` and :func:`join`:

 .. doctest::

     julia> repeat(".:Z:.", 10)
     ".:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:."

     julia> join(["apples", "bananas", "pineapples"], ", ", " and ")
     "apples, bananas and pineapples"

 Some other useful functions include:

 -  :func:`endof(str) <endof>` gives the maximal (byte) index that can be used to
    index into ``str``.
 -  :func:`length(str) <length>` the number of characters in ``str``.
 -  :func:`i = start(str) <start>` gives the first valid index at which a character
    can be found in ``str`` (typically 1).
 -  :func:`c, j = next(str,i) <next>` returns next character at or after the index
    ``i`` and the next valid character index following that. With
    :func:`start` and :func:`endof`, can be used to iterate through the
    characters in ``str``.
 -  :func:`ind2chr(str,i) <ind2chr>` gives the number of characters in ``str`` up to
    and including any at index ``i``.
 -  :func:`chr2ind(str,j) <chr2ind>` gives the index at which the ``j``\ th character
    in ``str`` occurs.

 .. _man-non-standard-string-literals:

 Non-Standard String Literals
 ----------------------------

 There are situations when you want to construct a string or use string
 semantics, but the behavior of the standard string construct is not
 quite what is needed. For these kinds of situations, Julia provides
 :ref:`non-standard string literals <man-non-standard-string-literals2>`.
 A non-standard string literal looks like
 a regular double-quoted string literal, but is immediately prefixed by
 an identifier, and doesn't behave quite like a normal string literal. The
 convention is that non-standard literals with uppercase prefixes produce
 actual string objects, while those with lowercase prefixes produce
 non-string objects like byte arrays or compiled regular expressions. Regular
 expressions, byte array literals and version number literals, as described
 below, are some examples of non-standard string literals. Other examples are
 given in the :ref:`metaprogramming <man-non-standard-string-literals2>`
 section.


 Regular Expressions
 -------------------

 Julia has Perl-compatible regular expressions (regexes), as provided by
 the `PCRE <http://www.pcre.org/>`_ library. Regular expressions are
 related to strings in two ways: the obvious connection is that regular
 expressions are used to find regular patterns in strings; the other
 connection is that regular expressions are themselves input as strings,
 which are parsed into a state machine that can be used to efficiently
 search for patterns in strings. In Julia, regular expressions are input
 using non-standard string literals prefixed with various identifiers
 beginning with ``r``. The most basic regular expression literal without
 any options turned on just uses ``r"..."``:

 .. doctest::

     julia> r"^\s*(?:#|$)"
     r"^\s*(?:#|$)"

     julia> typeof(ans)
     Regex

 To check if a regex matches a string, use :func:`ismatch`:

 .. doctest::

     julia> ismatch(r"^\s*(?:#|$)", "not a comment")
     false

     julia> ismatch(r"^\s*(?:#|$)", "# a comment")
     true

 As one can see here, :func:`ismatch` simply returns true or false,
 indicating whether the given regex matches the string or not. Commonly,
 however, one wants to know not just whether a string matched, but also
 *how* it matched. To capture this information about a match, use the
 :func:`match` function instead:

 .. doctest::

     julia> match(r"^\s*(?:#|$)", "not a comment")

     julia> match(r"^\s*(?:#|$)", "# a comment")
     RegexMatch("#")

 If the regular expression does not match the given string, :func:`match`
 returns ``nothing`` — a special value that does not print anything at
 the interactive prompt. Other than not printing, it is a completely
 normal value and you can test for it programmatically::

     m = match(r"^\s*(?:#|$)", line)
     if m === nothing
         println("not a comment")
     else
         println("blank or comment")
     end

 If a regular expression does match, the value returned by :func:`match` is a
 :obj:`RegexMatch` object. These objects record how the expression matches,
 including the substring that the pattern matches and any captured
 substrings, if there are any. This example only captures the portion of
 the substring that matches, but perhaps we want to capture any non-blank
 text after the comment character. We could do the following:

 .. doctest::

     julia> m = match(r"^\s*(?:#\s*(.*?)\s*$|$)", "# a comment ")
     RegexMatch("# a comment ", 1="a comment")

 When calling :func:`match`, you have the option to specify an index at
 which to start the search. For example:

 .. doctest::

     julia> m = match(r"[0-9]","aaaa1aaaa2aaaa3",1)
     RegexMatch("1")

     julia> m = match(r"[0-9]","aaaa1aaaa2aaaa3",6)
     RegexMatch("2")

     julia> m = match(r"[0-9]","aaaa1aaaa2aaaa3",11)
     RegexMatch("3")

 You can extract the following info from a :obj:`RegexMatch` object:

 -  the entire substring matched: ``m.match``
 -  the captured substrings as an array of strings: ``m.captures``
 -  the offset at which the whole match begins: ``m.offset``
 -  the offsets of the captured substrings as a vector: ``m.offsets``

 For when a capture doesn't match, instead of a substring, ``m.captures``
 contains ``nothing`` in that position, and ``m.offsets`` has a zero
 offset (recall that indices in Julia are 1-based, so a zero offset into
 a string is invalid). Here is a pair of somewhat contrived examples:

 .. doctest::

     julia> m = match(r"(a|b)(c)?(d)", "acd")
     RegexMatch("acd", 1="a", 2="c", 3="d")

     julia> m.match
     "acd"

     julia> m.captures
     3-element Array{Union{SubString{String},Void},1}:
      "a"
      "c"
      "d"

     julia> m.offset
     1

     julia> m.offsets
     3-element Array{Int64,1}:
      1
      2
      3

     julia> m = match(r"(a|b)(c)?(d)", "ad")
     RegexMatch("ad", 1="a", 2=nothing, 3="d")

     julia> m.match
     "ad"

     julia> m.captures
     3-element Array{Union{SubString{String},Void},1}:
      "a"
      nothing
      "d"

     julia> m.offset
     1

     julia> m.offsets
     3-element Array{Int64,1}:
      1
      0
      2

 It is convenient to have captures returned as an array so that one can
 use destructuring syntax to bind them to local variables::

     julia> first, second, third = m.captures; first
     "a"

 Captures can also be accessed by indexing the :obj:`RegexMatch` object
 with the number or name of the capture group:

 .. doctest::

     julia> m=match(r"(?<hour>\d+):(?<minute>\d+)","12:45")
     RegexMatch("12:45", hour="12", minute="45")
     julia> m[:minute]
     "45"
     julia> m[2]
     "45"

 Captures can be referenced in a substitution string when using :func:`replace`
 by using ``\n`` to refer to the nth capture group and prefixing the
 subsitution string with ``s``. Capture group 0 refers to the entire match object.
 Named capture groups can be referenced in the substitution with ``g<groupname>``.
 For example:

 .. doctest::

     julia> replace("first second", r"(\w+) (?<agroup>\w+)", s"\g<agroup> \1")
     "second first"

 Numbered capture groups can also be referenced as ``\g<n>`` for disambiguation,
 as in:

 .. doctest::

     julia> replace("a", r".", s"\g<0>1")
     "a1"

 You can modify the behavior of regular expressions by some combination
 of the flags ``i``, ``m``, ``s``, and ``x`` after the closing double
 quote mark. These flags have the same meaning as they do in Perl, as
 explained in this excerpt from the `perlre
 manpage <http://perldoc.perl.org/perlre.html#Modifiers>`_::

     i   Do case-insensitive pattern matching.

         If locale matching rules are in effect, the case map is taken
         from the current locale for code points less than 255, and
         from Unicode rules for larger code points. However, matches
         that would cross the Unicode rules/non-Unicode rules boundary
         (ords 255/256) will not succeed.

     m   Treat string as multiple lines.  That is, change "^" and "$"
         from matching the start or end of the string to matching the
         start or end of any line anywhere within the string.

     s   Treat string as single line.  That is, change "." to match any
         character whatsoever, even a newline, which normally it would
         not match.

         Used together, as r""ms, they let the "." match any character
         whatsoever, while still allowing "^" and "$" to match,
         respectively, just after and just before newlines within the
         string.

     x   Tells the regular expression parser to ignore most whitespace
         that is neither backslashed nor within a character class. You
         can use this to break up your regular expression into
         (slightly) more readable parts. The '#' character is also
         treated as a metacharacter introducing a comment, just as in
         ordinary code.

 For example, the following regex has all three flags turned on:

 .. doctest::

     julia> r"a+.*b+.*?d$"ism
     r"a+.*b+.*?d$"ims

     julia> match(r"a+.*b+.*?d$"ism, "Goodbye,\nOh, angry,\nBad world\n")
     RegexMatch("angry,\nBad world")

 Triple-quoted regex strings, of the form ``r"""..."""``, are also
 supported (and may be convenient for regular expressions containing
 quotation marks or newlines).

 Byte Array Literals
 -------------------

 Another useful non-standard string literal is the byte-array string
 literal: ``b"..."``. This form lets you use string notation to express
 literal byte arrays — i.e. arrays of ``UInt8`` values. The rules for
 byte array literals are the following:

 -  ASCII characters and ASCII escapes produce a single byte.
 -  ``\x`` and octal escape sequences produce the *byte* corresponding to
    the escape value.
 -  Unicode escape sequences produce a sequence of bytes encoding that
    code point in UTF-8.

 There is some overlap between these rules since the behavior of ``\x``
 and octal escapes less than 0x80 (128) are covered by both of the first
 two rules, but here these rules agree. Together, these rules allow one
 to easily use ASCII characters, arbitrary byte values, and UTF-8
 sequences to produce arrays of bytes. Here is an example using all
 three:

 .. doctest::

     julia> b"DATA\xff\u2200"
     8-element Array{UInt8,1}:
      0x44
      0x41
      0x54
      0x41
      0xff
      0xe2
      0x88
      0x80

 The ASCII string "DATA" corresponds to the bytes 68, 65, 84, 65.
 ``\xff`` produces the single byte 255. The Unicode escape ``\u2200`` is
 encoded in UTF-8 as the three bytes 226, 136, 128. Note that the
 resulting byte array does not correspond to a valid UTF-8 string — if
 you try to use this as a regular string literal, you will get a syntax
 error:

 .. doctest::

     julia> "DATA\xff\u2200"
     ERROR: syntax: invalid UTF-8 sequence
      ...

 Also observe the significant distinction between ``\xff`` and ``\uff``:
 the former escape sequence encodes the *byte 255*, whereas the latter
 escape sequence represents the *code point 255*, which is encoded as two
 bytes in UTF-8:

 .. doctest::

     julia> b"\xff"
     1-element Array{UInt8,1}:
      0xff

     julia> b"\uff"
     2-element Array{UInt8,1}:
      0xc3
      0xbf

 In character literals, this distinction is glossed over and ``\xff`` is
 allowed to represent the code point 255, because characters *always*
 represent code points. In strings, however, ``\x`` escapes always
 represent bytes, not code points, whereas ``\u`` and ``\U`` escapes
 always represent code points, which are encoded in one or more bytes.
 For code points less than ``\u80``, it happens that the UTF-8
 encoding of each code point is just the single byte produced by the
 corresponding ``\x`` escape, so the distinction can safely be ignored.
 For the escapes ``\x80`` through ``\xff`` as compared to ``\u80``
 through ``\uff``, however, there is a major difference: the former
 escapes all encode single bytes, which — unless followed by very
 specific continuation bytes — do not form valid UTF-8 data, whereas the
 latter escapes all represent Unicode code points with two-byte
 encodings.

 If this is all extremely confusing, try reading `"The Absolute Minimum
 Every Software Developer Absolutely, Positively Must Know About Unicode
 and Character Sets" <http://www.joelonsoftware.com/articles/Unicode.html>`_.
 It's an excellent introduction to Unicode and UTF-8, and may help alleviate
 some confusion regarding the matter.

 .. _man-version-number-literals:

 Version Number Literals
 -----------------------

 Version numbers can easily be expressed with non-standard string literals of
 the form ``v"..."``. Version number literals create :obj:`VersionNumber` objects
 which follow the specifications of `semantic versioning <http://semver.org>`_,
 and therefore are composed of major, minor and patch numeric values, followed
 by pre-release and build alpha-numeric annotations. For example,
 ``v"0.2.1-rc1+win64"`` is broken into major version ``0``, minor version ``2``,
 patch version ``1``, pre-release ``rc1`` and build ``win64``. When entering a
 version literal, everything except the major version number is optional,
 therefore e.g.  ``v"0.2"`` is equivalent to ``v"0.2.0"`` (with empty
 pre-release/build annotations), ``v"2"`` is equivalent to ``v"2.0.0"``, and so
 on.

 :obj:`VersionNumber` objects are mostly useful to easily and correctly compare two
 (or more) versions. For example, the constant ``VERSION`` holds Julia version
 number as a :obj:`VersionNumber` object, and therefore one can define some
 version-specific behavior using simple statements as::

     if v"0.2" <= VERSION < v"0.3-"
         # do something specific to 0.2 release series
     end

 Note that in the above example the non-standard version number ``v"0.3-"`` is
 used, with a trailing ``-``: this notation is a Julia extension of the
 standard, and it's used to indicate a version which is lower than any ``0.3``
 release, including all of its pre-releases. So in the above example the code
 would only run with stable ``0.2`` versions, and exclude such versions as
 ``v"0.3.0-rc1"``. In order to also allow for unstable (i.e. pre-release)
 ``0.2`` versions, the lower bound check should be modified like this: ``v"0.2-"
 <= VERSION``.

 Another non-standard version specification extension allows one to use a trailing
 ``+`` to express an upper limit on build versions, e.g.  ``VERSION >
 v"0.2-rc1+"`` can be used to mean any version above ``0.2-rc1`` and any of its
 builds: it will return ``false`` for version ``v"0.2-rc1+win64"`` and ``true``
 for ``v"0.2-rc2"``.

 It is good practice to use such special versions in comparisons (particularly,
 the trailing ``-`` should always be used on upper bounds unless there's a good
 reason not to), but they must not be used as the actual version number of
 anything, as they are invalid in the semantic versioning scheme.

 Besides being used for the :const:`VERSION` constant, :obj:`VersionNumber` objects are
 widely used in the :mod:`Pkg <Base.Pkg>` module, to specify packages versions and their
 dependencies.



.. _man-strings:

.. currentmodule:: Base

.. 
 *********
  Strings
 *********

*********
 文字列
*********

.. 
 Strings are finite sequences of characters. Of course, the real trouble
 comes when one asks what a character is. The characters that English
 speakers are familiar with are the letters ``A``, ``B``, ``C``, etc.,
 together with numerals and common punctuation symbols. These characters
 are standardized together with a mapping to integer values between 0 and
 127 by the `ASCII <https://en.wikipedia.org/wiki/ASCII>`_ standard. There
 are, of course, many other characters used in non-English languages,
 including variants of the ASCII characters with accents and other
 modifications, related scripts such as Cyrillic and Greek, and scripts
 completely unrelated to ASCII and English, including Arabic, Chinese,
 Hebrew, Hindi, Japanese, and Korean. The
 `Unicode <https://en.wikipedia.org/wiki/Unicode>`_ standard tackles the
 complexities of what exactly a character is, and is generally accepted
 as the definitive standard addressing this problem. Depending on your
 needs, you can either ignore these complexities entirely and just
 pretend that only ASCII characters exist, or you can write code that can
 handle any of the characters or encodings that one may encounter when
 handling non-ASCII text. Julia makes dealing with plain ASCII text
 simple and efficient, and handling Unicode is as simple and efficient as
 possible. In particular, you can write C-style string code to process
 ASCII strings, and they will work as expected, both in terms of
 performance and semantics. If such code encounters non-ASCII text, it
 will gracefully fail with a clear error message, rather than silently
 introducing corrupt results. When this happens, modifying the code to
 handle non-ASCII data is straightforward.

文字列は有限の文字の連続です。実際の問題は、文字が何であるかと尋ねた場合に発生します。
英語を話す人にとって馴染みがある文字は、 ``A`` 、 ``B`` 、 ``C`` などの文字と、数字と句読記号です。
これらの文字は、 `ASCII <https://en.wikipedia.org/wiki/ASCII>`_ 規格は0から127の整数へのマッピングされ標準化されています。
英語以外の言語では、アクセント記号やその他の変形のASCII文字や、英語の言語体系に
関連するキリル文字やギリシャ語、および英語の言語体系に関連しないアラビア語、中国語、
ヘブライ語、ヒンディー語、日本語、韓国語があります。　`Unicode <https://en.wikipedia.org/wiki/Unicode>`_ 規格は、
ある文字が正確に何であるかという複雑な問題に取り組み、また、この問題に対処する決定的な規格として
一般的に認識されています。あなたの必要に応じて、これらの複雑な問題を気にせず
ASCII文字だけを使用したり、非ASCIIテキストを扱う際に見かける様々な文字やエンコードを
使用したコードを書くことができます。JuliaはASCIIテキストの取り扱いをシンプルかつ効率的にし、
Unicodeの取り扱いを可能な限りシンプルかつ効率的にします。特に、ASCIIの文字列を処理するために
C言語スタイルの文字列コードを書くことができ、パフォーマンスと構文的な意味の両面で期待通りの動作をします。
JuliaはASCII以外のテキストが検出された場合、エラーをただ出力するのではなく、
明確なエラーメッセージを出力して処理を終了します。この場合、非ASCIIデータを処理できるように修正するのは簡単です。

.. 
 There are a few noteworthy high-level features about Julia's strings:

Juliaには、文字列に関する注目すべき高度な機能があります。

.. 
 -  The built-in concrete type used for strings (and string literals) in Julia is :obj:`String`.
    This supports the full range of `Unicode <https://en.wikipedia.org/wiki/Unicode>`_ characters
    via the `UTF-8 <https://en.wikipedia.org/wiki/UTF-8>`_ encoding.
    (A :func:`transcode` function is provided to convert to/from other Unicode encodings.)
 -  All string types are subtypes of the abstract type :obj:`AbstractString`,
    and external packages define additional :obj:`AbstractString` subtypes
    (e.g. for other encodings).  If you define a function expecting
    a string argument, you should declare the type as :obj:`AbstractString` in
    order to accept any string type.
 -  Like C and Java, but unlike most dynamic languages, Julia has a
    first-class type representing a single character, called :obj:`Char`.
    This is just a special kind of 32-bit bitstype whose numeric value
    represents a Unicode code point.
 -  As in Java, strings are immutable: the value of an :obj:`AbstractString` object
    cannot be changed. To construct a different string value, you
    construct a new string from parts of other strings.
 -  Conceptually, a string is a *partial function* from indices to
    characters: for some index values, no character value is returned,
    and instead an exception is thrown. This allows for efficient
    indexing into strings by the byte index of an encoded representation
    rather than by a character index, which cannot be implemented both
    efficiently and simply for variable-width encodings of Unicode
    strings.
   
-  Juliaにおける文字列を扱うビルトインの型は :obj:`String` です。これは、 `UTF-8 <https://en.wikipedia.org/wiki/UTF-8>`_ 
   エンコーディングによる全ての `Unicode <https://en.wikipedia.org/wiki/Unicode>`_ をサポートします。
   （Unicodeエンコードの変換を行うために :func:`transcode` 関数が提供されています。）
-  全ての文字列型は抽象型 :obj:`AbstractString` のサブタイプであり、外部パッケージは
   追加の :obj:`AbstractString` サブタイプ（例えばその他のエンコーディング用）を定義します。
   文字列の引数を扱う関数を定義する場合は、任意の文字列型を使用するために :obj:`AbstractString` を型として宣言する必要があります。
-  C言語やJavaと同様に、しかしほとんどの動的言語とは異なり、Juliaには :obj:`Char` と呼ばれる1文字を表す第1級型があります。
   これは数値がUnicodeコードポイントを表す特殊な種類の32ビットのビット型です。
-  Javaと同様に、文字列は不変であり、 :obj:`AbstractString` オブジェクトの値は変更できません。
   異なる文字列の値を作成するには、他の文字列の一部から新しい文字列を作成します。
-  概念的には、文字列はインデックスから文字への部分的な関数です。インデックス値によっては文字値は返されず、
   例外が出力されます。これは、Unicode文字列の可変幅のエンコードに対して効率的かつシンプルに実装できない
   文字インデックスではなく、エンコードされた表現のバイトインデックスによる文字列への効率的なインデックスを可能とします。   

.. _man-characters:

.. 
 Characters
 ----------

文字
----------

.. 
 A :obj:`Char` value represents a single character: it is just a 32-bit
 bitstype with a special literal representation and appropriate arithmetic
 behaviors, whose numeric value is interpreted as a `Unicode code
 point <https://en.wikipedia.org/wiki/Code_point>`_. Here is how :obj:`Char`
 values are input and shown:

:obj:`Char` 値は1つの文字を表します。これは特殊なリテラル表現と適切な算術演算を持つ32ビットのビットタイプであり、
数値は `Unicodeコードポイント <https://en.wikipedia.org/wiki/Code_point>`_ として解釈されます。
:obj:`Char` 値の入力と表示方法は以下の通りです。

.. doctest::

    julia> 'x'
    'x'

    julia> typeof(ans)
    Char

.. 
 You can convert a :obj:`Char` to its integer value, i.e. code point,
 easily:

:obj:`Char` を整数値（コードポイント）に簡単に変換することができます。

.. doctest::

    julia> Int('x')
    120

    julia> typeof(ans)
    Int64

.. 
 On 32-bit architectures, :func:`typeof(ans) <typeof>` will be ``Int32``. You can
 convert an integer value back to a :obj:`Char` just as easily:

32ビットのアーキテクチャ上では、「 :func:`typeof(ans) <typeof>` は ``Int32`` になります。
整数値を簡単に :obj:`Char` に戻すことができます。

.. doctest::

    julia> Char(120)
    'x'

.. 
 Not all integer values are valid Unicode code points, but for
 performance, the :func:`Char` conversion does not check that every character
 value is valid. If you want to check that each converted value is a
 valid code point, use the :func:`isvalid` function:

全ての整数値が有効なUnicodeコードポイントではありませんが、パフォーマンスのために、
:func:`Char` 変換の際に全ての文字値が有効であるかチェックしません。全ての変換された値が有効なコードポイントであるか
確認したい場合は、 :func:`isvalid` 関数を使用してください。

.. doctest::

    julia> Char(0x110000)
    '\U110000'

    julia> isvalid(Char, 0x110000)
    false

.. 
 As of this writing, the valid Unicode code points are ``U+00`` through
 ``U+d7ff`` and ``U+e000`` through ``U+10ffff``. These have not all been
 assigned intelligible meanings yet, nor are they necessarily
 interpretable by applications, but all of these values are considered to
 be valid Unicode characters.

現時点では、有効なコードポイントは ``U+00`` から ``U+d7ff`` までと、 ``U+e000`` から ``U+10ffff`` までとなります。
これらは全て理解可能な意味が割り当てられているわけではなく、また必ずしもアプリケーションによって
解釈可能ではありませんが、これらの値は全て有効なUnicode文字としてみなされます。

.. 
 You can input any Unicode character in single quotes using ``\u``
 followed by up to four hexadecimal digits or ``\U`` followed by up to
 eight hexadecimal digits (the longest valid value only requires six):

``\u`` に続けて最大4桁の16進数を入力するか、 ``\U`` に続けて最大8桁の16進数（6桁から有効）を
入力することで、Unicode文字をシングルクオーテーションとして入力することができます。

.. doctest::

    julia> '\u0'
    '\0'

    julia> '\u78'
    'x'

    julia> '\u2200'
    '∀'

    julia> '\U10ffff'
    '\U10ffff'

.. 
 Julia uses your system's locale and language settings to determine which
 characters can be printed as-is and which must be output using the
 generic, escaped ``\u`` or ``\U`` input forms. In addition to these
 Unicode escape forms, all of `C's traditional escaped input
 forms <https://en.wikipedia.org/wiki/C_syntax#Backslash_escapes>`_ can
 also be used:

Juliaは、システムのローカルと言語設定を使用して、どの文字が現状のまま出力できるか、
およびどの文字が ``\u`` または ``\U`` を使用してエスケープされた状態で出力すべきか判断します。
これらのUnicodeエスケープ形式に加えて、 `C言語のエスケープ形式 <https://en.wikipedia.org/wiki/C_syntax#Backslash_escapes>`_ の
入力も可能です。

.. doctest::

    julia> Int('\0')
    0

    julia> Int('\t')
    9

    julia> Int('\n')
    10

    julia> Int('\e')
    27

    julia> Int('\x7f')
    127

    julia> Int('\177')
    127

    julia> Int('\xff')
    255

.. 
 You can do comparisons and a limited amount of arithmetic with
 :obj:`Char` values:

:obj:`Char` 値を使用して比較と限られた範囲の算術演算を行うことができます。

.. doctest::

    julia> 'A' < 'a'
    true

    julia> 'A' <= 'a' <= 'Z'
    false

    julia> 'A' <= 'X' <= 'Z'
    true

    julia> 'x' - 'a'
    23

    julia> 'A' + 1
    'B'

.. 
 String Basics
 -------------

文字列の基礎
-------------

.. 
 String literals are delimited by double quotes or triple double quotes:

文字列リテラルはダブルクオーテーションまたは3つのダブルクオーテーションで区切られます。

.. doctest::

    julia> str = "Hello, world.\n"
    "Hello, world.\n"

    julia> """Contains "quote" characters"""
    "Contains \"quote\" characters"

.. 
 If you want to extract a character from a string, you index into it:
 
文字列から文字を抽出する場合は、その文字列にインデックスを付ける必要があります。 

.. doctest::

    julia> str[1]
    'H'

    julia> str[6]
    ','

    julia> str[end]
    '\n'

.. 
 All indexing in Julia is 1-based: the first element of any
 integer-indexed object is found at index 1, and the last
 element is found at index ``n``, when the string has
 a length of ``n``.

Juliaの全てのインデックスは1ベースです。整数インデックスオブジェクトの最初の要素は
インデックス1となり、その文字列の長さが ``n`` の場合、その最後の要素は ``n`` となります。

.. 
 In any indexing expression, the keyword ``end`` can be used as a
 shorthand for the last index (computed by :func:`endof(str) <endof>`).
 You can perform arithmetic and other operations with ``end``, just like
 a normal value:

どのインデックス表現でも、キーワード ``end`` は :func:`endof(str) <endof>` で計算される最後のインデックスの
省略形として使用できます。通常の値と同様に、算術演算やその他の処理を ``end`` を使用して行うことができます。

.. doctest::

    julia> str[end-1]
    '.'

    julia> str[end÷2]
    ' '

.. 
 Using an index less than 1 or greater than ``end`` raises an error::

     julia> str[0]
     ERROR: BoundsError: attempt to access 14-element Array{UInt8,1} at index [0]

     julia> str[end+1]
     ERROR: BoundsError: attempt to access 14-element Array{UInt8,1} at index [15]

1以下または ``end`` 以上のインデックスを使用した場合、エラーが出力されます。::

    julia> str[0]
    ERROR: BoundsError: attempt to access 14-element Array{UInt8,1} at index [0]

    julia> str[end+1]
    ERROR: BoundsError: attempt to access 14-element Array{UInt8,1} at index [15]

.. 
 You can also extract a substring using range indexing:

範囲のインデックスを使用することで、部分的な文字列を抽出することができます。

.. doctest::

    julia> str[4:9]
    "lo, wo"

.. 
 Notice that the expressions ``str[k]`` and ``str[k:k]`` do not give the same result:

``str[k]`` と ``str[k:k]`` は同じ結果とならないことに注意してください。

.. doctest::

    julia> str[6]
    ','

    julia> str[6:6]
    ","

.. 
 The former is a single character value of type :obj:`Char`, while the
 latter is a string value that happens to contain only a single
 character. In Julia these are very different things.

前者は :obj:`Char` 型の単一の文字値ですが、後者はこのケースでは1文字だけですが、
文字列値です。Juliaではこれらは異なるものです。

.. 
 Unicode and UTF-8
 -----------------

UnicodeおよびTF-8
-----------------

.. 
 Julia fully supports Unicode characters and strings. As `discussed
 above <#characters>`_, in character literals, Unicode code points can be
 represented using Unicode ``\u`` and ``\U`` escape sequences, as well as
 all the standard C escape sequences. These can likewise be used to write
 string literals:

JuliaはUnicode文字と文字列をサポートします。 `上記 <#文字>`_ の通り、文字リテラルでは、
Unicodeコードポイントは、C言語の標準的なエスケープ方法と同様に、Unicodeの
``\u`` および ``\U`` のエスケープを使用することで現すことができます。
これらは同様に文字列リテラルを書くために使用することができます。

.. doctest::

    julia> s = "\u2200 x \u2203 y"
    "∀ x ∃ y"

.. 
 Whether these Unicode characters are displayed as escapes or shown as
 special characters depends on your terminal's locale settings and its
 support for Unicode. String literals are encoded using the UTF-8
 encoding. UTF-8 is a variable-width encoding, meaning that not all
 characters are encoded in the same number of bytes. In UTF-8, ASCII
 characters — i.e. those with code points less than 0x80 (128) — are
 encoded as they are in ASCII, using a single byte, while code points
 0x80 and above are encoded using multiple bytes — up to four per
 character. This means that not every byte index into a UTF-8 string is
 necessarily a valid index for a character. If you index into a string at
 such an invalid byte index, an error is thrown:

これらのUnicode文字がエスケープとして出力されるか特殊な文字として出力されるかは、
あなたの端末のローカル設定とそのUnicodeのサポートの状況に依存します。文字列リテラルは
UTF-8エンコードを使用してエンコードされます。UFT-8は可変幅のエンコードであり、
全ての文字が同じバイト数でエンコードされるわけではありません。
UTF-8では、0x80 (128)以下のコードポイントのASCII文字は、1バイトでASCIIとしてエンコードされ、
0x80 とそれ以上のコードポイントは、1文字あたり最大4バイトの複数バイトでエンコードされます。
これは、UTF-8文字列の全てのバイトインデックスが必ずしも文字の有効なインデックスであるとは
限らないことを示しています。このような無効なバイトインデックスで文字列にインデックスを付けた場合、エラーが出力されます。

.. doctest::

    julia> s[1]
    '∀'

    julia> s[2]
    ERROR: UnicodeError: invalid character index
     in slow_utf8_next(::Array{UInt8,1}, ::UInt8, ::Int64) at ./strings/string.jl:67
     in next at ./strings/string.jl:92 [inlined]
     in getindex(::String, ::Int64) at ./strings/basic.jl:70
     ...

    julia> s[3]
    ERROR: UnicodeError: invalid character index
     in slow_utf8_next(::Array{UInt8,1}, ::UInt8, ::Int64) at ./strings/string.jl:67
     in next at ./strings/string.jl:92 [inlined]
     in getindex(::String, ::Int64) at ./strings/basic.jl:70
     ...

    julia> s[4]
    ' '

.. 
 In this case, the character ``∀`` is a three-byte character, so the
 indices 2 and 3 are invalid and the next character's index is 4; this
 next valid index can be computed by :func:`nextind(s,1) <nextind>`,
 and the next index after that by ``nextind(s,4)`` and so on.

この場合、 ``∀`` は3バイト文字であるため、インデックス2と3は無効であり、
次のインデックスは4となります。この場合次の有効なインデックスは :func:`nextind(s,1) <nextind>` により計算でき、
この次のインデックスは ``nextind(s,4)`` により計算できます。

.. 
 Because of variable-length encodings, the number of characters in a
 string (given by :func:`length(s) <length>`) is not always the same as the last index.
 If you iterate through the indices 1 through :func:`endof(s) <endof>` and index
 into ``s``, the sequence of characters returned when errors aren't
 thrown is the sequence of characters comprising the string ``s``.
 Thus we have the identity that ``length(s) <= endof(s)``, since each
 character in a string must have its own index. The following is an
 inefficient and verbose way to iterate through the characters of ``s``:

可変長エンコーディングのため、文字列内の文字数（ :func:`length(s) <length>` により取得）は、
常に最後のインデックスと同一というわけではありません。
インデックス1から :func:`endof(s) <endof>` まで繰り返し処理を行い、 ``s`` にインデックスする場合、
エラーが出力されなければ文字列 ``s`` を構成する一連の文字列が返されます。
したがって、文字列内のそれぞれの文字は固有のインデックスを持つため、
``length(s) <= endof(s)`` を使用して識別ができます。
以下は、 ``s`` という文字を反復処理する非効率的で冗長的な方法です。

.. doctest::

    julia> for i = 1:endof(s)
               try
                   println(s[i])
               catch
                   # ignore the index error
               end
           end
    ∀
    <BLANKLINE>
    x
    <BLANKLINE>
    ∃
    <BLANKLINE>
    y

.. 
 The blank lines actually have spaces on them. Fortunately, the above
 awkward idiom is unnecessary for iterating through the characters in a
 string, since you can just use the string as an iterable object, no
 exception handling required:

空白行には実際にスペースがあります。文字列を反復可能なオブジェクトとして使用することで
例外処理は必要ないため、上記の冗長的な記述は不要です。

.. doctest::

    julia> for c in s
               println(c)
           end
    ∀
    <BLANKLINE>
    x
    <BLANKLINE>
    ∃
    <BLANKLINE>
    y

.. 
 Julia uses the UTF-8 encoding by default, and support for new encodings can
 be added by packages. For example, the `LegacyStrings.jl
 <https://github.com/JuliaArchive/LegacyStrings.jl>`_ package implements
 ``UTF16String`` and ``UTF32String`` types. Additional discussion of other
 encodings and how to implement support for them is beyond the scope of this
 document for the time being. For further discussion of UTF-8 encoding issues,
 see the section below on `byte array literals <#Byte+Array+Literals>`_.
 The :func:`transcode` function is provided to convert data between
 the various UTF-xx encodings, primarily for working with external
 data and libraries.

JuliaはデフォルトでUTF-8エンコーディングを使用し、パッケージで追加できる新しいエンコーディングをサポートしています。
例えば、 `LegacyStrings.jl <https://github.com/JuliaArchive/LegacyStrings.jl>`_ パッケージは
``UTF16String`` および ``UTF32String`` 型を実装します。
その他のエンコーディングのとその実装方法は当ドキュメントの対象外となります。UTF-8エンコーディングに関する議論については、
以下の `バイト配列リテラル <#Byte+Array+Literals>`_ のセクションを参照してください。 :func:`transcode` 関数は、
様々なUTF-xxエンコーディング間のデータ変換するために用意されており、これは主に外部データとライブラリを扱うためです。

.. _man-string-interpolation:

.. 
 Interpolation
 -------------

補間
-------------

.. 
 One of the most common and useful string operations is concatenation:

最も一般的かつ便利な文字列操作の一つは連結です。

.. doctest::

    julia> greet = "Hello"
    "Hello"

    julia> whom = "world"
    "world"

    julia> string(greet, ", ", whom, ".\n")
    "Hello, world.\n"

.. 
 Constructing strings like this can become a bit cumbersome, however. To
 reduce the need for these verbose calls to :func:`string`, Julia allows
 interpolation into string literals using ``$``, as in Perl:

しかしこのような文字列を作成するのは少し複雑です。 :func:`string` へのこれらの冗長的な呼び出しを減らすため、
Juliaでは、Perlのように ``$`` を使用して文字列リテラルに補間を行うことができます。

.. doctest::

    julia> "$greet, $whom.\n"
    "Hello, world.\n"

.. 
 This is more readable and convenient and equivalent to the above string
 concatenation — the system rewrites this apparent single string literal
 into a concatenation of string literals with variables.

これは読みやすく便利で、上記の文字列連結と同等です。システムは、単一文字列リテラルを変数と
文字列リテラルの連結に書き換えます。

.. 
 The shortest complete expression after the ``$`` is taken as the
 expression whose value is to be interpolated into the string. Thus, you
 can interpolate any expression into a string using parentheses:

``$`` の後の最短の完全な式は、値が文字列に補間される式として解釈されます。
したがって、括弧を使用して任意の式を文字列に補間できます。

.. doctest::

    julia> "1 + 2 = $(1 + 2)"
    "1 + 2 = 3"

.. 
 Both concatenation and string interpolation call
 :func:`string` to convert objects into string form. Most
 non-:obj:`AbstractString` objects are converted to strings closely
 corresponding to how they are entered as literal expressions:

補間と文字列補間の両方ともオブジェクトを文字列形式に変換するために :func:`string` を呼び出します。
ほとんどの非 :obj:`AbstractString` オブジェクトは、どのようにリテラル表現として入力されるかに対応する文字列に変換されます。

.. doctest::

    julia> v = [1,2,3]
    3-element Array{Int64,1}:
     1
     2
     3

    julia> "v: $v"
    "v: [1,2,3]"

.. 
 :func:`string` is the identity for :obj:`AbstractString` and :obj:`Char`
 values, so these are interpolated into strings as themselves, unquoted
 and unescaped:

:func:`string` は :obj:`AbstractString` および :obj:`Char` 値と同一です。そのため、これらはクオーテーションが付かず、
エスケープもされずにそのまま文字列に補間されます。

.. doctest::

    julia> c = 'x'
    'x'

    julia> "hi, $c"
    "hi, x"

.. 
 To include a literal ``$`` in a string literal, escape it with a
 backslash:

文字列リテラルにリテラル ``$`` を含める場合は、バックスラッシュを使ってエスケープする必要があります。

.. doctest::

    julia> print("I have \$100 in my account.\n")
    I have $100 in my account.

.. 
 Triple-Quoted String Literals
 -----------------------------

3つのダブルクオーテーション
-----------------------------

.. 
 When strings are created using triple-quotes (``"""..."""``) they have some
 special behavior that can be useful for creating longer blocks of text. First,
 if the opening ``"""`` is followed by a newline, the newline is stripped from
 the resulting string.

文字列が3のダブルクオーテーション（ ``"""..."""`` ）を使用して作成される場合、
長いテキストを作成するのに便利な特殊な動作をします。まず、始めの ``"""`` の後ろに改行が続いた場合、
結果の文字列から改行は取り除かれます。

::

    """hello"""

.. 
 is equivalent to

は以下と同等です。

::

    """
    hello"""

.. 
 but

しかし、

::

    """

    hello"""

.. 
 will contain a literal newline at the beginning. Trailing whitespace is left
 unaltered. They can contain ``"`` symbols without escaping. Triple-quoted strings
 are also dedented to the level of the least-indented line. This is useful for
 defining strings within code that is indented. For example:

最初にリテラル改行を含みます。末尾の空白は変更されません。これらはエスケープなしで ``"`` 記号を含めることができます。
3つのダブルクオーテーションで囲まれた文字列は、最もインデントされていない行合わせてインデントします。
これはインデントされたコードないの文字列を定義するのに便利です。例えば、

.. doctest::

    julia> str = """
               Hello,
               world.
             """
    "  Hello,\n  world.\n"

.. 
 In this case the final (empty) line before the closing ``"""`` sets the
 indentation level.

この場合、閉じる ``"""`` の前の最後の（空の）行はインデントレベルを定義します。

.. 
 Note that line breaks in literal strings, whether single- or triple-quoted,
 result in a newline (LF) character ``\n`` in the string, even if your
 editor uses a carriage return ``\r`` (CR) or CRLF combination to end lines.
 To include a CR in a string, use an explicit escape ``\r``; for example,
 you can enter the literal string ``"a CRLF line ending\r\n"``.

シングルクオートかトリプルクオートかどうかに関係なく、文字列リテラルの改行は、
たとえあなたのエディタがキャッリジリターン ``\r`` （CR）またはCRLFの組み合わせを行末に使っているとしても、
文字列内の改行（LF）文字 ``\n`` になります。文字列にCRを含めるためには、明示的に ``\r`` でエスケープを使用してください。
例えば、文字列リテラルに ``"a CRLF line ending\r\n"`` を入力することができます。

.. 
 Common Operations
 -----------------

共通処理
-----------------

.. 
 You can lexicographically compare strings using the standard comparison
 operators:
 
文字列を標準比較演算子を使用して比較できます。 

.. doctest::

    julia> "abracadabra" < "xylophone"
    true

    julia> "abracadabra" == "xylophone"
    false

    julia> "Hello, world." != "Goodbye, world."
    true

    julia> "1 + 2 = 3" == "1 + 2 = $(1 + 2)"
    true

.. 
 You can search for the index of a particular character using the
 :func:`search` function:

:func:`search` 関数を使用して特定の文字のインデックスを検索することができます。

.. doctest::

    julia> search("xylophone", 'x')
    1

    julia> search("xylophone", 'p')
    5

    julia> search("xylophone", 'z')
    0

.. 
 You can start the search for a character at a given offset by providing
 a third argument:

3番目の引数を指定することで、指定されたオフセットで文字の検索をすることができます。

.. doctest::

    julia> search("xylophone", 'o')
    4

    julia> search("xylophone", 'o', 5)
    7

    julia> search("xylophone", 'o', 8)
    0

.. 
 You can use the :func:`contains` function to check if a substring is
 contained in a string:

:func:`contains` 関数を使用して、文字列に部分文字列が含まれているか確認することができます。

.. doctest::

    julia> contains("Hello, world.", "world")
    true

    julia> contains("Xylophon", "o")
    true

    julia> contains("Xylophon", "a")
    false

    julia> contains("Xylophon", 'o')
    ERROR: MethodError: no method matching contains(::String, ::Char)
    Closest candidates are:
      contains(!Matched::Function, ::Any, !Matched::Any) at reduce.jl:682
      contains(::AbstractString, !Matched::AbstractString) at strings/search.jl:366
     ...

.. 
 The last error is because ``'o'`` is a character literal, and :func:`contains`
 is a generic function that looks for subsequences. To look for an element in a
 sequence, you must use :func:`in` instead.
 
最後のエラーは、 ``'o'`` が文字リテラルであり、 :func:`contains` は部分列を探す汎用関数であるためです。
文字列内の要素を探すためには、 :func:`in` を使用する必要があります。 

.. 
 Two other handy string functions are :func:`repeat` and :func:`join`:

その他2つの便利な文字列関数は :func:`repeat` および :func:`join` 関数です。

.. doctest::

    julia> repeat(".:Z:.", 10)
    ".:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:..:Z:."

    julia> join(["apples", "bananas", "pineapples"], ", ", " and ")
    "apples, bananas and pineapples"

.. 
 Some other useful functions include:

その他の便利な関数は以下です。

.. 
 -  :func:`endof(str) <endof>` gives the maximal (byte) index that can be used to
    index into ``str``.
 -  :func:`length(str) <length>` the number of characters in ``str``.
 -  :func:`i = start(str) <start>` gives the first valid index at which a character
    can be found in ``str`` (typically 1).
 -  :func:`c, j = next(str,i) <next>` returns next character at or after the index
    ``i`` and the next valid character index following that. With
    :func:`start` and :func:`endof`, can be used to iterate through the
    characters in ``str``.
 -  :func:`ind2chr(str,i) <ind2chr>` gives the number of characters in ``str`` up to
    and including any at index ``i``.
 -  :func:`chr2ind(str,j) <chr2ind>` gives the index at which the ``j``\ th character
    in ``str`` occurs.
   
-  :func:`endof(str) <endof>` は ``str`` へのインデックス付けに使用できる最大の（バイト）のインデックスを返します。
-  :func:`length(str) <length>` ： ``str`` の文字数
-  :func:`i = start(str) <start>` は、文字が ``str`` （通常は1）にある最初の有効なインデックスを返します。
-  :func:`c, j = next(str,i) <next>` は、インデックスの文字またはインデックス ``i`` の次の文字とそれに続く有効な文字を返します。:func:`start` および :func:`endof` を使って、 ``str`` の文字を反復処理をすることができます。
-  :func:`ind2chr(str,i) <ind2chr>` は、インデックス ``i`` を含めた ``str`` の文字数を与えます。
-  :func:`chr2ind(str,j) <chr2ind>` は ``str`` の ``j`` 番目の文字があるインデックスを返します。 
   

.. _man-non-standard-string-literals:

.. 
 Non-Standard String Literals
 ----------------------------

非標準文字列リテラル
----------------------------

.. 
 There are situations when you want to construct a string or use string
 semantics, but the behavior of the standard string construct is not
 quite what is needed. For these kinds of situations, Julia provides
 :ref:`non-standard string literals <man-non-standard-string-literals2>`.
 A non-standard string literal looks like
 a regular double-quoted string literal, but is immediately prefixed by
 an identifier, and doesn't behave quite like a normal string literal. The
 convention is that non-standard literals with uppercase prefixes produce
 actual string objects, while those with lowercase prefixes produce
 non-string objects like byte arrays or compiled regular expressions. Regular
 expressions, byte array literals and version number literals, as described
 below, are some examples of non-standard string literals. Other examples are
 given in the :ref:`metaprogramming <man-non-standard-string-literals2>`
 section.

文字列を作成したり文字列を使用したいが、標準的な文字列構成の挙動が必要ではない場合があるかもしれません。
このような場合のために、Juliaは :ref:`非標準文字列リテラル <man-non-standard-string-literals2>` を提供しています。
非標準文字列リテラルは通常のダブルクオート文字列リテラルと同じように見えますが、すぐに識別子が前につけられ、
通常の文字列リテラルとは異なった挙動をします。大文字の接頭辞を非標準リテラルは実際の文字列オブジェクトを生成し、
一方で小文字の接頭辞を持つものはバイト配列やコンパイルされた正規表現のような非文字列オブジェクトを生成します。
以下に説明されている正規表現（バイト配列リテラルおよびバージョン番号リテラル）は、非標準文字列リテラルの例です。
他の例は、 :ref:`メタプログラミングセクション <man-non-standard-string-literals2>` に記載されています。


.. 
 Regular Expressions
 -------------------

正規表現
-------------------

.. 
 Julia has Perl-compatible regular expressions (regexes), as provided by
 the `PCRE <http://www.pcre.org/>`_ library. Regular expressions are
 related to strings in two ways: the obvious connection is that regular
 expressions are used to find regular patterns in strings; the other
 connection is that regular expressions are themselves input as strings,
 which are parsed into a state machine that can be used to efficiently
 search for patterns in strings. In Julia, regular expressions are input
 using non-standard string literals prefixed with various identifiers
 beginning with ``r``. The most basic regular expression literal without
 any options turned on just uses ``r"..."``:

JuliaはPerlと互換性がある `PCRE <http://www.pcre.org/>`_ ライブラリが提供する正規表現があります。正規表現は2つの点で文字列と関連しています。
1つは、正規表現は文字列内の規則的なパターンを見つけるために使用されるという明確な関連性があります。
もう1つは、正規表現自体が文字列として入力され、文字列のパターンを効率的に検索するために使用できるステートマシンに解析されます。
Juliaでは、正規表現は ``r`` で始まる様々な識別子の接頭辞が付いた非標準文字列リテラルを使用して入力されます。
オプションがない最も基本的な正規表現リテラルは、 ``r"..."`` を使用します。

.. doctest::

    julia> r"^\s*(?:#|$)"
    r"^\s*(?:#|$)"

    julia> typeof(ans)
    Regex

.. 
 To check if a regex matches a string, use :func:`ismatch`:

正規表現が文字列に一致するか確認するためには、 :func:`ismatch`: を使用します。

.. doctest::

    julia> ismatch(r"^\s*(?:#|$)", "not a comment")
    false

    julia> ismatch(r"^\s*(?:#|$)", "# a comment")
    true
.. 
 As one can see here, :func:`ismatch` simply returns true or false,
 indicating whether the given regex matches the string or not. Commonly,
 however, one wants to know not just whether a string matched, but also
 *how* it matched. To capture this information about a match, use the
 :func:`match` function instead:

見てわかる通り、 :func:`ismatch` は単純に正規表現が文字列と一致するかどうかを示しすtrueまたはfalseを返します。
しかし一般的には、文字列が一致するかどうかだけでなく、どのように一致するかを知りたいと考えるかもしれません。
一致に関するこの情報を得るために、代わりに :func:`match` 関数を使用してください。

.. doctest::

    julia> match(r"^\s*(?:#|$)", "not a comment")

    julia> match(r"^\s*(?:#|$)", "# a comment")
    RegexMatch("#")

.. 
 If the regular expression does not match the given string, :func:`match`
 returns ``nothing`` — a special value that does not print anything at
 the interactive prompt. Other than not printing, it is a completely
 normal value and you can test for it programmatically::

    m = match(r"^\s*(?:#|$)", line)
    if m === nothing
        println("not a comment")
    else
        println("blank or comment")
    end

正規表現が文字列と一致しない場合、 :func:`match` は ``nothing`` 返します。これは対話型プロンプトで何も出力しない特別な値です。
これは出力しない場合を除き、正常な値であり、プログラムでテストすることができます。::

    m = match(r"^\s*(?:#|$)", line)
    if m === nothing
        println("not a comment")
    else
        println("blank or comment")
    end

.. 
 If a regular expression does match, the value returned by :func:`match` is a
 :obj:`RegexMatch` object. These objects record how the expression matches,
 including the substring that the pattern matches and any captured
 substrings, if there are any. This example only captures the portion of
 the substring that matches, but perhaps we want to capture any non-blank
 text after the comment character. We could do the following:

正規表現が一致する場合、 :func:`match` によって返される値は :obj:`RegexMatch` オブジェクトとなります。
このオブジェクトは、パターンが一致する部分文字列およびもしあればキャプチャされた部分文字列を含む、
その正規表現がどのように一致するかを記録します。この例では、部分文字列の一部だけを取得していますが、
コメント文字後の空白以外のテキストを取得したい場合があるかもしれません。その場合は以下でできます。

.. doctest::

    julia> m = match(r"^\s*(?:#\s*(.*?)\s*$|$)", "# a comment ")
    RegexMatch("# a comment ", 1="a comment")

.. 
 When calling :func:`match`, you have the option to specify an index at
 which to start the search. For example:

:func:`match` を呼び出すとき、どのインデックスから検索を開始するかを指定するオプションがあります。例えば、

.. doctest::

    julia> m = match(r"[0-9]","aaaa1aaaa2aaaa3",1)
    RegexMatch("1")

    julia> m = match(r"[0-9]","aaaa1aaaa2aaaa3",6)
    RegexMatch("2")

    julia> m = match(r"[0-9]","aaaa1aaaa2aaaa3",11)
    RegexMatch("3")

.. 
 You can extract the following info from a :obj:`RegexMatch` object:

:obj:`RegexMatch` オブジェクトから次の情報を抽出することができます。

.. 
 -  the entire substring matched: ``m.match``
 -  the captured substrings as an array of strings: ``m.captures``
 -  the offset at which the whole match begins: ``m.offset``
 -  the offsets of the captured substrings as a vector: ``m.offsets``

-  全ての一致した部分文字列： ``m.match``
-  文字列の配列としてキャプチャされた部分文字列： ``m.captures``
-  全体の一致が始まるオフセット： ``m.offset``
-  ベクトルとしてキャプチャされた部分文字列のオフセット： ``m.offsets``

.. 
 For when a capture doesn't match, instead of a substring, ``m.captures``
 contains ``nothing`` in that position, and ``m.offsets`` has a zero
 offset (recall that indices in Julia are 1-based, so a zero offset into
 a string is invalid). Here is a pair of somewhat contrived examples:

キャプチャが一致しない場合、部分文字列の代わりに、 ``m.captures`` には ``nothing`` が格納され、
``m.offsets`` にはゼロオフセットが格納されます。（Juliaは1ベースであり、文字列へのゼロオフセットは無効です。）
以下は考慮の末出された例です。

.. doctest::

    julia> m = match(r"(a|b)(c)?(d)", "acd")
    RegexMatch("acd", 1="a", 2="c", 3="d")

    julia> m.match
    "acd"

    julia> m.captures
    3-element Array{Union{SubString{String},Void},1}:
     "a"
     "c"
     "d"

    julia> m.offset
    1

    julia> m.offsets
    3-element Array{Int64,1}:
     1
     2
     3

    julia> m = match(r"(a|b)(c)?(d)", "ad")
    RegexMatch("ad", 1="a", 2=nothing, 3="d")

    julia> m.match
    "ad"

    julia> m.captures
    3-element Array{Union{SubString{String},Void},1}:
     "a"
     nothing
     "d"

    julia> m.offset
    1

    julia> m.offsets
    3-element Array{Int64,1}:
     1
     0
     2

.. 
 It is convenient to have captures returned as an array so that one can
 use destructuring syntax to bind them to local variables::

    julia> first, second, third = m.captures; first
    "a"

キャプチャが配列として返されることで、ローカル変数に紐づける再構成構文を使うことができます。::

    julia> first, second, third = m.captures; first
    "a"

.. 
 Captures can also be accessed by indexing the :obj:`RegexMatch` object
 with the number or name of the capture group:

キャプチャグループの番号または名前で :obj:`RegexMatch` オブジェクトをインデックスすることで、
取得した値にアクセスすることもできます。

.. doctest::

    julia> m=match(r"(?<hour>\d+):(?<minute>\d+)","12:45")
    RegexMatch("12:45", hour="12", minute="45")
    julia> m[:minute]
    "45"
    julia> m[2]
    "45"

.. 
 Captures can be referenced in a substitution string when using :func:`replace`
 by using ``\n`` to refer to the nth capture group and prefixing the
 subsitution string with ``s``. Capture group 0 refers to the entire match object.
 Named capture groups can be referenced in the substitution with ``g<groupname>``.
 For example:

``\n`` を使用してn番目のキャプチャグループを参照し、サブ文字列に ``s`` を接頭辞として使用することで
:func:`replace` を使用した場合、置換文字列でキャプチャを参照できます。キャプチャ・グループ0はマッチ・オブジェクト全体を
参照します。名前付きキャプチャグループは、 ``g<groupname>`` での置換で参照できます。例えば：

.. doctest::

    julia> replace("first second", r"(\w+) (?<agroup>\w+)", s"\g<agroup> \1")
    "second first"

.. 
 Numbered capture groups can also be referenced as ``\g<n>`` for disambiguation,
 as in:

番号付きキャプチャグループは、次のように曖昧さ回避のために ``\g<n>`` として参照することもできます。

.. doctest::

    julia> replace("a", r".", s"\g<0>1")
    "a1"

.. 
 You can modify the behavior of regular expressions by some combination
 of the flags ``i``, ``m``, ``s``, and ``x`` after the closing double
 quote mark. These flags have the same meaning as they do in Perl, as
 explained in this excerpt from the `perlre
 manpage <http://perldoc.perl.org/perlre.html#Modifiers>`_::


    i   Do case-insensitive pattern matching.

        If locale matching rules are in effect, the case map is taken
        from the current locale for code points less than 255, and
        from Unicode rules for larger code points. However, matches
        that would cross the Unicode rules/non-Unicode rules boundary
        (ords 255/256) will not succeed.

    m   Treat string as multiple lines.  That is, change "^" and "$"
        from matching the start or end of the string to matching the
        start or end of any line anywhere within the string.

    s   Treat string as single line.  That is, change "." to match any
        character whatsoever, even a newline, which normally it would
        not match.

        Used together, as r""ms, they let the "." match any character
        whatsoever, while still allowing "^" and "$" to match,
        respectively, just after and just before newlines within the
        string.

    x   Tells the regular expression parser to ignore most whitespace
        that is neither backslashed nor within a character class. You
        can use this to break up your regular expression into
        (slightly) more readable parts. The '#' character is also
        treated as a metacharacter introducing a comment, just as in
        ordinary code.

閉じるダブルクオーテーションの後に、フラグ  ``i`` 、 ``m`` 、 ``s`` および ``x`` の組み合わせによって
正規表現の挙動を変更することができます。これらのフラグは、 `Perlのマニュアルページ <http://perldoc.perl.org/perlre.html#Modifiers>`_ から
抜粋したように、Perlで使用されるのと同じ意味を持っています。::
        
        
    i   Do case-insensitive pattern matching.

        If locale matching rules are in effect, the case map is taken
        from the current locale for code points less than 255, and
        from Unicode rules for larger code points. However, matches
        that would cross the Unicode rules/non-Unicode rules boundary
        (ords 255/256) will not succeed.

    m   Treat string as multiple lines.  That is, change "^" and "$"
        from matching the start or end of the string to matching the
        start or end of any line anywhere within the string.

    s   Treat string as single line.  That is, change "." to match any
        character whatsoever, even a newline, which normally it would
        not match.

        Used together, as r""ms, they let the "." match any character
        whatsoever, while still allowing "^" and "$" to match,
        respectively, just after and just before newlines within the
        string.

    x   Tells the regular expression parser to ignore most whitespace
        that is neither backslashed nor within a character class. You
        can use this to break up your regular expression into
        (slightly) more readable parts. The '#' character is also
        treated as a metacharacter introducing a comment, just as in
        ordinary code.        

.. 
 For example, the following regex has all three flags turned on:

例えば、次の正規表現には3つのフラグがすべて設定されています。

.. doctest::

    julia> r"a+.*b+.*?d$"ism
    r"a+.*b+.*?d$"ims

    julia> match(r"a+.*b+.*?d$"ism, "Goodbye,\nOh, angry,\nBad world\n")
    RegexMatch("angry,\nBad world")

.. 
 Triple-quoted regex strings, of the form ``r"""..."""``, are also
 supported (and may be convenient for regular expressions containing
 quotation marks or newlines).

``r"""..."""`` 形式の3つのダブルクオーテーション付き正規表現文字列もサポートされています。
（また、クオーテーションや改行を含む正規表現にも便利です。）

.. 
 Byte Array Literals
 -------------------

バイト配列リテラル
-------------------

.. 
 Another useful non-standard string literal is the byte-array string
 literal: ``b"..."``. This form lets you use string notation to express
 literal byte arrays — i.e. arrays of ``UInt8`` values. The rules for
 byte array literals are the following:

その他の便利な非標準の文字列リテラルは、バイト配列文字列リテラル（ ``b"..."`` ）です。
この形式は文字列表記を使用してバイト配列リテラル、つまり ``UInt8`` 値の配列を表現できます。バイト配列リテラルのルールは次の通りです。

.. 
 -  ASCII characters and ASCII escapes produce a single byte.
 -  ``\x`` and octal escape sequences produce the *byte* corresponding to
    the escape value.
 -  Unicode escape sequences produce a sequence of bytes encoding that
    code point in UTF-8.
   
-  ASCII文字とASCIIエスケープは1バイトを生成します。
-  ``\x`` と8進エスケープシーケンスは、エスケープした値に対応するバイトを生成します。
-  Unicodeのエスケープシーケンスは、UTF-8でコードポイントをエンコードする一連のバイトを生成します。   

.. 
 There is some overlap between these rules since the behavior of ``\x``
 and octal escapes less than 0x80 (128) are covered by both of the first
 two rules, but here these rules agree. Together, these rules allow one
 to easily use ASCII characters, arbitrary byte values, and UTF-8
 sequences to produce arrays of bytes. Here is an example using all
 three:

``\x`` の動作と0x80（128）未満の8進エスケープは最初の2つのルールの両方が適用されるため、
これらのルールの間にはいくつかの重複がありますが、。これらのルールは共存します。これらのルールにより、
ASCII文字、任意のバイト値、およびUTF-8シーケンスを簡単に使用することができます。この3つの例では全てを使用しています。

.. doctest::

    julia> b"DATA\xff\u2200"
    8-element Array{UInt8,1}:
     0x44
     0x41
     0x54
     0x41
     0xff
     0xe2
     0x88
     0x80

.. 
 The ASCII string "DATA" corresponds to the bytes 68, 65, 84, 65.
 ``\xff`` produces the single byte 255. The Unicode escape ``\u2200`` is
 encoded in UTF-8 as the three bytes 226, 136, 128. Note that the
 resulting byte array does not correspond to a valid UTF-8 string — if
 you try to use this as a regular string literal, you will get a syntax
 error:

ASCII文字列 「DATA」はバイト68、65、84、65に対応します。``\xff`` は1バイト255を生成します。
Unicodeエスケープ文字 ``\u2200`` は、UTF-8で3バイト226、136、128としてエンコードされます。
処理結果のバイト配列は有効なUTF-8文字列に対応しません。これを通常の文字列リテラルとして使用した場合、構文エラーが発生します。

.. doctest::

    julia> "DATA\xff\u2200"
    ERROR: syntax: invalid UTF-8 sequence
     ...

.. 
 Also observe the significant distinction between ``\xff`` and ``\uff``:
 the former escape sequence encodes the *byte 255*, whereas the latter
 escape sequence represents the *code point 255*, which is encoded as two
 bytes in UTF-8:
 
``\xff`` および ``\uff`` の重要な違いについても確認してください。前者のエスケープシーケンスはバイト255をエンコードする一方、
後者のエスケープシーケンスは、UTF-8で2バイトとしてエンコードされたコードポイント255を表します。 

.. doctest::

    julia> b"\xff"
    1-element Array{UInt8,1}:
     0xff

    julia> b"\uff"
    2-element Array{UInt8,1}:
     0xc3
     0xbf

.. 
 In character literals, this distinction is glossed over and ``\xff`` is
 allowed to represent the code point 255, because characters *always*
 represent code points. In strings, however, ``\x`` escapes always
 represent bytes, not code points, whereas ``\u`` and ``\U`` escapes
 always represent code points, which are encoded in one or more bytes.
 For code points less than ``\u80``, it happens that the UTF-8
 encoding of each code point is just the single byte produced by the
 corresponding ``\x`` escape, so the distinction can safely be ignored.
 For the escapes ``\x80`` through ``\xff`` as compared to ``\u80``
 through ``\uff``, however, there is a major difference: the former
 escapes all encode single bytes, which — unless followed by very
 specific continuation bytes — do not form valid UTF-8 data, whereas the
 latter escapes all represent Unicode code points with two-byte
 encodings.

文字リテラルでは、文字は常にコードポイントを表すため、この違いは顕在化せず、
``\xff`` はコードポイント255を表します。しかし文字列では、
``\x`` エスケープはコードポイントではなく常にバイトを表し、一方で ``\u`` および ``\U`` エスケープは
常に1またはそれ以上のバイトでエンコードされたコードポイントを表します。
``\u80`` より小さいコードポイントの場合、各コードポイントのUTF-8エンコーディングは、
対応する ``\x`` エスケープによって生成される1バイトだけなので、この区別は無視しても問題ありません。
しかし、 ``\u80`` から ``\uff`` までと比較して、 ``\x80`` から ``\xff`` までの
エスケープには大きな違いがあります。前者は、特定の継続バイトが続かない限り、
有効なUTF-8データを構成しない全てのシングルバイトをエスケープします。一方で後者は、
2バイトのエンコーディングでUnicodeコードポイントを表す全てをスケープします。

.. 
 If this is all extremely confusing, try reading `"The Absolute Minimum
 Every Software Developer Absolutely, Positively Must Know About Unicode
 and Character Sets" <http://www.joelonsoftware.com/articles/Unicode.html>`_.
 It's an excellent introduction to Unicode and UTF-8, and may help alleviate
 some confusion regarding the matter.

もしこれらについて混乱している場合は、 `"The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets" <http://www.joelonsoftware.com/articles/Unicode.html>`_ を参照してください。
これはUnicodeおよびUTF-8に関するすばらしい紹介です。また、この問題に関する理解に役立つかもしれません。

.. _man-version-number-literals:

.. 
 Version Number Literals
 -----------------------

バージョン番号リテラル
-----------------------

.. 
 Version numbers can easily be expressed with non-standard string literals of
 the form ``v"..."``. Version number literals create :obj:`VersionNumber` objects
 which follow the specifications of `semantic versioning <http://semver.org>`_,
 and therefore are composed of major, minor and patch numeric values, followed
 by pre-release and build alpha-numeric annotations. For example,
 ``v"0.2.1-rc1+win64"`` is broken into major version ``0``, minor version ``2``,
 patch version ``1``, pre-release ``rc1`` and build ``win64``. When entering a
 version literal, everything except the major version number is optional,
 therefore e.g.  ``v"0.2"`` is equivalent to ``v"0.2.0"`` (with empty
 pre-release/build annotations), ``v"2"`` is equivalent to ``v"2.0.0"``, and so
 on.

バージョン番号は、 ``v"..."`` 形式の非標準文字列リテラルで簡単に表すことができます。
バージョン番号リテラルは、`バージョン管理 <http://semver.org>`_ の仕様に従った :obj:`VersionNumber` オブジェクトを生成するため、
バージョン番号リテラルはメジャー、マイナー、パッチの数値で構成され、プレリリース番号が続き、
英数字注釈で構成されます。例えば、 ``v"0.2.1-rc1+win64"`` は、メジャー・バージョン ``0`` 、
マイナー・バージョン ``2`` 、パッチ・バージョン ``1`` 、プレリリース ``rc1`` およびビルド ``win64`` に分割できます。
バージョン番号リテラルを入力するとき、メジャーバージョン番号を除くすべてはオプショナルです。
``v"0.2"`` は ``v"0.2.0"`` （空白のプレリリースおよびビルド注釈が空白の場合）に相当します。 ``v"2"`` は ``v"2.0.0"`` と同一です。

.. 
 :obj:`VersionNumber` objects are mostly useful to easily and correctly compare two
 (or more) versions. For example, the constant ``VERSION`` holds Julia version
 number as a :obj:`VersionNumber` object, and therefore one can define some
 version-specific behavior using simple statements as::

    if v"0.2" <= VERSION < v"0.3-"
        # do something specific to 0.2 release series
    end

:obj:`VersionNumber` オブジェクトは、2つ（またはそれ以上）のバージョンを簡単かつ正確に比較するのに最も役立ちます。
例えば、定数 ``VERSION`` にはJuliaのバージョン番号が :obj:`VersionNumber` オブジェクトとして格納されているため、
単純な文を使用してバージョン固有の動作を定義することができます。::
    
    if v"0.2" <= VERSION < v"0.3-"
        # do something specific to 0.2 release series
    end    

.. 
 Note that in the above example the non-standard version number ``v"0.3-"`` is
 used, with a trailing ``-``: this notation is a Julia extension of the
 standard, and it's used to indicate a version which is lower than any ``0.3``
 release, including all of its pre-releases. So in the above example the code
 would only run with stable ``0.2`` versions, and exclude such versions as
 ``v"0.3.0-rc1"``. In order to also allow for unstable (i.e. pre-release)
 ``0.2`` versions, the lower bound check should be modified like this: ``v"0.2-"
 <= VERSION``.

上記の例では、非標準バージョン番号 ``v"0.3-"`` が使用され、末尾に ``-`` が付いています。
この表記は標準のJuliaの拡張機能であり、これは、全てのプレリリースを含めて ``0.3`` リリースより
低いバージョンをであることを示すために使用されます。したがって、上記の例では、コードは安定した
``0.2`` バージョンでのみ実行され、 ``v"0.3.0-rc1"`` などのバージョンは除外されます。
不安定な（例えばプレリリース） ``0.2`` バージョンを許容するため、下限チェックは次のように変更する必要があります。
``v"0.2-" <= VERSION``

.. 
 Another non-standard version specification extension allows one to use a trailing
 ``+`` to express an upper limit on build versions, e.g.  ``VERSION >
 v"0.2-rc1+"`` can be used to mean any version above ``0.2-rc1`` and any of its
 builds: it will return ``false`` for version ``v"0.2-rc1+win64"`` and ``true``
 for ``v"0.2-rc2"``.

別の非標準バージョン仕様拡張では、後続の ``+`` を使用してビルドバージョンの上限を表すことができます。
例えば、 ``VERSION > v"0.2-rc1+"`` は、 ``0.2-rc1`` 以上の全てをバージョンを指定するために使用することができ、
``v"0.2-rc1+win64"`` では ``false`` を返し、 ``v"0.2-rc2"`` では ``true`` を返します。

.. 
 It is good practice to use such special versions in comparisons (particularly,
 the trailing ``-`` should always be used on upper bounds unless there's a good
 reason not to), but they must not be used as the actual version number of
 anything, as they are invalid in the semantic versioning scheme.

このような特殊なバージョンを比較のために使用するのは有益です。（特に、後続の ``-`` は、必要の場合を除き、
常に上限を上限を示すために使用すべきです。）しかし、それらはバージョンスキームでは無効であるため、
実際のバージョン番号として使用できません。

.. 
 Besides being used for the :const:`VERSION` constant, :obj:`VersionNumber` objects are
 widely used in the :mod:`Pkg <Base.Pkg>` module, to specify packages versions and their
 dependencies.

:const:`VERSION` 定数に使用されるほかに、 :obj:`VersionNumber` オブジェクトはパッケージのバージョンと
その依存関係を指定するために :mod:`Pkg <Base.Pkg>` モジュールで広く使用されています。
