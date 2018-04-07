**This is a write-up for an experimental concept. Both the concept and the write-up are 'work-in-progress'.**
 
## About

Doodle script is the notation for music programming designed as a learning tool for experimenting with melodic/rhythmic patterns. Implemented as part of midi-chat web application (standalone mode,
available at doodle.concert0x.com).

The idea is:

- you are given an outline of the song as an array of anchor points `[ T1, T2, ...]` indexed by bar number. Each Tn can be considered as a 'point' in a 'musical space'. Such "point" is similar to the geometric point, with the subtle difference: in musical space, the 'point' is characterized by the absolute pitch AND the scale, so each Tn in fact encodes a pair (pitch, scale). The scale attribute in some sense defines the geometry of the 'space' at a given point. In effect, the array encodes a chord progression, with essential points (anchors) selected from each chord in the progression.

- your task is to fill the gaps (interpolate) between the anchors. Interpolation sequence is called a `motif`. The motif played in bar N is a melodic pattern that has to **approach** the anchor in bar N+1. You may define several motifs - say, m1, m2 and m3, and then use motif m1 in bars 1 and 4, motif m2 - in bars 2 and 3, etc. Notice that the motif is played in bar N, but its goal is to arrive at the anchor note of bar N+1 (the latter can be considered a target note for the bar N). 

- doodle notation allows you to write motifs in a position- and scale- independent manner, so you can apply the same motif in a wide range of contexts.

(\*) *normally*, Tn is a chord note in corresponding scale. And *normally*, it's the first note in bar n, except for the special case of delayed resolution (to be discussed later).


## Motivation

The intent of doodle notation is to allow position- and scale- independent encoding of melodic patterns. 
E.g. we can take the pattern from the first bar of the Rondo Alla Turka: `b4 a4 g#4 a4 c5` and interpret it as follows: let T1=c5 in A minor. We can generalize the pattern in the following manner:
`T/-1k/+1s T/-1k T/-1k/-1c T/-1k T`. Here, the first note is written as a path expression `T/-1k/+1s`, which means: from target note T, go 1 chord note down (`k` stands for 'chord note'), then go 1 scale step up (`s` stands for 'scale note'). For the second note, we have
`T/-1k`, which means: from note T, go 1 chord note down - and so on. If we denote the last note as dot (.), then we can simplify the expression as `T/-1k/+1s ./-1s ./-1c ./+1c T`. Further, let's agree to omit the dot in the beginning of the expression - then we can shorten the encoding again:  `T/-1k/+1s -1s -1c +1c T`.  This is our final form.   

Thus, we generalized the pattern by encoding it in a position- and scale-independent manner, so it can be applied to different notes of the same or different scale. This generalization is made possible by the introduction of 'relative steps' of 3 different kinds: k-step (chord-note step), s-step (scalewise step) and c-step (chromatic step). In general, the pattern can be thought of as 1-parameter function with parameter T (where T is the anchor of the next bar). Now we can render the pattern from a different note in a different scale - e.g if we apply it to the note T=a4 over F major, we get a sequence `g4 f4 e4 f4 a4`.

Note: generalized pattern sometimes needs to know more about the context than just the target note and its scale. See details below.


## What is scale

TL;DR: In doodle script, we use slightly modified concept of the scale, by incorporating information about k-notes into the definition of the scale.

The scale can be thought of as the configuration of 3 kinds of notes: chord notes (k-notes), scale notes (s-notes) and chromatic notes (c-notes).

Important: every k-note is also s-note and c-note; every s-note is also a c-note; every note is c-note. This convention of inclusion is necessary for our notation to work.

Example: C Major scale (k-notes are green, s-notes - blue)

![C Major scale](https://dl.dropboxusercontent.com/s/gzvcbcvxq6pucik/C-major.jpg?dl=0)

Example: F# minor scale:

![F# Minor scale](https://dl.dropboxusercontent.com/s/3fjxfggfew3its6/F%23-minor.jpg?dl=0)

In our model, the scale changes on the onset of the bar, so in each bar we have a different configuration (\*) Notes 'work' like a group of musicians, which change their roles during the performance of the song: some come into the front stage, others recede into the background etc. K-notes are similar to soloists, they are featured prominently in the time span of the bar; s-notes serve as a glue for k-notes; c-notes are also used as a glue in some contexts. (This can only be demonstrated on examples).

The reason why this glue works is this: each scale induces a kind of vector field (or: **landscape**) of gravitational forces acting between notes. As an example, you can play C major chord and hit F# in the right hand - it will want to go to G. You have to experiment to find out how this field works for every note. We can also think of this field in terms of tensions/resolutions (which is a more common metaphor). When F#, which feels tense against C major, goes to G, we say it "resolves to G". (\*\*)  

The scales having the same intervalic structure, but starting on different notes, constitute the class of equivalence called "scale category". Example of scale category: major scale, corresponds to the following sequence of intervals: `x +2c +2c +1c +2c +2c +2c`, where starting point x is a parameter. For every x, we can build scale instance xM having x as a starting note (root), e.g. C#M is a major scale starting on note C#.

Please notice that based on this definition, 2 different scales may share the same SET of notes, but differ in a way how k-notes are selected. E.g. C Major and D minor (dorian) consist of same notes `c d e f g a b`, but k-notes are `c e g` for the former, and `d f a` for the latter. Standard chord progressions very often are based on the same set of 7 s-notes throughout the entire composition - the only thing that changes is the set of k-notes, which is enough to cause the radical change in the harmonic landscape from one bar to another (in melodic minor, it's a bit more complicated, but just a bit). It feels like the landscape is "breezing".

![chord progression](https://dl.dropboxusercontent.com/s/dxthpbkbxj6dzj2/chord-progression.gif?dl=0)

In doodle script, we have some commonly used scales (pre)defined in terms of note properties rather than intervals. E.g. Major scale is defined as an array of 12 note types: `k c s c k s c k c s c s`.
When we need a major scale starting on note G, we can create an instance: `MajorScale.getInstance("g")`, and then redirect all requests for step calculations to this instance; runtime is doing it behind the scenes while processing the notation. 

As a degenerate case of scale, we have chromatic scale, defined as "s s s s s s s s s s s" (that is, every note is a scale note, with no chord notes).
Where the notion of scale comes from and how standard scales are defined is explained later in this write-up.

(\*): the idea is intentionally simplified. The way how scales are used in jazz is not that straighforward (where exactly the change of scale occurs is in the eye of beholder), but we need to start from a simple concept. One of the ideas of "forward motion" is that approach sequence tends to align with the target scale (or its dominant scale).

(\*\*) Doodle notation is based upon observation that the "curvature of space" around k-notes is similar enough for all scales. This makes it possible to define common approach sequences (appogiaturas) in abstract terms of k-notes, s-notes and c-notes (this is a well-known fact, can be found in every textbook, the difference is just that we use notation where the textbook uses verbal description).

## Hello, World

```
pragma title: HelloWorld
pragma bpm: 120                             # tempo in beats per minute, beat = 24 ticks
outline: f5/~FM g5/~GM c6/~C7 f5/~FM        # outline: 4 notes
motif m: T/-12c +4s ^T                      # motif - approaching T via sequence of 3 notes
rhythm r: 24 24 24                          # note styles for motif (each duration=24 ticks)
voice: m*r m*r m*r m*r                      # what motif to play for n-th anchor, with what rhythm
```   

Executes as follows: 
- parse "outline"  (results in an array of 4 anchor points)
- parse "voice"  (results in array of 4 pairs (motif, rhythm) to be rendered for the corresponding anchor)
- iterate over `voice`: let n-th element of `voice` be m\*r; render motif m paired with rhythm pattern r. 

NOTES: 

- rhythm parameter `r` in voice pattern can be omitted; if so, the motif is rendered with the styles encoded in the motif itself, e.g

```
outline: f5/~FM g5/~GM c6/~C7 f5/~FM 
motif m: *=24 T/-12c +4s ^T  # default note duration = 24 ticks
voice v1: m m m m            # no style override, uses the original style from the motif
```
- Symbol caret (^) marks the note to be synchronized with the onset of the next bar. In other words, it allows you to position your motif on time axis relative to the bar boundary.

You can define **more than one voice** (e.g. adding a separate voice for bass line).  

## Motif

The motif can be written as a sequence of note path expressions separated by one or more spaces. Each note path expression evaluates to a note, which is added to the sequence. Syntactically, note path expression is a sequence of path elements separated by slash ("/"). Examples of path expressions: `T/-1c`, `+1k/-1s`. Notation also supports assigment of the form `name=expr`, e.g. we can write `x=c5/~CM/+1k x/+1k`. Assignment does not add note to the sequence, just memorizes it for future reference. The last example produces a single note g5.

Though the goal of the motif is to approach the anchor note of the next bar as a target, the motif does not necessarily ends on this note - it can continue, thus overlapping in time with the next bar. In general, motif can be thought of as having 3 parts: approach notes; target note; exit notes. Which note of the motif is played on the onset of the next bar is controlled by a caret symbol.

Very often, exit notes of the motif can be perceived as the beginning of approach sequence for the next target. Some people believe this is the essense of our intuition of the melody.

Different cases of motif layout are illustrated below: approach and exit, approach only, exit only, delayed resolution, overlapping motifs, continuous line.

![Motif layout examples](https://dl.dropboxusercontent.com/s/zy1dbpx9diwgz22/motif-cases-fig.jpg?dl=0)

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
- o7 - move note to given octave (`c#/07` evaluates to c#7)

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
- .=T/-1s - assign reference note, e.g. `.=a#5 +1c` evaluates to `b5`
- ~=FM - assign scale for a reference note (current scale)
- ~xx=scale - assign a different scale for earlier defined note `xx`, e.g. ~T=FM - results in note T being associated with scale FM
- *=style - sets the default style for the following motif elements

Predefined variables:

- T - anchor note to be used as target in the current motif
- S - previous anchor


When the variable is used as an element of path expression, the scale associated with it becomes "current scale", so all further calculations of steps are performed relative to this scale.
Special expressions `~x` and `@x` are used to refer to the scale or the pitch of x individually. Example: in the expression
`x=d4/~Dm c4/CM x/+1k` the last note evaluates to f4, because the scale is taken from x. Compare with `x=d4/~Dm c4/CM @x/+1k`. Now the last note is e4, because only the pitch is taken from note x.
Special case of scale change: `~dom` - changes scale to the dominant scale of current scale. 


## Note Style

Note style incorporates information about note duration, velocity etc, e.g. t24v6 means "note duration is 24 ticks, velocity=6 (gets multiplied by 16 while converting to MIDI velocity value).
The following attributes are supported:

- slot duration, e.g t36 (or simply 36, 't' prefix is optional)
- effective duration, e.g. e48 (48 ticks)
- velocity level, e.g. v7 (MIDI velocity=7*16)
- time shift, e.g. +5 (will be shifted by 5 ticks relative to the slot's nominal start-time)

In doodle script, note style can be applied to the note in 3 different ways

- using default style, assigned by `*=noteStyle` expression. Example: `*=24v6 a5` - note a5 will acquire the default style
- explicitly, per note, e.g. `+1k/-1s*24v5`.
- by specifying `rhythm` name in 'voice' statement


## Markers and note substitutions

Notes in the motif can be marked with `markers` in patentheses:

`motif m: T/-12c(M) +12c(N)` -- markers M and N

Several markers can be specified, separated by semicolons:

`motif m: T/-12c(M;N) +12c(K)` -- first note has 2 markers: M and N

Using markers, you can program "note substitutions" using 'sub' statement. Example:

`sub M: T T/-1c T` -- e.g. the original note is c4 with duration 24, then it will be replaced with the sequence `c4 b3 c4` (each having duration 8); Substitution pattern, like motif, 
can be considered a 1-parameter function of parameter T, which gets passed by the runtime.

Notes in `sub` statement can have their own styles, but durations are interpreted in fractional units rather than ticks, e.g

`sub M: T*2 T/-1c*1 T*1` -  if T is c4 with duration 24, then it will be replaced with `c4 b3 c4` where first note has duration 12, and the other two - duration 6.

## Replication sugar

To help avoid copy-pasting identical elements/sequences, syntax supports replication sugar:

- `motif m: a4 +1s +1k +1s +1k` can be abbreviated as `motif m: a4 [+1s +1k]*2`
- `voice m: +1k etc... +1s` - the total number of elements is derived from "outline". `etc...` is suppored for voice and rhythm only b/c the interpreter in these cases knows how many tokens it has to substitute.

## Pragma statement

Examples:
- pragma title: Hello, World
- pragma bpm: 120 - sets tempo in beats per minute. Please don't get fixated on the exact meaning, just put it initially as 120, and then increase or decrease according to your taste.
- (this list will grow)

## Trivia from music theory

### Chromatic scale

Here's a short explanation of why we have 12 notes per octave; where the notion of octave comes from; and why the ratio of frequencies of 2 neighboring is constant for all notes and is equal to 2^(1/12).

When you hit any note on piano, you can hear the superposition of frequencies, of which one is the fundamental frequency of the note, and others are the multiples - so if we denote the original frequency as f, then for each n, you can hear also the corresponding harmonic - the tone of frequency n\*f (the amplitude of which is not linear with respect to n). This follows from string equation. In real world, the mathematical model doesn't hold exactly, but it's still valid as a first approximation. 

Among all harmonics the strongest corresponds to n=2, so when we hit the note, say, of frequency 400Hz, then we can hear not only 400Hz tone, but also 800Hz tone. It makes sense to allocate a specific note with frequency 800Hz - these 2 notes will sound "harmonious" if played together. By the same logic, we need also a note of frequency f/2: after all, if we have any notes lower than f, then each of them will have a corresponding "double frequency" note, so one of these "double frequencies" must be exactly f.

Then we have the next strongest harmonic corresponding to 3\*f. Same logic applies here. As soon as we choose to tune any note to frequency f, it just begs for allocating another note with the frequency 3\*f.

We could go further and apply same logic to the multiples of 5, 7 etc, and this (or similar) was tried, but it would lead to proliferation of tones that do not necessarily form a consistent set musically. Historically, different types of tuning were explored (based on different principles), each giving birth to a different style of music (long story, omitted here)

Eventually, somebody noticed that the factors of 2 and 3 might be enough to derive a sensible tuning for the entire setup. This is made possible by curious numeric coincidence: 
3^12 is close to 2^19. 

Indeed, let's postulate the following rules of tuning. 

1. Select `seed frequency` f arbirtarily, and put it into the set.
2. If f belongs to the set, put 2\*f into the set (propagation to higher octaves)
3. If f belongs to the set, put 1/2\*f into the set (propagation to lowe octaves)
4. if f belongs to the set, put 3\*f into the set (generation)

(we will see why we don't need a rule for 1/3\*f shortly)

It's easy to see that after we apply "generation" rule 12 times , each time propagating the newly coined note into all octaves, we will arrive (on 12th step) at the value very close to f. About 400 years ago, somebody came up with a brilliant idea (\*): what if we replace rule 4 by another rule, where instead of factor 3 we would use a value, say, alpha, such that
alpha^12 *exactly* equals 2^19? With such alpha, after 12 iterations the loop closes *exactly*, and we will have exactly 12 distinct notes in each octave. The alpha in question is equal to 2^(19/12)=2.9966.

Listen to your favorite jazz or rock song if you need a proof that the idea works quite well.

(\*) in fact, the idea was much older than that. The benefits of equals temperament tuning were not obvious, and became apparent only after a long period of experimentation and tinkering. See [wikipedia article](https://en.wikipedia.org/wiki/Equal_temperament) for details.

The rest of this section is yet to be written, will just list the main points for now

### Intervals

TODO. Main points:

- the notion of interval makes sense because frequencies of notes in chromatic scale form arithmetic progression in log scale
- interval names 
- inversions 
- special role of intervals: fifth (f\*3/2, `+7c`), fourth (inversion of fifth, f\*4/3, `+5c`), tritone (f*sqrt(2), `+6c`, equals to own inversion)
- you can play entire accompanement using just 2-note voicings of fifth, fourth and tritone. They preserve their function when inverted.

### Triads and scales

TODO. Main points:

- triad as interpolation of fifth by adding extra note in between
- triads remain recognizable and preserve their function when inverted
- 4 types of triad `x +3c +4c` (minor), `x +4c +3c` (major), `x +3c +3c` (diminished), `x +4c +4c` (augmented)
- scale as further interpolation of triad
- triads also can be derived from the analysis of songs: on-beat notes are most likely to be notes from the triad
- k-notes almost always form a triad, sometimes 4th k-note is added

### Chord progressions

TODO. Main points:

- very limited number of chord progressions gets reused over and over across Western music
- along the progression, we normally have the same set of s-notes, with varying subset of k-notes. Shows in the diagram as a movement between loweset row and middle row.
- slow-moving pattern of increased tension towards V chord, then resolution to I chord.
- using the metaphor of attraction, V chord is attracted to I chord.

### Bass line and inner melodies

TODO. Main points:

- While listening to a song, we are aware of just a single melody. But there's at least one more: bass line. We just don't normally pay attention to it b/c it is moving slowly. Without it, the whole thing would just fall apart.
- bass line is a (preferably) continuous sequence of essential notes from chord progression. Playing roots only - sucks.
- inner melodies (listen to Bill Evans, Keith Jarrett)

### Further reading

- ["The Jazz Piano Book" by Mark Levine](https://www.amazon.com/Jazz-Piano-Book-Mark-Levine/dp/0961470151/ref=sr_1_sc_1?s=books&ie=UTF8&qid=1522792962&sr=1-1-spell)

- ["Forward Motion" by Hal Galper](https://www.amazon.com/Forward-Motion-Hal-Galper/dp/1883217415/ref=sr_1_1?s=books&ie=UTF8&qid=1522793080&sr=1-1)


