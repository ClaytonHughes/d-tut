Unicode
=======

D is focused on UTF-8.
Source files are UTF-8.

Strings
-------

The builtin D type ``string``
is just an immutable char array.
However, if you iterate over it,
dchar code points are returned.
Thus the following different lengths.

.. code-block:: d

   string foo = "bär";
   assert(foo.length == 4);
   assert(walkLength(foo) == 3);

..

  Wouldn't it be more consistent to have <string>.length return the
  number of UTF-8 characters as well?
  – `nickles <http://forum.dlang.org/post/phybgondilwmlwrflawx@forum.dlang.org>`_

This seems to be a weird design choice at first,
but once you learn more about Unicode
it is the only sane choice
if you also care about performance.
To understand this some background of Unicode is necessary.

Code Point
  One Unicode character, a number between 0 and 1,114,112.
  The Unicode standard defines the meaning of these numbers.

Encoding
  An encoding specifies how to represent code points in bytes.

Code Unit
  Depending on the encoding a code point might be split into multiple units.
  For example, in UTF-8 one code unit is one byte
  and the code point ``ä`` above consists of two units (in hexcode) ``c3`` and ``a4``.

Glyph
  One character as humans perceive it.
  Unicode allows to combine code points.
  For example ``ä`` can also be produced with ``a`` and the
  `combining diaeresis <http://www.fileformat.info/info/unicode/char/308/index.htm>`_ code point.

What people usually want to count are glyphs,
but this is only possible with all the knowledge of the Unicode tables.
Counting the code points only requires knowledge of the encoding.
Counting the bytes is only a lookup in D,
since array lengths are stored in memory.

Transcoding
-----------

Since not everything your program consumes is UTF-8,
the standard library provides the ``std.encoding`` module for conversion.

.. code-block:: d

  wstring ws;
  transcode("hello world",ws);
    // transcode from UTF-8 to UTF-16

  Latin1String ls;
  transcode(ws, ls);
    // transcode from UTF-16 to ISO-8859-1

As source files are UTF-8,
string literals are UTF-8 as well.
The ``wstring`` type is UTF-16,
and ``transcode`` identifies source and target encoding from the types of its arguments.

.. seealso::

   `The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets <http://www.joelonsoftware.com/articles/Unicode.html>`_
