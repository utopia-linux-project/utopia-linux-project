# Roadmap

The UTOPICS character set consists of 512 'rows' (numbered 0 to 511). Each row contains 128 'columns'. Row 0 is identical to US-ASCII.

Row 0 - US-ASCII
Row 1 - Specials
Row 2 - General Punctuation
...
Rows 508 & 509 - Braille
Rows 510 & 511 - DOS Code Page 437  ("Direct-to-hardware" mapping on VGA-compatible devices.)

# UTOPICS Codepoint Notation

The standard way to notate a codepoint in UTOPICS is as _X.Y_, where X is the row number, and Y is the column (starting at 0, up to a maximum of 127) within that block. X and Y are in decimal format, not hex.

This notation is for the convenience of humans, not machines. It's designed to be easy to type on a numeric keypad (for Alt-key entry), and to be distinct from the notation used for Unicode codepoints.

# UTOPICS Binary Representation

A UTOPICS character can fit into a 16-bit integer. (Hence, UTOPICS can be described as a 16-bit character set.)

    u16 character = (row & 0x1FF) << 7 | (column & 0x7F);

However, when transmitting UTOPICS data between different applications, it is recommended to use UTF-8 encoding. This allows UTOPICS data to be mixed with Unicode data with no ambiguity.

To encode UTOPICS data in UTF-8, first a transformation is applied:

* For characters in row 0 (ASCII), no transformation is applied.
* For other characters, 0xF0000 is added to the 16-bit integer value of the character. This puts the character in Plane 15 of Unicode (which is Private Use Plane).

Then the result is UTF-8 encoded as usual. (This means that every non-ASCII UTOPICS character takes up *four* bytes in UTF-8, with the first byte being 0xF7. If you think that's a lot of overhead, well, that's the price of interoperability with Unicode.)

Although UTOPICS uses UTF-8, it doesn't not use the Unicode character set (ISO 10646). (In this context, "UTF" could be said to stand for "Utopia Transformation Format", rathern than Unicode Transformation Format.)

# Why not Unicode?

Unicode requires a degree of complexity that is largely incompatible with the Unix design philosophy. It would require that language-specific processing for many languages be added to the Linux kernel, like repositioning the reph in Hindi, or contextual shaping of Arabic letters. Up until now, the Linux kernel has always been language-independent, and that's how it should remain. 

The Utopia Linux kernel will not contain any language-specific processing. It will, however, add broad support for right-to-left characters, larger character sets, and "overlay" (non-spacing) characters.

Here are some of the ways that Unicode differs from UTOPICS.

* Unicode follows the philosophy that each codepoint should correspond to a single "letter", even if that letter takes different forms in different contexts. In contrast, each UTOPICS codepoint corresponds to a glyph, meaning a particular visual shape. Rendering is trivial. There is no need for a book that explains how a codepoint should be rendered, and there are no "joiner" or "non-joiner" characters in UTOPICS.

* UTOPICS pragmatically stores characters in visual order. Unicode follows the philosophy that characters should be stored in "logical order", even when that order is different from the visual order of the characters (though it makes an exception for Thai, for compatibility with earlier standards). This requires logic to reorder the corresponding glyphs for rendering.

* In UTOPICS, each character is explicitly left-to-right or right-to-left. This means that UTOPICS has, for example, two different question mark characters: One for use in Hebrew (right-to-left), and one for use in English (left-to-right). In Unicode, there is only one question mark character, and its directionality must be inferred from context according to complex rules. (This is part of the "one character, one codepoint" philosophy.) 
And if those rules aren't complex enough, Unicode provides TWELVE different control characters that can be used override the default inference. (If you want an insight into how complex Unicode can be, try looking at the [Unicode bidirectional algorithm](https://unicode.org/reports/tr9/) sometime.)

For programmers, Utopia offers some other benefits over Unicode. Changing the case of a Unicode character (uppercase or lowercase) requires a table lookup, but in UTOPICS, changing the case of a character requires only the flipping of bit, as in ASCII.

