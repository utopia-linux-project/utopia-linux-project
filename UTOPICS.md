# Roadmap

The UTOPICS character set consists of 189 'blocks', numbered from 0 to 188. Each block contains 94 codepoints. Block 0 contains the 94 printable characters from US-ASCII (ASCII 0x21 - 0x7E). Block 188 contains control characters and other special characters.

Block 0 - ASCII  
Block 1 - General Punctuation  
Block 2 - European Latin  
Block 3 - European Latin Diacritics  
Block 4 - Cyrillic  
... TO BE COMPLETED ...  
Block 188 - Specials

# UTOPICS Codepoint Notation

The standard way to notate a codepoint in UTOPICS is as _X.Y_, where X is the block number, and Y is the position of the codepoint (starting at 0, up to a maximum of 93) within that block. X and Y are both decimal numbers, not hex. Y can be one or two digits.

So the tenth character in the Greek block is written as 5.9 (or 5.09), and the twelfth character in the Armenian block is 6.11.

This notation is for the convenience of humans, not machines. It's designed to be easy to type on a numeric keypad (for Alt-key entry), and to be distinct from the notation used for ASCII or Unicode codepoints.

# UTOPICS Binary Representation

When used in a binary data stream, a UTOPICS codepoint X.Y is encoded as follows.

* Codepoints in Block 0 are encoded as a single-byte, equal to 0x21 + Y. This gives a value identical to ASCII.
* Codepoints in blocks 1 to 94 are encoded as a sequence of three bytes. The first byte is 0xFE. The second byte is 0x9F + X. (X is from 1 to 94, giving a byte from 0xA0 to 0xFD.) The third byte is 0xA0 + Y. (Y is from 0 to 93, giving a byte from 0xA0 to 0xFD.)
* Codepoints in blocks 95 to 188 are encoded as a sequence of three bytes. The first byte is 0xFF. The second byte is 0x9F + X - 94. (X is from 95 to 188, giving a byte from 0xA0 to 0xFD.) The third byte is 0xA0 + Y. (Y is from 0 to 93, giving a byte from 0xA0 to 0xFD.)

This is the only normatively correct way to represent UTOPICS in binary. There are no 32-bit, 16-bit, big-endian, or little-endian versions.

If you think three bytes to encode one character is a lot of overhead, well, that's the price of interoperability with Unicode.

# The Utopia data stream

The UTOPICS character set is not designed to be used on its own. (For one thing, there's no space character in UTOPICS.) The UTOPICS character set is designed to appear within a Utopia Data Stream.

The follow kinds of data can appear in a Utopia Data Stream.

 * ASCII bytes, including:
   * C0 control characters (0x00 - 0x1F)
   * The SP (space) character (0x20)
   * The 94 printable ASCII characters (0x21 - 0x7E). (This is identical to Block 0 of UTOPICS.)
   * The DEL (delete) character (0x7F)

 * C1 control character bytes (0x80 - 0x9F)
   This is of note for the Linux console, which uses the CSI (0x9B) character.

 * Multi-byte Encoded UTOPICS characters (Start byte: 0xFE - 0xFF)
   These represent UTOPICS characters in Blocks 1 and above, as described above.

 * UTF-8 sequences (Start byte: 0xC0 - 0xFD)
   A Utopia application must be able to decode and process UTF-8 sequences in the data stream without corruption. However, a Utopia application is not required to display those characters. The application may choose to ignore Unicode characters, or may substitute a question mark or other character. Alternatively, the application may automatically convert Unicode characters to an equivalent (or approximately equivalent) UTOPICS character.

# Mixing UTOPICS and UTF-8

I will use the term "Pure Utopia" to describe data that consists only of UTOPICS sequences, ASCII characters, and C1 characters. I will use the term "Mixed Utopia" to describe data that also contains UTF-8 sequences.

Note that UTF-8 is not "C1 clean", meaning that UTF-8 sequences can include bytes in the 0x80-0x9F range, colliding with the C1 control characters. (This is not an issue introduced by UTOPICS. This is a pre-existing gotcha.) Hence, Mixed Utopia data is not C1 clean either.

However, with proper UTF-8 decoding, C1 control characters can be distinguished from UTF-8 sequences, because the start byte of a UTF-8 sequence will never be the same as a C1 control character.

Note that:
 * UTOPICS sequences always start with 0xFE or 0xFF.
 * The bytes 0xFE and 0xFF are not used in UTF-8, so there can be no confusion between UTOPICS sequences and UTF-8 sequences.
 * The bytes 0xFE and 0xFF never appear as the second or third byte of a UTOPICS sequence. So the start of a UTOPICS sequence can always be distinguished from the middle of a UTOPICS sequence.
 * When the bytes 0x00-0x7F appear in a Utopia data stream, they always represent the same value they would represent in ASCII.
 * In a Pure Utopia data stream, the bytes 0x80-0x9F always represent C1 control characters. In a Mixed Utopia data stream, they could represent C1 control characters or they could represent continuation (non-start) bytes in UTF-8 sequences, so proper UTF-8 decoding is required to disambiguate them.

# Why not Unicode?

Unicode requires a degree of complexity that is largely incompatible with the Unix design philosophy. It would require that language-specific processing for many languages be added to the Linux kernel, like repositioning the reph in Hindi, or contextual shaping of Arabic letters. Up until now, the Linux kernel has always been language-independent, and that's how it should remain. 

The Utopia Linux kernel will not contain any language-specific processing. It will, however, add broad support for right-to-left characters, larger character sets, and "overlay" (non-spacing) characters.

Here are some of the ways that Unicode differs from UTOPICS.

* Unicode follows the philosophy that each codepoint should correspond to a single letter, even if that letter takes different forms in different contexts. In contrast, each UTOPICS codepoint corresponds to a glyph, meaning a particular visual shape. Rendering is trivial. There is no need for a book that explains how a codepoint should be rendered. (There are no "joiner" or "non-joiner" characters in UTOPICS.)

* Unicode follows the philosophy that characters should be stored in "logical order", even when that order is different from the visual order of the characters (though it makes an exception for Thai, for compatibility with earlier standards). This requires logic to reorder the characters for rendering. UTOPICS, in contrast, pragmatically stores characters in visual order.

* Unicode attempts to infer from context whether a punctuation character should be treated as left-to-right or right-to-left, providing TWELVE different control characters that can override the default inference. (This is part of the "one character, one codepoint" philosophy.) In UTOPICS, each character is explicitly left-to-right or right-to-left. This means that UTOPICS has, for example, two different question mark characters: One for use in Hebrew (right-to-left), and one for use in English (left-to-right). (If you want an insight into how complex Unicode can be, try looking at the [Unicode bidirectional algorithm](https://unicode.org/reports/tr9/) sometime.)

For programmers, Utopia offers some other benefits over Unicode. Changing the case of a Unicode character (uppercase or lowercase) requires a table lookup, but in UTOPICS, changing the case of a character requires only the flipping of bit, as in ASCII. Also, UTOPICS does not have multiple "normalization" forms. (There is no normalization form C, D, KC, or KD.)
