log

this is string
new line


                  VIM REFERENCE MANUAL    by Bram Moolenaar et al.



qweqweqwe

Multi-byte support                              multibyte multi-byte
                                                Chinese Japanese Korean
This is about editing text in languages which have many characters that can
not be represented using one byte (one octet).  Examples are Chinese, Japanese
and Korean.  Unicode is also covered here.

For an introduction to the most common features, see usr_45.txt in the user
manual.
For changing the language of messages and menus see mlang.txt.

1.  Getting started                     mbyte-first
2.  Locale                              mbyte-locale
3.  Encoding                            mbyte-encoding
4.  Using a terminal                    mbyte-terminal
5.  Fonts on X11                        mbyte-fonts-X11
6.  Fonts on MS-Windows                 mbyte-fonts-MSwin
7.  Input on X11                        mbyte-XIM
8.  Input on MS-Windows                 mbyte-IME
9.  Input with a keymap                 mbyte-keymap
10. Input with imactivatefunc()         mbyte-func
11. Using UTF-8                         mbyte-utf8
12. Overview of options                 mbyte-options

NOTE: This file contains UTF-8 characters.  These may show up as strange
characters or boxes when using another encoding.

==============================================================================
1. Getting started                                      mbyte-first

This is a summary of the multibyte features in Vim.  If you are lucky it works
as described and you can start using Vim without much trouble.  If something
doesn't work you will have to read the rest.  Don't be surprised if it takes
quite a bit of work and experimenting to make Vim use all the multi-byte
features.  Unfortunately, every system has its own way to deal with multibyte
languages and it is quite complicated.


LOCALE

First of all, you must make sure your current locale is set correctly.  If
your system has been installed to use the language, i
- eqwe
- qw rewrwe

- k


Linux kernel
============

There are several guides for kernel developers and users. These guides can
be rendered in a number of formats, like HTML and PDF. Please read
Documentation/admin-guide/README.rst first.

In order to build the documentation, use ``make htmldocs`` or
``make pdfdocs``.  The formatted documentation can also be read online at:

    https://www.kernel.org/doc/html/latest/

There are various text files in the Documentation/ subdirectory,
several of them using the Restructured Text markup notation.

Please read the Documentation/process/changes.rst file, as it contains the
requirements for building and running the kernel, and information about
the problems which may result by upgrading your kernel.
© 2020 GitHub, Inc.
