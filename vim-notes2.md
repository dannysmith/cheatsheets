# Vim Notes

## The Dot

The dot character allows us to repeat previous actions. It will 'record' the previous "Change" and play it back. Changes include anything typed when in insert mode.

If we wanted to append a semicolon to the end of every line in a file, we might move to the end and then append the character: `$a;`. We could then repeat this by typing `j$.`, to move down a line, jump to the end and repeat the `a;` action.

There are a number of compound actions that combine normal movements. In this case, we could use `A` in place of `$a`. That means we can repeat the action more easily, with just `j.`. 

We can undo anything we did with the dot using `u`.

Here are some more compound actions, all of which enter INSERT mode:

Compound | Longhand | Meaning
---------|----------|--------
C | c$ | Delete from here to the end of the line
s | cl | Delete the current char and enter insert mode
S | ^C | Replace the whole line
I | ^i | Enter insert mode at the start of the line
A | $a | Append to the end of the line
o | A<CR> | Insert a newline underneath
O | ko | Insert a newline above

Note: Just like the dot repeats the last action, the semicolon repeats the last search on the current line using `f`. Just like `u` undoes the last dot action, pressing the comman will jump us back when using `;`.

Here are all the repeatable actions and their reverse.

Action | Repeat | Reverse | Meaning
-------|--------|---------|--------
{edit} | . | u | Make a change
f{c} or  t{c} | ; | , | Scan for next character
F{c} or T{c} | ; | , | Scan for previous character
/patt | n | N | Search forwards for a pattern
?patt | n | N | Search backwards for a pattern
:s/tgt/repl | & | u | Replace tgt with repl
qx{changes}q | @x | u | Execute a sequence of changes

There's a shortcut for searching: `*` searches for the word currently under the cursor. We then use `n` to cycle through subsequent matches, and `N` to jump back. (We could, for example, use `cw` to change the first instance of a word and then repeat this using `n` and the dot)

## Counting

Vim offers <C-a> and <C-x>, which increment and decrement the next number found on the current line. We can pass in a "Count" to these to have it jump in multiples. These counts can be passed into most other things too.

## Operators and Motions

Generally, an action is the combination of an operator with a motion.

Here some of the more common operators:

Trigger | Effect
--------|------
c | Change
d | Delete
y | Yank into Register
g~ | Swap Case
gu | Make Lowercase
gU | Make Uppercase
> | Shift Right
< | Shift Left
= | Autoindent
! | Filter {motion} lines through an external program

### A note on Insert Mode

When In insert mode, we can use <C-w> to delete a word and <C-u> to delete a line. This works in bash too.

We can switch from insert mode to normal mode with <Esc> or <C-[>. The latter is often faster. We can also temporarily switch to normal mode with <C-o>, which will then switch back after one command. This is useful for things like `zz`, which redraws the screen with the current line in the middle, or for jumping to the start and end of lines, or finding forward in lines.

We can also paste from a register using <C-r>{n} where n is the number of the register to pase from. This is 0 for text we have just yanked. We can use this with the `=` register to do on-the-fly calculations. We can also use <C-k> to insert special characters by digraph (ie. >> becomre »).

Finally, we can enter replace mode from NORMAL mode by pressing `R`. This behaves just like insert mode, but will overwrite characters.

## Visual Mode

Vim has three types of  visual mode: character-wise, line-wise and block-wise.

v | Enter character-wise
V | Enter line-wise
<C-v> | Enter block-wise
gv | reselect last visual selection

The first three all toggle visual mode on an off. Visual mode selections have a fixed end and a free end (that moves with the cursor). We can toggle the free end with `o`. This allows us to refine a selection from either end, usually using the `e`, `b` and `w` movements.

## Command Line Mode

We can apply certain commands to lines like this: `:2d`. This will delete line 2. We can also use ranges: `:1-4p` This will print the first four lines of the file. There are some special meanings:


1 | The first line
1,3 | A range from line 1 to line 3
$ | The last line
. | The current line
% | The whole file
0 | Virtual first line
'm| Line containing mark m
'<|Start of visual selection
'>|End of visual selection

Additionally, if we have a range selected in visual mode, the command prompt will be pre-populated with it. This is useful for find and replace within blocks, for instance.

This whole file range is very common when used with the substitute command: `:%s/This/WithThis`.

We can match against regexps too: `:/def something/,/end/p`. When matching a regexp, we can reduce the selection by adding offsets: `:/def something/+1,/end/-1p` in the form `+1` or `-1`.

We can copy a certain line to just below the current line: `:6t.`. This says, copy line 6 *to* the current line (`.`). This obviously works with ranges as well as individual lines. There is also `:m`, which moves a line. This is especially useful when combined with visual mode. We can first select an series of lines using `V`, then use `:t` or `:m` to move them to some other line number.

One of the most powerful commands is `:{range}normal`. This says "apply the normal command to every line in the range". For example, `:%normal i//` will prepend every line in the file (`%`) with two slashes (`i//`). To add a semicolon to the end of every line from 3 to 16, we could use `:3,16normal A;`.

When at the prompt, we can use `<C-r><C-w>` to get the word under the current cursor. This is particularly useful when searching for a long word, or looking up the help for a word.


# Files and Buffers


