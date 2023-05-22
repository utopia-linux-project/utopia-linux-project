The UTOPICS character set consists of 189 'blocks', numbered from 0 to 188. Each block contains 94 codepoints.

Block 0 contains the 94 printable characters from US-ASCII (ASCII 0x21 - 0x7E).

Block 1 contains general punctuation and symbols. Blocks 2-127 each contain the characters for a particular writing system.

Blocks 128-177 are reserved for future expansion.

Blocks 178-187 are a "Private Use Area" (PUA), where the codepoints are deliberately unassigned and unreserved. Outside of the PUA, all unassigned codepoints are reserved for future expansion.

Block 188 contains control characters and other special characters that are not part of any particular writing system.

# Roadmap
Block 0 - ASCII  
Block 1 - General Punctuation & Symbols  
Block 2 - European Latin  
Blocks 3 & 4 - Cyrillic  
Block 5 - Greek  
Block 6 - Armenian  
Block 7 - Georgian  
Blocks 8 & 9 - Arabic / Perso-Arabic  
Block 10 - Hebrew  
Block 11 - Syriac  
Block 12 - Thaana (Divehi)  
Block 13 - Bengali / Assamese  
Blocks 14 & 15 - Devanagari  
Block 16 - Gurmurkhi  
Block 17 - Gujarati  
Block 18 - Oriya  
Block 19 - Telegu  
Block 20 - Malayalam  
Block 21 - Kannada  
Block 22 - Tamil  
Block 23 - Sinhala  
Block 24 - Burmese  
Block 25 - Thai  
Block 26 - Lao  
Blocks 27 & 28 - Khmer  
Block 29 - Tibetan  
Block 30 - Mongolian  
Blocks 31 & 32 - Hangul Jamo  
Block 33 - Hirigana  
Block 34 - Katakana  
Blocks 35-108 - Hanzi / Kanji  
Block 109 - Bopomofo & Pinyin  
Block 110 - Vietnamese Latin  
Blocks 111 & 112 - African Latin  
Block 113 - Coptic  
Block 114 - Tifinagh  
Blocks 115-119 - Ethiopic (Ge'ez)  
Blocks 120-123 - Vai  
Blocks 124 & 125 - N'Ko  
Block 126 - Cherokee  
Block 127 - Canadian Aboriginal Syllabics  
Blocks 128-177 - Reserved for future expansion  
Blocks 178-187 - Private Use Area (PUA)  
Block 188 - Specials

THIS ROADMAP IS TENTATIVE AND PRONE TO CHANGE. IT WILL BE FINALIZED WHEN UTOPIA 1.0 IS RELEASED.

Each block will be documented separately on this wiki.

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
