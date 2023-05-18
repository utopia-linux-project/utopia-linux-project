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

# What changes will need to be made to the Linux console?

* Support for console fonts with more than 512 characters.
* Support for displaying characters of different widths
* Support for overlaid characters (multiple characters appearing in one cell)
* Support for displaying bidirectional text
* Support for bidirectional cursor handling
* Support for a RTL (right-to-left) mode
* Support for contextual characters
* Support for Chinese and Japanese input methods

Note that Utopia requires the Linux framebuffer console.

# Why? 

You might wonder if there's any need for the Linux console to support non-Latin scripts. After all, the Linux console is used almost solely by developers, and the fact of our world is that developers need to be functionally literate in English. Users who are not developers would rather use a Linux distro that boots straight into a graphical environment.

To which, I have three responses.

1) Developers need to read documentation, and if documentation is available in their native language, they might benefit from being able to read that documentation on the Linux console.

2) Developers might like to write emails in their native language on the Linux console.

3) It's a matter of basic fairness. The Linux kernel should not favour one language over another.

# Is this doable?

Yes. I have put much thought into this over the past few years, and I am quite certain it is doable.

# Utopic Ideas

At the heart of Utopia is UTOPICS, the Utopia Terminal-Oriented Pragmatic International Character Set.

UTOPICS is NOT Unicode. It has different design goals from Unicode. However, UTOPICS is designed to be completely interoperable with Unicode, meaning that UTOPICS and UTF-8 can be mixed in a single data stream with no ambiguity. (No one wants to return to the days of multiple incompatible character encodings.) You can have UTOPICS files and UTF-8 files sitting side-by-side on your file system with no need to tag them.

UTOPICS is designed to be easy to render with console-style bitmap fonts, so that no one has to try to insert a Pango-style rendering engine into the Linux kernel.

See UTOPICS.md for more information.
