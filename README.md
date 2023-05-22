Utopia is a project to add support for many of the world's writing systems to the Linux console.

# Language Support

Utopia 1.0 will support the following writing systems.

European Scripts
 * Latin
 * Cyrillic
 * Greek
 * Armenian
 * Georgian

Middle Eastern Scripts
 * Arabic / Perso-Arabic
 * Hebrew
 * Syriac

South Asian Scripts
 * Bengali / Assamese
 * Devanagari
 * Gurmurkhi
 * Gujarati
 * Oriya
 * Telegu
 * Malayalam
 * Kannada
 * Tamil
 * Sinhala
 * Divehi

South-East Asian Scripts
 * Burmese
 * Thai
 * Lao
 * Khmer
 * Vietnamese

East Asian Scripts
 * Chinese (Hanzi, Bopomofo, Pinyin)
 * Japanese (Kanji, Hirigana, Katakana)
 * Korean (Hanja, Hangeul)
 * Tibetan
 * Mongolian

African Scripts
 * Coptic
 * Tifinagh
 * Ge'ez (Ethiopic)
 * Vai
 * N'Ko

American Scripts
 * Cherokee
 * Canadian Aboriginal Syllabics

# Why? 

You might wonder if there's any need for the Linux console to support non-Latin scripts. After all, the Linux console is used almost solely by developers, and the fact of our world is that developers need to be functionally literate in English. Users who are not developers would rather use a Linux distro that boots straight into a graphical environment.

To which, I have four responses.

1) Developers need to read documentation, and if documentation is available in their native language, they might benefit from being able to read that documentation on the Linux console.

2) Developers might like to write emails in their native language on the Linux console.

3) Some embedded systems might lack a graphical environment.

4) It's a matter of basic fairness. The Linux kernel should not favour one language over another.

# Is this doable?

Yes. I have put much thought into this over the past few years, and I am quite certain it is doable.

# Utopic Ideas

At the heart of Utopia is UTOPICS, the Utopia Terminal-Oriented Pragmatic International Character Set.

UTOPICS is NOT Unicode. It has different design goals from Unicode. However, UTOPICS is designed to be completely interoperable with Unicode, meaning that UTOPICS and UTF-8 can be mixed in a single data stream with no ambiguity. (No one wants to return to the days of multiple incompatible character encodings.) You can have UTOPICS files and UTF-8 files sitting side-by-side on your file system with no need to tag them.

UTOPICS is designed to be easy to render with console-style bitmap fonts, so that no one has to try to insert a Pango-style rendering engine into the Linux kernel.

See UTOPICS.md for more information.

# Why not Unicode? If you added support for Unicode to the Linux kernel, Linus Torvalds would merge the patch right now!

Unicode requires a degree of complexity that is largely incompatible with the Unix design philosophy. It would require that language-specific processing for many languages be added to the Linux kernel, like repositioning the reph in Hindi, or contextual shaping of Arabic letters. Up until now, the Linux kernel has always been language-independent, and that's how it should remain. 

If you want an insight into how complex Unicode can be, try looking at the [Unicode bidirectional algorithm](https://unicode.org/reports/tr9/) sometime. Or try reading about the various kinds of Zero-Width Joiner and Zero-Width Non-Joiner characters which are needed to render Hindi.

Here are some of the ways that Unicode differs from UTOPICS.

* Unicode follows the philosophy that each codepoint should correspond to a single letter, even if that letter takes different forms in different contexts. In contrast, each UTOPICS codepoint corresponds to a glyph, meaning a particular visual shape. Rendering is trivial. There is no need for a book that explains how a codepoint should be rendered. (This also makes UTOPICS intuitive for users. The fact that Unicode Persian keyboards require a Zero-Width Non-Joiner key is a bit of a travesty, if you ask me. Khmer-speakers and Mongolian-speakers are other examples of people who have found Unicode unintuitive to deal with.)

* Unicode follows the philosophy that characters should be stored in "logical order", even when that order is different from the visual order of the characters (though it makes an exception for Thai, for compatibility with earlier standards). This requires logic to reorder the characters for rendering. UTOPICS, in contrast, pragmatically stores characters in visual order.

* Unicode attempts to infer from context whether a punctuation character should be treated as left-to-right or right-to-left, providing TWELVE different control characters that can override the default inference. (This is part of the "one character, one codepoint" philosophy.) In UTOPICS, each character is explicitly left-to-right or right-to-left. This means that UTOPICS has, for example, two different question mark characters: One for use in Hebrew (right-to-left), and one for use in English (left-to-right).

The Utopia Linux kernel will not contain any language-specific processing. It will, however, add broad support for right-to-left characters, larger character sets, and "overlay" (non-spacing) characters.

For programmers, Utopia offers some other benefits over Unicode. Changing the case of a Unicode character (uppercase or lowercase) requires a table lookup, but in UTOPICS, changing the case of a character requires only the flipping of bit, as in ASCII. Also, UTOPICS does not have multiple "normalization" forms. (There is no normalization form C, D, KC, or KD.)

# How will keyboard input work?

As you probably know, some languages like Chinese and Japanese require "input methods". The Utopia project will include a shell program that executes commands inside a pseudo-terminal that will process keyboard input and provide input method functionality. Because the shell program executes in user space, language-specific processing can be kept out of the kernel.

This shell will also be required for languages like Arabic and Hindi, as the input layer will contain the logic for contextual shaping and reordering.

# But the translation files for my software are encoded in Unicode. I need to display Unicode text!

No problem! The aforementioned shell program will also be able to automatically convert Unicode output into (approximately) equivalent UTOPICS code for display on the console.

# Requirements

Utopia uses (and requires) the Linux framebuffer console.
