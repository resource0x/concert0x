## About

Doodle script is a notation for music programming. Designed as a learning tool for experimenting with melodic/rythmic patterns. Implemented as part of midi-chat web application.

## Melodic patterns

Melodic pattern is represented as a sequence of expressions, where the expression has either of the following formats:

- hop/hop/.../hop, e.g. T/-1c/oct.5 means: from note T (defined earlier), move 1 chromatic step down, then move the result to octave 5. See hop syntax below
- name=hop/hop/.../hop - same, but the note is not included into output stream. Instead, it gets assigned to the variable `name`

## Hop types

- . - last note
- name - variable name, e.g T
- xxdd, where xx-note name, dd - octave, e.g. c#5 - note C# in octave 5 (absolute notation)
- [<>][=]?romanLiteral, e.g. >III - move to 3rd degree of current scale up
- ~scale - e.g ~CM - add scale marker to the note, e.g. c4/~CM - note c in octave 4, carrying a marker of C Major. Marker is necessary for scale arithmetic.
- [+-]nnn[skc] - relative step, e.g. +2s - two scale steps up from current position. There are 3 types of steps: chromatic(c), diatonic (s), chord note step (k)
E.g. c4/~CM/+1k evaluates as e4
- 

