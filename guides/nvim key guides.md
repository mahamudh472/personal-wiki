
## Exiting
q -> Close.
q! -> Close without saving.
qw -> Save and close.

## Moving the cursor.
j -> Down
k -> Up
h -> Left
l -> Right

## Text editing.
x -> Delete text
i -> Insert
a -> Append

## Deletion
dw -> Delete a word until the next word start.
d$ -> Delete to the end of the line.

## Operator and motions
d   motion
- d is the delete operator
- motion is what the operator will operate on.

motions:
w -> until the start of next word
e -> until the end of current word.
$ -> until the end of line.

## Count for motion
2w -> move the cursor two words forward
3e -> move the cursor to the end after the third word forward.
0 -> move to the start of the line.

## Count for delete
d   number   motion
d2w -> Delete two words forward
dd -> Delete line
2dd -> Delete two lines

## Undo - redo command
u -> Undo last commands
U -> Undo a whole line changes
<C-r> (Control + R) -> Redo


