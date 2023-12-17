# boar-extras

Some extra programs to make using [boar](https://github.com/jimd1989/boar) and [boar-midi](https://github.com/jimd1989/boar-midi) easier.

## Build

    cc semicolons.c -o semicolons
    mv semicolons /usr/local/bin
    chmod +x delay.sh
    mv delay.sh /usr/local/bin/delay
    chicken-install srfi-1
    chicken-csc boar-composer.scm
    mv boar-composer /usr/local/bin

## semicolons

Input to `boar` programs is newline delimited, which can make specifying chords or multiple options a hassle. The `semicolons` command turns semicolons into newlines, so that a chord like

    n60;n65;n69

is read as

    n60
    n65
    n69

This functionality is effectively the same as `tr`, but I've found that its buffering behavior can gum up pipes. `semicolons` will flush after every command.

## delay

I am not sure if it's my old synthesizer, my old computer, or both, but rapid MIDI output is ignored unless I delay it slightly. Running `delay n` in a pipe will hold off on echoing its input for n seconds. n can be fractional. Using an imperceptibly small value of n seems to do the trick.

## boar-composer

Takes a S-expression of form:

    ((steps n) 
     (notes (list (chord ...) ...)))

and generates a looped score of boar note on/off events suitable for use with [pop](https://github.com/jimd1989/pop).

Syntax available in `notes`:

- Individual MIDI notes `C0 .. Bb10`. Sharps are not available due to the significance of `#` in Scheme. Just use flats.
- `mj` and `mn` chord functions for use against a single note. Also `mj7` and `mn7`. Create more in the source code using these as templates.
- An `arp` function to break a chord into individual note events. You most likely want to flatten it with `,@`.
- Rests can be represented with `∅` or the normal empty list `'()`.

For example:

    echo '((steps 4) (notes (list (mj C5) (mn Db5) (mj F5) (mj G5))))' | boar-composer

Recommended for use with Vim. Select parts of your score and pipe them into this program.    
    
## Example

Opening a repl that speaks to a synth on MIDI channel one, then playing a chord on it.

    mkfifo synth
    boar-midi 1 <> synth
    rlwrap cat | semicolons | delay 0.001 > synth
    n60;n65;n69
    o60;o65;o69
