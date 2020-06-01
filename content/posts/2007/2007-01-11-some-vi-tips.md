---
date: 2007-01-11
title: "Some vi tips"
---







categories:
- Linux
- RHCE


This is my personal vi reminder.
Feel free to share your tips !



Search and replace a string globally in a document :
`:.,$s/search_string/replacement_string/g`

Searching for a string :
Type "/" followed by the string
e.g. : /pattern

Disabling highlighted patterns :
Type ":nohl" to disable highlighting

Comment out several lines at once (I like that one) :
`1. Ctrl + V (visual block)
2. Select the lines you want to be commented out
3. Shift + I (insert mode)
4. Add the "#" character on the first line
5. Press escape`

Copy and paste several lines :
`1. Shift + V (visual line)
2. Select the lines
3. Press "Y" (x lines yanked)
4. Go where you want the block to be pasted and press "p"`

Cut and paste several lines :
`1. Let's say you want to cut 3 lines, put your cursor on the first of the 3 lines
2. Type : 3dd
3. Locate your cursor where you want the block to be pasted
4. Type "p"`

Some key combinations :
`dd : Delete current line
D : Delete from cursor to end of line
d$ : Delete from cursor to end of line
d0 : Delete from cursor to beginning of line
dw : Delete from cursor to end of current word
db : Delete from cursor to beginning of current word
yy : Copy current line (use "p" to paste)`

Here's a great cheatsheet from http://www.worldtimzone.com/res/vi.html
Some advices here may be redundant with the above tips

Cursor movement

    * h - move left
    * j - move down
    * k - move up
    * l - move right
    * w - jump by start of words (punctuation considered words)
    * W - jump by words (spaces separate words)
    * e - jump to end of words (punctuation considered words)
    * E - jump to end of words (no punctuation)
    * b - jump backward by words (punctuation considered words)
    * B - jump backward by words (no punctuation)
    * 0 - (zero) start of line
    * ^ - first non-blank character of line
    * $ - end of line
    * G - Go To command (prefix with number - 5G goes to line 5)

Note: Prefix a cursor movement command with a number to repeat it. For example, 4j moves down 4 lines.
Insert Mode - Inserting/Appending text

    * i - start insert mode at cursor
    * I - insert at the beginning of the line
    * a - append after the cursor
    * A - append at the end of the line
    * o - open (append) blank line below current line (no need to press return)
    * O - open blank line above current line
    * ea - append at end of word
    * Esc - exit insert mode

Editing

    * r - replace a single character (does not use insert mode)
    * J - join line below to the current one
    * cc - change (replace) an entire line
    * cw - change (replace) to the end of word
    * c$ - change (replace) to the end of line
    * s - delete character at cursor and subsitute text
    * S - delete line at cursor and substitute text (same as cc)
    * xp - transpose two letters (delete and paste, technically)
    * u - undo
    * . - repeat last command

Marking text (visual mode)

    * v - start visual mode, mark lines, then do command (such as y-yank)
    * V - start Linewise visual mode
    * o - move to other end of marked area
    * Ctrl+v - start visual block mode
    * O - move to Other corner of block
    * aw - mark a word
    * ab - a () block (with braces)
    * aB - a {} block (with brackets)
    * ib - inner () block
    * iB - inner {} block
    * Esc - exit visual mode

Visual commands

    * > - shift right
    * < - shift left
    * y - yank (copy) marked text
    * d - delete marked text
    * ~ - switch case

Cut and Paste

    * yy - yank (copy) a line
    * 2yy - yank 2 lines
    * yw - yank word
    * y$ - yank to end of line
    * p - put (paste) the clipboard after cursor
    * P - put (paste) before cursor
    * dd - delete (cut) a line
    * dw - delete (cut) the current word
    * x - delete (cut) current character

Exiting

    * :w - write (save) the file, but don't exit
    * :wq - write (save) and quit
    * :q - quit (fails if anything has changed)
    * :q! - quit and throw away changes

Search/Replace

    * /pattern - search for pattern
    * ?pattern - search backward for pattern
    * n - repeat search in same direction
    * N - repeat search in opposite direction
    * :%s/old/new/g - replace all old with new throughout file
    * :%s/old/new/gc - replace all old with new throughout file with confirmations

Working with multiple files

    * :e filename - Edit a file in a new buffer
    * :bnext (or :bn) - go to next buffer
    * :bprev (of :bp) - go to previous buffer
    * :bd - delete a buffer (close a file)
    * :sp filename - Open a file in a new buffer and split window
    * ctrl+ws - Split windows
    * ctrl+ww - switch between windows
    * ctrl+wq - Quit a window
    * ctrl+wv - Split windows vertically
