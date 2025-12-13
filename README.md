# tonality
My various songs I've transcribed to be played in Tonality Composer


Tonality Composer is a simple chiptune writing library that someone I know
has written a basic browser app for. I'm not sure if s/he's willing to
release it publicly, so you'll have to take my word for it

## Tonality Composer Basic Syntax

### Comments

Anything within curly braces is a comment, and not parsed

### Basic Rhythm and Melody

Tones are specified using scale letters. A is tuned to 440hz, and Just Intonation
is used to generate other scale tones relative to Concert A

A melody can be specified with scale letters as follows:

`A B C D`

By default, every note is the same length as the note prior to it, and the smallest interval is chosen.
In the case of the above example, this is a rising riff, because the B above an A is closer than the one below it

To specify a different octative than would be defaulted, use `,` to lower it and `'` to raise it

`A B C, D` will play the C _below_ B, instead of above


Notes can have a duration specified, it works just the same as sheet music. A number concatenated to a letter
name specifies it's length (`1` for whole note, `2` for half note, `4` for quarter note, etc). Additionally, `.` dots work
as they do in sheet music, extending a note's duration by 1/2 its length (eg `A2.` would be the same length as `A2 A4`)

You can specify the time signature with the following command, that sets the specified note to the specified
BPM:

`T(4=120)`

This will cause quarter notes to be played at 120 bpm

An `R` denotes a rest, and otherwise functions the same as a note

### Accidentals

Accidentals work as they do in sheet music. Concatenating a `#` makes a note sharp, and concatenating a `b` makes it flat.

You can specify a key signature as so. Doing so will cause all of the specified notes to be played, by default,
sharp or flat as specified.

`K(F#C#)`

Should you need a natural note while a key signature is specified, you can use `@` the way you would use a natural
sign in sheet music.

Like all other things in Tonality Composer, everything is relative to what came before it. If you use an accidental,
it will stay in effect until you specify otherwise. For example, `F# F F F F@ F` plays four sharp Fs and
then two natural ones. 

Accidentals can be undone with the bar line marker, `|`. This is the only syntactic use for a bar line marker,
and it is otherwise not necessary to use them, although it can help with organization in complex pieces

### The Empty Note

Because everything is relative to what came before it in the syntax of Tonality Composer, sometimes you
can find yourself having trouble keeping track of what the current context is, and what any given
pattern of notes might play. In order to reset the pitch and rhythm from its current context back to
default, you can include the empty note `O`. This note has no sound or duration, it simply resets the context

The empty note can be rhymically or melodically modified, the same as any other note, to set a new
default context that the following notes are relative to. For example, `O,,` would make the notes that follow be
a deep bassline, and `O16` would make the notes that follow default to sixteenth notes

### Multiple Voices

To play multiple tones simultaneously, a polyphony block is used, with the syntax `[ / ]`.
The `[ ]` square brackets denote the presence of parallel voices, and the `/` separates each.
This can be arbitrarily nested, which is useful for things like pianos, guitars, and any other instrument
that can play chords. For example, consider a piano piece where the right hand plays a melody, and the left
hand plays backing chords. You might notate it as follows:

```
[ { Right Hand }

A B C D 

/ { Left Hand }

  [
    A1
  /
    C1
  /
    E1
  ]

]
```

This example has the right hand playing a rising scale, while the left hand plays whatever chord ACE is
(Despite having a ton of musical experience, I have next to zero knowledge of music theory, and couldn't
tell you what a chord is from its notes)

Caution! Currently, Tonality Composer does very little in terms of syntax or semantic validation.
When parallel voices are introduced, Tonalty Composer will *not* synchronize the lines. It's on the programmer
to make sure that the rhythms in parallel voices are the same length of time. If they are not,
then after the shorter parallel line is complete, the song will continue to the next line while
the longer parallel voice is still playing. This is almost certainly not what you want if it happens


## Tips and Tricks

### Scoring

There are multiple ways to score the same voices playing the same rhythms and tones, and Tonality Composer
does not mandate a 'correct' way to do it. In my experience, I have found that trying to match
the organizational layout of standard sheet music is usually preferable. So, if you were trying to transcribe
a concert band piece, you might create a top-level polyphony block and have one section for each voice:

```
{ Some Concert Band Piece}

[ { Flutes }

/ { Clarinets }

/ { Trumpets }

/ { Tromboners }

/ { Euphonia }

/ { Oboes (the mortal enemy of us Flautists) }

/ { etc }

]

```

I have found it useful, for pieces with many voices, to separate the piece into sections
for organizational purposes. If one uses one giantic top-level polyphony block, it can be difficult
to ensure that all of the voices stay synchronized and no typos are made. I have found that
in such songs, it makes sense to have separate top-level polyphony blocks for each section
of the song. A good example is the first song I attempted to transcribe for Tonality Composer,
[Unite Synchronize](https://github.com/eqdw/tonality/blob/master/songs/unite_synchronize.ton),
which as of the time of writing this README, is in-progress with top-level polyphony
blocks defined for each of the following sections:

{ Unite } // The first half of the song
{ Begin } // The intro
{ Verse } // The beginning of the main part of the song
{ Section C } // The section labelled "C" in the sheet music I found
{ Section D } // The section labelled "D" in the sheet music I found

----

However, if you were transcribing a piece written for a single guitar, you may instead elect to
write the song as a single voice melody, and use polyphony blocks for each chord individually:

```
{ Radical Dreamers: Unstealable Jewel (Fourth Bar) }
T(4=75)
K(F#C#)

E,,16 E' A B E B A E [E,2 / G#2 / B2 ]


### Rhythms

Because Tonality Composer does not validate syntax or sematics before playing, it is
on the programmer to ensure that everything is correct. One useful technique to help with 
this is to structure your code similarly to sheet music, keeping it neatly organized into bars,
lines, and sections. It is helpful to make liberal use of the fact that whitespace is meaningless in
Tonality Composer.

I have found it effective to write my code such that each line is either two or four bars of the sheet
music, depending on what makes sense in context. Further, I have found that it is useful to vertically
justify the notes, to make it clear when a given rhythm repeats on a voice. For example:

```
{ Unite Synchronization (First Eight Bars, Right Hand }
{ https://homestuck.bandcamp.com/track/unite-synchronization }

T(4=138)
K(AbBbEb)

{ Unite }

{RH}

O G'8 C  D  E  G, C  D  E  G, C  D  E  A, C  D  E
  E,  B' D  E  E, B' D  E  E, B' D  E  A, C  D  E
O G'8 C  D  E  G, C  D  E  G, C  D  E  A, C  D  E
  E,  B' D  E  E, B' D  E  E, B' D  E  A, C  D  E

```

Each line is two bars, and it is clear that the rhythm repeats. Any typos or mistakes would be obvious.

This technique can only take you so far, though. In general, depending on how you score the piece,
you will have to choose between lining up a given voice with itself, to emphasize rhythmic self-similarity,
or line up several voices together, to emphasize that they're playing harmoniously. And should the rhythms
not line up note-for-note, lining up lines together vertically becomes challenging. Use your judgement;
there is no right way, the important part is that it's easily legible to you, and those who come later. 

### Explicit notation

Tonality Composer's design decision to make all notation relative to the previous note, instead of absolute,
is a useful feature that optimizes for many common musical use cases, it can frequently make things hard
to follow and reason about in rhymically or melodically complex pieces. I have found it very useful
to write much of the code more explicitly than is necessary. First, I find it useful to explicitly specify
note durations on complex rhythms even when it's unnecessary. A section from Unite Synchronize again

```
O C8. C8. C16 C16 C1 A8. A8. A16 A16 
  E8. E8. E16 E16 E1 A8. A8. A16 A16
O C8. C8. C16 C16 C1 A8. A8. A16 A16 
  E8. E8. E16 E16 E1 A8. A8. A16 A16  
O C8. C8. C16 C16 C1 A8. A8. A16 A16 
  E8. E8. E16 E16 E1 A8. A8. A16 A16  
O C8. C8. C16 C16 C1 A8. A8. A16 A16 
  E8. E8. E16 E16 E1 A8. A8. A16 A16  
```

Strictly speaking, Tonality Composer would allow us to write this more compactly as

```
O C8. C. C16 C C1 A8. A. A16 A 
  E8. E. E16 E E1 A8. A. A16 A
O C8. C. C16 C C1 A8. A. A16 A 
  E8. E. E16 E E1 A8. A. A16 A  
O C8. C. C16 C C1 A8. A. A16 A 
  E8. E. E16 E E1 A8. A. A16 A  
O C8. C. C16 C C1 A8. A. A16 A 
  E8. E. E16 E E1 A8. A. A16 A  
```

However, I find it much harder to reason about how the music will play
when written in this form.

Additionally, notice the `O`s prepending every other line. This is the empty note,
resetting the relative pitches of the music. In the case of this specific line of music,
if we left the `O`s off, it would play the same. However, if the line was slightly different, 
we might unexpectedly jump to the lower C on line 3 instead of the higher one we want.
If that was the case, then line three would have to start with `C'8.` to force picking the higher one.

Given that the semantics of the music in this section is for the same line to repeat four times
in a row, I would find it confusing if the third line was written visually differently from the first
one. Thus, I make frequent use of `O`, typically every two or four bars, to reset the relative pitch.
Perhaps as I start transcribing more pieces, I'll find a better technique. 
