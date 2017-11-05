@title[Introduction]
## The test which will save your day
##### by Ivan Styazhkin from DataRobot

+++
@title[Disclaimer]

## I am not selling
- No particular technology or methodology
- I want to talk about very basic principles
- All of this is based on my experience and knowledge
- I invite you to share your stories after the talk

+++
@title[Epigraph]

## Quality
> The test of the machine is the satisfaction it gives you.
 There isn't any other test.
 If the machine produces tranquility it's right.
 If it disturbs you it's wrong until either the machine or your mind is changed.

+++
@title[Expectations]

## Target audience
- You have a gut feeling that tests are good for your project
- You do read & write tests on daily basis
- You want to think ahead about testability of system

---
@title[5 whys]

# Why do we need tests?

+++
@title[5 because]

## To have
- Less errors
- Easier maintenance and change
- Easier to understand/reason about the code
- Easier to develop new features

+++
@title[Extras]

## Nice to have
- Confidence to all parties (pigs & chickens)
- Freedom of choice for a next step

---
@title[Definitions]

## Some Definitions
To reduce misunderstanding

+++
@title[Definitions: Autotest]
### Autotest
a check or group of checks, which can be executed by *one simple human action*

+++
@title[Definitions: Unittest]
### Unittest
a test of a isolated part of the program (unit of work) for correctness

+++
@title[Definitions: Integration test]
### Integration test
a test of interaction of several programs (or it's parts) for correctness and coherence

+++
@title[Definitions: Test utils]
### Test utils
a set of programs for modeling of work environment and execution of checks

---
@title[What is it]

# A good test is

+++
@title[A good test: Three aspects]

## A good test is
- Trustworthy
- Maintainable
- Readable

+++
@title[A good test: Trustworthy]

## Trustworthy
If tests are failed at start because of misconfigurations or it's a
"known issue" of some dependency/part of the system

*No one* will run them!

+++
@title[A good test: Maintainable]

## Maintainable
If we spend more time for setting up, running or required changes than on actual development

*No one* will write them!

+++
@title[A good test: Readable]

## Readable
If we need much time to debug a test and figure out the reason of it's failure

*No one* will care about "green build"

---
@title[Thank you]

## T[h]ank you!

@inesusvet

fb.com/ivan.nesusvet

ivan.styazhkin@datarobot.com

+++
@title[References]

## Further reading
- "The art of unit testing" by Roy Oserove
- "TDD by example" by Kent Beck
