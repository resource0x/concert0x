## About

Doodle script is the notation for music programming designed as a learning tool for experimenting with melodic/rhythmic patterns. Implemented as part of midi-chat web application (standalone mode,
available at doodle.concert0x.com).

The idea is:

- you are given the outline of the song as an array of anchor points `[ T0, T1, T2, ...]` indexed by bar number. Each Tn can be considered as a 'point' in a 'musical space'. Such "point" is similar to the geometric point, with the subtle difference: in musical space, the 'point' is characterized by the absolute pitch AND the scale, so each Tn in fact encodes a pair (pitch, scale). The scale attribute in some sense defines the geometry of the 'space' at a given point. In effect, the array encodes a chord progression, with essential points (anchors) selected in each bar along the progression.

- your task is to fill the gaps (interpolate) between the anchors. Interpolation sequence is called a `motif`. The motif played in bar N is a melodic pattern that has to **approach** the anchor in bar N+1. You may define several motifs - say, m1, m2 and m3, and then use motif m1 in bars 1 and 4, motif m2 - in bars 2 and 3, etc. Important: notice that the motif is played in bar N, but its goal is to arrive at the anchor note of bar N+1 (the latter can be considered a target note for the bar N).

- there's a notation that allows you to write motifs in a position- and scale- independent manner, so you can apply the same motif in a wide range of contexts. For each motif, you need to sele

(\*) *normally*, Tn is a chord note in Sn. And *normally*, it's first note in bar n, except for the special case of delayed resolution (to be discussed later). 

## Motivation

The intent of this notation is to allow position- and scale- independent encoding of melodic patterns. 
E.g. we can take the pattern from the first bar of the Rondo Alla Turka: `b4 a4 g#4 a4 c5` and interpret it as follows: let T1=c5, S0=S1=A minor. We can generalize the pattern in the following manner:
`T/-1k/+1s T/-1k T/-1k/-1c T/-1k T`. Here, the first note is written as a path expression `T/-1k/+1s`, which means: from target note T, go 1 chord note down (`k` stands for 'chord note'), then go 1 scale step up (`s` stands for 'scale note'). For the second note, we have
`T/-1k`, which means: from note T, go 1 chord note down - and so on. If we denote the last note as dot (.), then we can simplify the expression as `T/-1k/+1s ./-1s ./-1c ./+1c T`. Further, let's agree to omit the dot in the beginning of the expression - then we can shorten the encoding again:  `T/-1k/+1s -1s -1c +1c T`.  This is our final form.   

Thus, we generalized the pattern by encoding it in a position- and scale-independent manner, so it can be applied to different notes of the same or different scale. This generalization is made possible by the introduction of 'relative steps' of 3 different kinds: k-step (chord-note step), s-step (scalewise step) and c-step (chromatic step). In general, the pattern can be thought of as 1-parameter function with parameter T (the anchor defined for next bar). If we apply same pattern to the note T=a4 over F major, we get a sequence `g4 f4 e4 f4 a4`.

Note: generalized pattern sometimes needs to know more about the context than just the target note and its scale. See details below.


## What is scale

The scale can be thought of as the configuration of 3 kinds of notes: chord notes (k-notes), scale notes (s-notes) and chromatic notes (c-notes).

Important: every k-note is also s-note and c-note; every s-note is also a c-note; every note is c-note. This convention of inclusion is necessary for our notation to work.

Example: C Major scale (k-notes are green, s-notes - blue)

![C Major scale](https://dl.dropboxusercontent.com/s/gzvcbcvxq6pucik/C-major.jpg?dl=0)

F# minor scale:

![F# Minor scale](https://dl.dropboxusercontent.com/s/3fjxfggfew3its6/F%23-minor.jpg?dl=0)

The scale changes in every bar (according to the chord progression we chose for the tune), so in each bar we have a different configuration. Notes 'work' like a group of musicians, which change their roles during the performance of the song: some come into the front stage, others recede into the background etc. K-notes are similar to soloists, they are featured prominently in the time span of the bar; s-notes serve as a glue for k-notes; c-notes are also used as a glue in some contexts. (This only can be demonstrated on examples)

The reason why this glue works is this: each scale induces a kind of vector field (or: **landscape**) of tensions and resolutions, with the forces of attraction acting between some notes. Accurate verbal description of this 'field' is difficult, because it all depends on tempo, rhythm, style and general context.  

The scales having the same intervalic structure, but starting on different notes, constitute the class of equivalence called "scale category". Example of scale category: major scale, having the following structure: `x +2c +2c +1c +2c +2c +2c +1c`, where starting point x is a parameter. For every x, we can build scale instance xM having x as a starting note (root), e.g. C#M is a major scale starting on note C#.

In doodle script, we have all commonly used scales (pre)defined in terms of note properties rather than intervals. E.g. Major scale is defined as an array of 12 note types: `k c s c k s c k c s c s`.
We can create an instance: `MajorScale.getInstance("c")`, and redirect all requests for step calculations to this instance; runtime is doing it behind the scenes while processing the notation. 

As a degenerate case of scale, we have chromatic scale, defined as "s s s s s s s s s s s" (that is, "every note is a scale note, with no chord notes").

NOTE: the notion of scale, as defined above, is intentionally simplified. The way how scales are used in jazz is not that straighforward, but we need to start with a simple concept.

## Hello, World

```
pragma title: hello_world
pragma bpm: 120                             # tempo in beats per minute, beat = 24 ticks
outline: f5/~FM g5/~GM c6/~C7 f5/~FM        # outline: 4 notes
motif m: T/-12c +4s ^T                      # motif - approaching T via sequence of 3 notes
rhythm s: 24 24 24                          # note styles for motif (each duration=24 ticks)
voice: m*s m*s m*s m*s                      # what motif to play for n-th anchor, with what rhythm
```   

Executes as follows: 
- parse "outline"  (results in an array of 4 anchor points)
- parse "voice"  (results in array of 4 pairs (motif, rhythm) to be rendered for the corresponding anchor)
- iterate over `voice`: let n-th element of `voice` be m\*s; render motif m paired with rhythm pattern s. 

NOTES: 

- rhythm factor `*s` in voice pattern can be omitted; if so, the motif is rendered with the styles encoded in the motif itself, e.g

```
outline: f5/~FM g5/~GM c6/~C7 f5/~FM 
motif m: *=24 T/-12c +4s ^T  # default note duration = 24 ticks
voice v1: m m m m            # no style override, still uses the style from the motif
```
- Symbol tilda (^) marks the note that has to be synchronized with the onset of the next bar. In other words, it allows you to position your motif on time axis relative to the bar boundary.

You can define **more than one voice** (e.g. adding a separate voice for bass line). The idea behind voice syntax is this: you can define several motifs and several rhythmic patterns, and mix and match them while defining the voice (assuming both patterns have the same number of notes) 

## Motif

The motif can be written as a sequence of note path expressions separated by one or more spaces. Each note path expression evaluates to a note, which is added to the sequence. Syntactically, note path expression is a sequence of path elements separated by slash ("/"). Examples of path expressions: `T/-1c`, `+1k/-1s`. Notation also supports assigment of the form `name=pathExpression`, e.g. we can write `x=c5/~CM/+1k x/+1k`. Assignment does not add note to the sequence, just memorizes it for future reference. The last example produces a single note g5.

Though the goal of the motif is to approach the anchor note of the next bar as a target, the motif does not necessarily ends on this note - it can continue, thus overlapping in time with the next bar. In general, motif can be thought of as having 3 parts: approach notes; target note; exit notes. Which note of the motif is played on the onset of the next bar is controlled by a tilda symbol.

Very often, exit notes of the motif can be perceived as the beginning of approach sequence for the next target. Some people believe this is the essense of our intuition of the melody.

## Path elements

Formal definition is cryptic, illustrating by examples instead

Constants:

- c#5 - absolute pitch  (note c# in octave 5). Only sharps are supported (no flats). Valid names are: `c, c#, d, d#, e, f, f#, g, g#, a, a#, b`
- ~CM - set scale association of current note, e.g. c4/~CM means: note c4 in scale C Major.  Scale attribute is necessary for scale arithmetic, e.g. "c4/~CM/+1k" evaluates to e4, "c4/~Cm/+1k" evaluates to d#4. The note is always associated with some scale. If not explicitly set, most recent scale is used. The following scales are predefined:

- xM
- xm
- x7
... etc (TODO)

Steps:

- +5c - chromatic step(s) (move by 5 semitones up from current position)
- -2s - diatonic (scale) step(s) (move by 2 diatonic steps down from current position)
- +2k - chord note step(s) (move to 2nd chord note starting from current position).
- root - move to the closes scale root, e.g  f4/~CM/root evaluates as c4.
- o7 - move note to given octave (`c#/07` evaluates as c#7)

References:

- . - current note, e.g `a3 . .` is equivalent to `a3 a3 a3` 
- *name* - reference to an earlier assigned variable (identifier) e.g xx/-1s - one scale step down from xx. Note that the scale changes at that point to the one associated with xx.
- @name - use only pitch from named variable (see explanation below)
- ~name - use scale from named variable (see explanation below)

Conditionals:

- is.k, is.s - note type assertion: is.k - assert that current position is k-note, is.s - assert scale note. If assertion fails, the whole motif is rejected (except when there's no alternatives)
- isnot.k, isnot.s - negative type assertion: e.g. isnot.k - current note is not k-note
- is.s?+1c, isnot.k?+1s - conditional step: e.g. is.s?+1c - if current note is s-note then execute chromatic step. Both "is" and "isnot" conditions are supported.

Variables

- xx=c#4/~CM/+1k -creating a named variable so it can be later used in expressions like xx/+1k
- .=T/-1s - assign reference note, e.g. `.=a#5 +1c` evaluates as `b5`
- ~=FM - assign scale for a reference note (current scale)
- ~xx=scale - assign a different scale for earlier defined note `xx`, e.g. ~T=FM - results in note T being associated with scale FM
- *=style - sets the default style for the following motif elements

Predefined variables:

- T - anchor note to be used as target in the current motif
- S - previous anchor

When the variable is used as an element of path expression, the scale associated with it becomes "current scale", so all further calculations of steps are performed relative to this scale.
Special expressions `~x` and `@x` are used to refer to the scale or the pitch of x individually. Example: in the expression
`x=d4/~Dm c4/CM x/+1k` the last note evaluates as f4, because the scale is taken from x. Compare with `x=d4/~Dm c4/CM @x/+1k`. Now the last note is e4, because only the pitch is taken from note x.


## Note Style

Note style incorporates information about note duration, velocity etc, e.g. t24v6 means "note duration is 24 ticks, velocity=6 (gets multiplied by 16 while converting to MIDI velocity value).
The following attributes are supported:

- slot duration, e.g t36 (or simply 36, 't' prefix is optional)
- effective duration, e.g. e48 (48 ticks)
- velocity level, e.g. v7 (MIDI velocity=7*16)
- time shift, e.g. +5 (will be shifted by 5 ticks relative to the slot's nominal start-time)

In doodle script, note style can be applied to the note in 3 different ways

- using default style, assigned by `*=noteStyle` expression. Example: `*=t24v6 a5` - note a5 will acquire the default style
- explicitly, e.g. `+1k/-1s*t24v5`.
- by specifying `style` name in 'voice' statement (see below)


## Markers and note substitutions

Notes in the motif can be marked with `markers` in patentheses:

`motif m: T/-12c(M) +12c(N)` -- markers M and N

Using markers, you can program "note substitutions" using 'sub' statement. Example:

`sub M: T T/-1c T` -- e.g. the original note is c4 with duration 24, then it will be replaced with the sequence `c4 b3 c4` (each having duration 8); Substitution pattern, like motif, 
can be considered a 1-parameter function of parameter T, which gets passed by the runtime.

Notes in `sub` statement can have their own styles, but durations are interpreted in fractional units rather than ticks, e.g

`sub M: T\*2 T/-1c\*1 T\*1` -  if T is c4 with duration 24, then it will be replaced with `c4 b3 c4` where first note has duration 12, and the other two - duration 6.

## Replication sugar

To help avoid copy-pasting identical elements/sequences, syntax supports replication sugar:

- `motif m: a4 +1s +1k +1s +1k` can be abbreviated as `motif m: a4 [+1s +1k]*2`
- `voice m: +1k etc... +1s` - the total number of elements is derived from "outline". `etc...` is suppored for voice and rhythm only b/c the interpreter in these cases knows how many tokens it has to substitute.

## Pragma statement

Examples:
- pragma title: Hello, World
- pragma bpm: 120 - sets tempo in beats per minute. Please don't get fixated on the exact meaning, just put it initially as 120, and then increase or decrease according to your taste.
- (this list will grow)
