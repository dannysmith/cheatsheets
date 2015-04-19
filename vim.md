# Vim and Tmux Stuff

## Basic Navigation

h,j,k and l are considered a bit of an antipattern, since there are faster ways to move about in vim. That said, you need to get used to them. To move a set number of lines or characters, prepend with a number.

These are all examples of motions, which are used to navigate. Every motion can be prefixed with a number.

## List of Motions

w Until the start of the word excluding its first character
b Same as above but backwards
e Until the start of the next word including the last character
ge Same as above but backwards
$ To the end of the line including the last charecter
^ To the start of the line
0 To start of line
l One character
<thing>i<to> Inside the text object. This could be `w`, `[` etc. See below.
h Left character
j Next line
k Previous line
l Right character

gg Move to top of file
G Move to bottom of file
200G Move to line 200

f<char> First instance of character on this line. We can jump to the next one with `;` and move back to the previous with `,`. This is an incredibly effective way to move about on a page. 

F Same as `f`, but searches to the left rather than the right.
t Same as `f`, but will select the character *before* the match.
T Same as `t`, but searches to the left.

## Editing

x Delete current character

i Insert before the current character
a Instert after the current character (most useful)

o Enter insert mode on a newline below the current
O Enter insert mode on a newline above the current

I insert at the beginning of a line
A Append to the end of a line

dd Delete a Whole Line (shortcut)
yy Copy a whole line

dw Delete from the cursor to the end of the word
d$ Delete from the cursor to the end of the line
d0 Delete from the cursor to the beginning of the line

## Operators and Motions

An operator explains what will be done. In the case of `ds`, the `d` is the delete operator. Th `s` is the motion, which says what it will operate on.

Typing a number before a motion repeats it a set number of times. This `d2w` will delete ne next two words and `2dd` will delete two lines.

Motions can be used to move the cursor. Thus `w` will always move to the first character of the next word, and `e` will move th the last character of the next word.

The format is `operator[number]motion`.

## Undoing

u Undo the last change.
U undo all changes to the line we're on

To undo these undo's, use CTRL-R

## The Put Command

p Put previously deleted text after the cursor

We can move lines by deleting them with `dd` and then using `p` to put them under the cursor. We can repeat this by providing a number. If we have deleted a line, `5p` will insert it five times under the cursor. This also works with inline text.

## The Replace and Change Operators

r replaces the current character with another
R replaces multiple characters (like using insert on Windows)
ce corrects the current word from the current character (and enters edit mode)
c$ corrects from the current character to the end of the line.

For change, the format is `c[number]motion`.

We can also indent and outdent lines using `<<` and `>>`.

## Searching and Jumping

/term Search forward for "term"
?term Search backwards for "term"
n Repeat the last search (moving forwards)
N Repeat the last search (moving backwards)

To jump back to where you were before, use CRTL-o. Used with CTRL-i, this allows is to jump about a document. Every time we switch paces in a file or to a new file, Vim adds an entry to the jumplist. These commands allow us to move about the jumplist.

## Matching brackets

When on a bracket, `%` will move to the corresponding bracket.

## Selecting Text

v Enter visual mode

This selects text using the normal motions. We can then appy operators to it, and even save it by using the `:w FILENAME` command.

We can copy (or yank) the text with `y`. We can paste it with `p`. We can use `y` as an operatore, so `3y` yanks three words, and `y$` yanks to the end of the line.

# Some Useful Tips

    Given some text with a comma, like this.

We can use `f,` to move to the comma, then `dt.` to delete everything up to the full stop. Simillarly, `yt.` would copy the text, `ct.` would change it and `vt.` would select it.

## Working with text objects using `i`.

If we want to edit the text inside some delimeters, like quotes of brackets, we can use `ciw` to change the text within a word and `ci"` to change the text within some quotes. This works with brackets too, so `vi[` will select all the text within the current square brackets.

Simillarly, we can use `a` in place of `i` to include the delimeters in the selection, so `da[` will delete everything inside the current square brackets *including the brackets*.

If we have the `vim-textobj-rubyblock` plugin installed, we can select the contents of a ruby block with `vir`, and delete a whole block with `dar`.

## Commands

:w - Save
:q - Quit
:q! - Quit without saving
:wq - Save and then quit
:!<command> - Execute a shell command
:s/old/new/g - Replace old with new in the current line
:%s/old/new/g - Replace old with new in the whole file
:s%/old/new/gc - Same as above, but include a confirmation.

:r filename - insert the contents of filename at the current cursor location. This also works with commands, so `:r !ls` inserts the directory listing.

:set <setting> - Set a setting
:set no<setting> - Unset a setting


## Dealing with Files

### Buffers

:e <filename> - Opens a file.
:ls - Show the currently open buffers and switch to them
:bp and :bn - Cycle through open buffers
:b1 - Switch to buffer 1.

### Splits

:sp <filename> - Open file in new vertical split
:vs <filename> - Open file in new horozontal split

## Repeating commands
We can use `.`. to repeat the last phrase used in vim, so if we've used `ciw` to
change a word to something new, `.` will make the same change to the current
word.



# Plugins

### Vundle
Invoke with `:PluginInstall`.

### CtrlP and NERDTree
Fuzzy find for any file in the current project. Use with `Crtl + p` Key Combo. NerdTree can be toggled with `Ctrl + n` combo.

### Vim Ruby

Adds indentation and syntax highlighting, but allso adds comppletions.

    "This is a string".ca<CTRL-X><CTRL-O>

Will show a list of string methods starting with "ca".

`]m` moves to the start of the next method definition, while `]M` moves to the end of it. `[m` and `[M` Moves to the previous one.

### Vim Endwise

Adds end statements after if's and the like.


### Ruby Refactoring

See here: https://justinram.wordpress.com/2010/12/30/vim-ruby-refactoring-series/





