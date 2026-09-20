# Rule-Derived Test Design

**The test design is derived from the ruleset, mechanically, and then read by a human against
what they meant.**

A requirement is a finite piece of writing; an implementation is a machine that runs. The
implementation therefore always decides more than the requirement said. `if (password.Length
< 8)` satisfies "at least 8 characters" and, in the same line, also decides that surrounding
whitespace is not trimmed, that a family emoji counts as eleven characters, and that there is
no upper bound. Three of those four decisions are not in the requirement.

**No list of those decisions exists anywhere.** Without one, a test designer either reads the
whole implementation or picks by instinct, and what was never noticed cannot be written down,
measured, or reviewed. That is why software quality still depends on individual talent —
talent that does not reproduce, does not scale, and eventually resigns.

Rule-Derived Test Design produces the list. On a foundation whose rules are declarative, are
themselves what runs, and can be walked, everything observable about a ruleset is finite and
reachable: which inputs are legal in a state, whether the state is terminal and with what
result, and where each legal input leads. Walking the states and writing all of it down is the
test design, and it is produced without anyone noticing anything.

What stays with the human is judgement. The enumeration is tautological — it states what the
ruleset decides, never what it should decide — so the oracle remains human intent, outside the
system. The human changes the machine's choices where the defaults are not what they want to
see, and decides whether what came out is what they meant. Nobody writes cases, test data, or
expected values.

**This does not apply to general-purpose languages, and the narrow scope is part of the
claim.** An arbitrary `if` condition cannot be enumerated. Expressive power has to be given up
before enumerability can be bought — the same trade a type system, SQL, or a regular
expression makes.

[Ruledger](https://github.com/reny-develop/Ruledger) implements this method on top of
[Rulealize](https://github.com/reny-develop/Rulealize).

## Documents

| | |
|---|---|
| [doc/thesis.md](doc/thesis.md) | The thesis. Written in Japanese until it settles; to be rewritten in English on publication |
| [doc/verification.md](doc/verification.md) | What has been measured, what merely follows from it, and what has not been checked |
| [doc/motivation.md](doc/motivation.md) | Where this came from, in the author's own words |

## Status

Private. It goes public once the minimum needed to demonstrate the method — and to let someone
experience it — exists. Nothing here is stable.

## License

Apache-2.0. See [LICENSE](LICENSE).
