# Rule-Derived Test Design

**The test design is derived from the rule set, mechanically, and then read by whoever holds the
requirement against what they meant.**

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

What stays with a human is judgement. The enumeration is tautological — it states what the
rule set decides, never what it should decide — so what counts as correct stays where it
already was, in the intent of the person who holds the requirement, outside anything the
machine holds. They change the machine's choices where the defaults are not what they want to
see, and decide whether what came out is what they meant. Nobody writes cases, test data, or
expected values.

**This does not apply to general-purpose languages, and the narrow scope is part of the
claim.** An arbitrary `if` condition cannot be enumerated. Expressive power has to be given up
before enumerability can be bought — the same trade a type system, SQL, or a regular
expression makes.

**What has been given up can be added back, one word at a time.** A rule set can only use the
operations that the vocabularies loaded into the runtime provide. When none of them says what a
rule set needs, an engineer writes one: a plugin, written in C# against Rulealize's abstractions
([writing a vocabulary](https://github.com/reny-develop/Rulealize.Templates/blob/main/doc/writing-a-vocabulary.md)).
Nothing in the derivation changes for it. Every state a rule set reaches through the new operation,
and every input legal there, is walked and listed like any other. What the operation decides inside itself is
not listed: that is ordinary code again, tested the way ordinary code is. So an operation is
kept small, general and pure — the same answer for the same arguments, reading nothing from
outside them — and whatever is specific to one application stays in the rule set, where it is
listed.

## Read by the person who holds the requirement

The judgement belongs to whoever holds the requirement, and that is rarely somebody who reads a
rule set. So the test design is not handed to them as a document. It is shown as the application
they asked for, on its own window: each situation the rules can reach, the steps that reach it,
what may be done there and what is refused, in the words of their own screen.

[RulealizeStudio.Avalonia](https://github.com/reny-develop/RulealizeStudio.Avalonia) is where
that is done, in VS Code. The person designs the screen and writes what the application should
do. An agent writes the rules from that. The person then reads the test cases on the
application's window and asks for changes. Each change is shown as the window before and after,
and they keep what they meant. They read no JSON and no XAML.

**An agent is not part of the method.** What the method needs is rules that are declarative, run
and can be walked; who writes them is outside it, and so the reading is the same whoever wrote
them. An agent writes them today because nothing else lets somebody who is not an engineer
assemble them. That they assemble the rules themselves is what this is aimed at. There is no
means of doing it in sight yet.

## Seeing it rather than reading about it

Everything claimed here is a number a command prints, half an hour at a shell, or half an hour
in VS Code.

| | |
|---|---|
| the proof | `dotnet test verify/Ruledger.Verify.csproj --logger "console;verbosity=detailed"` in a clone of [Ruledger](https://github.com/reny-develop/Ruledger). It walks the measured rule sets, prints what it found, and fails if any of it has moved. Four minutes, and it needs nothing but a clone |
| reading the rules | [Ruledger.Cli's doc/tutorial.md](https://github.com/reny-develop/Ruledger.Cli/blob/main/doc/tutorial.md). Two tools installed, a rule set changed one line at a time, and a count at the end of what did not happen |
| reading the screen | [RulealizeStudio.Avalonia's doc/practice.md](https://github.com/reny-develop/RulealizeStudio.Avalonia/blob/main/doc/practice.md). An empty folder to an application that does what you meant, without reading JSON or XAML |

None of them is this document, and that is deliberate. A method whose evidence is prose is a
method somebody has to take on trust.

## Status

The method is implemented and measured in [Ruledger](https://github.com/reny-develop/Ruledger),
on [Rulealize](https://github.com/reny-develop/Rulealize); its README says what it does and does
not do. [RulealizeStudio.Avalonia](https://github.com/reny-develop/RulealizeStudio.Avalonia)
brings it to the person who holds the requirement, for Avalonia applications.

## License

Apache-2.0. See [LICENSE](LICENSE).
