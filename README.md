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

# Is this doable?

Yes. I have put much thought into this over the past few years, and I am quite certain it is doable.

# Utopic Ideas

At the heart of Utopia is UTOPICS, the Utopia Terminal-Oriented Pragmatic International Character Set.

UTOPICS is NOT Unicode. It has different design goals from Unicode. However, UTOPICS is designed to be completely interoperable with Unicode, meaning that UTOPICS and UTF-8 can be mixed in a single data stream with no ambiguity. (No one wants to return to the days of multiple incompatible character encodings.) You can have UTOPICS files and UTF-8 files sitting side-by-side on your file system with no need to tag them.

UTOPICS is designed to be easy to render with console-style bitmap fonts, so that no one has to try to insert a Pango-style rendering engine into the Linux kernel.

See UTOPICS.md for more information.

# How will keyboard input work?

As you probably know, some languages like Chinese and Japanese require "input methods". The Utopia project will include a shell program that executes commands inside a pseudo-terminal that will process keyboard input and provide input method functionality. Because the shell program executes in user space, language-specific processing can be kept out of the kernel.

This shell will also be required for languages like Arabic and Hindi, as the input layer will contain the logic for contextual shaping and reordering.

# But the translation files for my software are encoded in Unicode. I need to display Unicode text!

No problem! The aforementioned shell program will also be able to automatically convert Unicode output into (approximately) equivalent UTOPICS code for display on the console.

# Requirements

Utopia uses (and requires) the Linux framebuffer console.
