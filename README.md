# Rule-Derived Test Design

**The test design is derived from the rule set, mechanically, and then read by a human against
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
themselves what runs, and can be walked, everything observable about a rule set is finite and
reachable: which inputs are legal in a state, whether the state is terminal and with what
result, and where each legal input leads. Walking the states and writing all of it down is the
test design, and it is produced without anyone noticing anything.

What stays with the human is judgement. The enumeration is tautological — it states what the
rule set decides, never what it should decide — so what counts as correct stays where it
already was, in the intent of the person reading, outside anything the machine holds. The
human changes the machine's choices where the defaults are not what they want to see, and
decides whether what came out is what they meant. Nobody writes cases, test data, or
expected values.

**This does not apply to general-purpose languages, and the narrow scope is part of the
claim.** An arbitrary `if` condition cannot be enumerated. Expressive power has to be given up
before enumerability can be bought — the same trade a type system, SQL, or a regular
expression makes.

**Whom it reaches first is a stage, not a condition.** The foundation is new, so hardly
anyone can read a rule set yet, and today this reaches the people who write them and the
people who review those changes. The method is aimed past them, at whoever holds the
requirement: they assemble the rules, read what came out, and say whether it is what they
meant. What stands in between is a way to see and edit a rule set, and that does not exist
yet. Read the first stage as the condition and this is a method with nobody left to read it.

[Ruledger](https://github.com/reny-develop/Ruledger) implements this method on top of
[Rulealize](https://github.com/reny-develop/Rulealize).

## Seeing it rather than reading about it

Everything claimed here is either a number a command prints or half an hour at a shell.

| | |
|---|---|
| the proof | `dotnet test verify/Ruledger.Verify.csproj --logger "console;verbosity=detailed"` in a clone of Ruledger. It walks the measured rule sets, prints what it found, and fails if any of it has moved. Four minutes, and it needs nothing but a clone |
| the experience | [Ruledger's doc/tutorial.md](https://github.com/reny-develop/Ruledger/blob/main/doc/tutorial.md). Two tools installed, a rule set changed one line at a time, and a count at the end of what did not happen |

Neither of them is this document, and that is deliberate. A method whose evidence is prose
is a method somebody has to take on trust.

## What is not here

There were three working documents: the thesis, a record of what had been measured, and
the author's own account of where this came from. The first two are gone. They were
scaffolding — a thesis is a claim, and a record of measurements is a claim about
measurements, and both were replaced by measurements that run. The third is kept out of
the repository rather than deleted; it is not a reader's document.

What survives them is this page and a tool you can run, which is what the two sections
above are. Deleting the rest was the last thing the method asked for.

## Status

The method is implemented and measured. [Ruledger](https://github.com/reny-develop/Ruledger)
is where both of those live, and its README says what it does and does not do.

## License

Apache-2.0. See [LICENSE](LICENSE).
