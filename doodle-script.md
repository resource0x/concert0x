## About

Doodle script is a notation for music programming. Designed as a learning tool for experimenting with melodic/rythmic patterns. Implemented as part of midi-chat web application.

Because there's no standard terminology, I had to invent one, which is kind of 'work-in-progress', but this affects only verbal descriptions. The meaning of operators is well-defined.

## Melodic patterns

Melodic pattern is represented as a sequence of expressions, where the expression has either of the following formats:

- hop/hop/.../hop, e.g. T/-1c/oct.5 means: from note T (defined earlier), move 1 chromatic step down, then move the result to octave 5. See hop syntax below
- name=hop/hop/.../hop - same, but the note is not included into output stream. Instead, it gets assigned to the variable `name`

## Hop types

- . - last note
- name - variable name, e.g T. Names T and S are reserved for standard variables: T is a target note for a motif; S is the target note of the previous bar.
- xxdd, where xx-note name, dd - octave, e.g. c#5 - note C# in octave 5 (absolute notation)
- [<>][=]?romanLiteral, e.g. >III - move to 3rd degree of current scale up
- ~scale - e.g ~CM - add scale marker to the note, e.g. c4/~CM - note c in octave 4, carrying a marker of C Major. Marker is necessary for scale arithmetic.
- [+-]nnn[skc] - relative step, e.g. +2s - two scale steps up from current position. There are 3 types of steps: chromatic(c), diatonic (s), chord note step (k)
E.g. c4/~CM/+1k evaluates as e4
- is.type -assertion like is.k - assert that current position is k-note. If assertion fails, the whole motif is rejected (except when there's no alternatives)
- isnot.type  - assertion, e.g. isnot.k - current position is not k-note
- is.type?correctiveStep, e.g. is.s?+1c - if current position is s-note then correct it by chromatic step.
- isnot.type?correctiveStep - similar to the above, just with opposite condition
- ~scaleName, e.g. ~Cm - switch to C minor scale
- oct.N - e.g. oct.7 - move to the same note in a different octave, e.g. c#5/oct.7 evaluates as c#7

IMPORTANT: note is represented internally as pair (picth, scale). Therefore, every note carries information not only about the pitch, but about the scale associated with it (no requirement for it being a scale note). 
This makes it possible to perform step calculations of different types.

Special cases 
- .=expr - assign reference note, e.g. .=T/-1s
- ~=scale - assign scale for a reference note
- ~name=scale - for note reffered by name, assign a different scale, e.g. ~T=FM - results in note T being associated with scale FM
