# boar-extras

Some extra programs to make using [boar](https://github.com/jimd1989/boar) and [boar-midi](https://github.com/jimd1989/boar-midi) easier.

## Build

    cc semicolons.c -o semicolons
    mv semicolons /usr/local/bin
    chmod +x delay.sh
    mv delay.sh /usr/local/bin/delay

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

## Example

Opening a repl that speaks to a synth on MIDI channel one, then playing a chord on it.

    mkfifo synth
    boar-midi 1 <> synth
    rlwrap cat | semicolons | delay 0.001 > synth
    n60;n65;n69
    o60;o65;o69
