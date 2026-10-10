# Rule-Derived Test Design

**Rule-Derived Test Design (RDTD) is a way of building software so that nobody has to design its
tests. What the software may do is written as rules that are declarative, are themselves what
runs, and can be walked. Software built that way has a test design that is derived mechanically
from its rules, and a person reads it.**

The constraint is on how the software is built; the test design is what it buys. Once the
software is built that way, testing it takes two steps:

1. **Derive (machine).** Walk every state the rules can reach. For each one, write down what
   may be done there, what is refused, where each action leads, and whether the state is an end.
   That list is the test design.
2. **Judge (person).** Whoever holds the requirement reads the list against what they meant.
   Where it differs, the rules are changed.

Nobody writes test cases or expected values. The same rules give the same test design, whoever
derives it and however many times.

## Why it is needed

A requirement is a finite piece of writing; an implementation is a machine that runs. The
implementation therefore always decides more than the requirement said.
`if (password.Length < 8)` satisfies "at least 8 characters". The same line also decides that
surrounding spaces count, that there is no upper bound, and what counts as one character: one
symbol as the user sees it, or one unit of the string's encoding. The requirement mentioned none
of them.

**No list of those decisions exists anywhere.** Without one, a test designer either reads the
whole implementation or picks by instinct. What was never noticed cannot be written down,
measured, or reviewed. That is why software quality still depends on individual talent, and
talent does not reproduce, does not scale, and eventually resigns.

RDTD makes the list, and makes it without anyone having to notice anything.

## What it asks of the rules

Each of the three conditions is there because the list cannot be made without it.

| The rules must be | Without it |
|---|---|
| declarative | reading a rule means running arbitrary code, and what lies past an `if` cannot be listed |
| themselves what runs | the list describes a specification of the software, not the software. Whether the software does what the specification says would have to be tested separately |
| walkable | even a finite set of reachable states cannot be counted out |

## An example

An approval workflow: a request is drafted, submitted, then approved or rejected. Walking its
rules gives this list.

| State | May be done | Refused | Ends? |
|---|---|---|---|
| Draft | edit → Draft, submit → Submitted | approve, reject | no |
| Submitted | approve → Approved, reject → Rejected, send back → Draft | edit | no |
| Approved | — | everything | yes, approved |
| Rejected | — | everything | yes, rejected |

Nobody had to think of anything to produce it. What is left for a person are questions such as
"Is it right that a submitted request cannot be edited?" and "Should a rejected request really
be final?"

## What stays with a person

The list says what the rules decide, never what they should decide. Correctness therefore stays
where it always was: in the intent of the person who holds the requirement, outside anything the
machine holds. Their work is to read the list and judge it.

That person rarely reads rules or code. So the list has to reach them in a form they already
read, in the words and the screens of what they asked for, not as a document written in the
rules' notation.

## What it costs

**Expressive power.** An arbitrary `if` cannot be listed, so RDTD does not apply to programs
written in a general-purpose language. That narrow scope is part of the claim. Enumerability has
to be paid for with expressive power, the same trade a type system, SQL, or a regular expression
makes.

**Values that cannot be counted out.** Where a value is chosen from a list, is true or false, or
is a whole number within bounds, every value is walked. Where it is typed freely, such as a name
or a sentence, it cannot be. There the person names the values they want to see, and those are
walked and read like the rest. The machine never makes one up.

**What the primitives decide inside.** Every rule language rests on basic operations it cannot
see into. The list covers what the rules decide by combining those operations. What an
operation decides inside itself is not listed; it is ordinary code and is tested the ordinary
way. So operations are kept small and general, and whatever is specific to one application stays
in the rules, where it is listed.

**Operations must be pure.** An operation has to give the same answer for the same arguments
and read nothing from outside them. Otherwise the same input in the same state could lead to
different places, and walking the states would prove nothing.

## How it differs from nearby methods

- **Model-based testing** derives tests from a model that is separate from the implementation,
  so the gap between the two remains. In RDTD the rules are the implementation, so there is no
  separate model to diverge from.
- **Property-based testing** generates inputs mechanically, but a person still has to think of
  the properties to check, and the inputs are sampled. RDTD walks every state and asks the person
  only to read.
- **Code coverage** measures what the tests touched, not what the implementation decided. It
  cannot point at a decision nobody wrote a test for.

## An implementation: Rulealize, Ruledger and RulealizeStudio.Avalonia

Everything above is the method; this section is one place it has been built.

[Rulealize](https://github.com/reny-develop/Rulealize) is a runtime whose rule sets are
declarative, run as written, and can be walked.
[Ruledger](https://github.com/reny-develop/Ruledger) walks a Rulealize rule set and writes the
list down; its README says what it does and does not do.

The operations a rule set can use come from the vocabularies loaded into the runtime. When none
of them says what a rule set needs, an engineer writes one: a plugin, written in C# against
Rulealize's abstractions
([writing a vocabulary](https://github.com/reny-develop/Rulealize.Templates/blob/main/doc/writing-a-vocabulary.md)).
Nothing in the derivation changes for it. What it costs is what is described above under "What
the primitives decide inside".

[RulealizeStudio.Avalonia](https://github.com/reny-develop/RulealizeStudio.Avalonia) is a VS Code
extension that carries out both steps for an Avalonia desktop application, for somebody who reads
neither rules nor code:

1. **Say what you want.** You design the application's window by dragging controls onto it, and
   write what it should do as states and transitions in plain words.
2. **Have the rules written.** You ask your own coding agent, such as Claude Code, working in the
   application's folder, to write the rule set from the window and that description. The
   extension does not write rules and contains no agent.
3. **Read the test design.** The extension runs Ruledger on the rules and shows every test case as
   the window you designed in step 1, drawn as it would stand at each point: the steps that reach
   a situation, the window after them, what may be done next and where it leads, and what is
   refused and what the refusal says.
4. **Ask for changes.** Where something is not what you meant, you tell your agent in your own
   words. Each test case the change touched is shown before and after, side by side, and you keep
   the version you meant by committing it.

You read no JSON and no XAML.
[doc/practice.md](https://github.com/reny-develop/RulealizeStudio.Avalonia/blob/main/doc/practice.md)
walks through it from an empty folder.

## Trying it

Each of these lets you check the claims above by running something rather than by reading this
page.

| | |
|---|---|
| the claims, run at size | `dotnet test verify/Ruledger.Verify.csproj --logger "console;verbosity=detailed"` in a clone of [Ruledger](https://github.com/reny-develop/Ruledger). The approval workflow under "An example" is small enough to check by eye; this checks the same claims on rule sets that are not, board games and business workflows: that every reachable state is listed with what may be done, what is refused and where it leads, that the same rules walked twice give the same bytes, and that changing one line of the rules moves only what that line decides. Each check prints what it found, and a passing run means every one of those claims held on the code you cloned. About four minutes, and nothing beyond the clone |
| reading the rules | [Ruledger.Cli's doc/tutorial.md](https://github.com/reny-develop/Ruledger.Cli/blob/main/doc/tutorial.md), about half an hour at a shell, for a developer who can read a rule set. You change a board game's rules one line at a time and read the diff of the test design: it shows everything the change decided, including consequences you did not think to look for. Then the same on a shift roster. Along the way you write no test case, no expected value and no program to drive the rules |
| reading the screen | [RulealizeStudio.Avalonia's doc/practice.md](https://github.com/reny-develop/RulealizeStudio.Avalonia/blob/main/doc/practice.md), about half an hour in VS Code with a coding agent such as Claude Code. You take a small application of your own through the four steps under "An implementation": design its window, say what it should do, have your agent write the rules, then read every test case drawn on that window and ask for changes until it does what you meant. You read no JSON and no XAML: this is the method as it reaches whoever holds the requirement, not the person who writes the rules |

## License

Apache-2.0. See [LICENSE](LICENSE).
