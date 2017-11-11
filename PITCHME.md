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
- Easier maintenance
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
@title[Trustworthy]

## Trustworthy

- Do not delete or modify tests. It's better to make new tests. _Unless ..._
- Separate unittests from integration
- Get rid of brittle tests
- Which values to check? Those which are real
- Build tests with no logic (`if`, `for`)
- Know difference between Whitebox vs Blackbox
- Coverage means something only if you trust your tests

+++
@title[Trustworthy: Logic is bad for you]

## Logic

```
def foobar(x, y):
    return '%s,%s' % (x, y)

def test_foobar__replay_logic__ok():
    result = foobar(1, 2)
    assert result == '%s,%s' % (1, 2)
```

+++
@title[Trustworthy: Randoms often doesn't help]

## Logic

```
def foobar(x, y):
    return '%s,%s' % (x, y)

def test_foobar__replay_logic__ok():
    x, y = randint(0, 10), randint(0, 10)
    result = foobar(x, y)
    assert result == '%s,%s' % (x, y)
```

+++
@title[Trustworthy: Blackbox]

## Blackbox

```
def calc_sum(x, y):
    calc = Calculator(randint(0, 100))
    logger.debug(‘%s + %s’, x, y)
    return calc.sum(int(x), float(y))

def test_sum__positive_numbers__ok():
    assert calc_sum(1, 2) == 3
```

+++
@title[Trustworthy: Whitebox]

## Whitebox

```
def sum(x, y):
    ...

def test_sum__positive_numbers__ok():
    assert sum(1, 2) == 3
    assert logger_mock.called_with(
        ‘%s + %s’, 1, 2
    )
    assert calculator_mock.called(...)
```

+++
@title[Trustworthy: What the coverage means?]

## "Good coverage"

```
def foobar(x, y):
    return '%s,%s' % (x, y)

def test_foobar__good_coverage__ok():
    assert foobar(1, 2)
```

---
@title[Maintainable]

## Maintainable

- Do stateless tests. Start with a blank page _each time_
- Don't do cross-dependencies, any caches and _magic_
- Don't do cross-calls of test from the other test
- Isolate things accurately (stub vs mock)
- Test different layers of abstractions separately
- Build your helpers. Treat tests code as the production one
- Do atomic tests. One test - one assertion

+++
@title[Maintainable: Helpers]

## Helpers

```
def test_foobar__app_failed__error():
    message = ‘not good’
    setup_error(message)

    response = foobar(1, 2)

    assert_error(response, message)
```

+++
@title[Maintainable: Atomic]

## Atomic

```
def foobar(x, y):
    return requests.post(
        ‘/foo’, data={‘x’: x, ‘y’: y})

def test_foobaz__atomic__ok():
    resp = foobar(1, 2)
    assert resp.status_code == 200
    assert db_mock.query().count() == 1
```

+++
@title[Maintainable: Atomic]

## Atomic

```
def foobar(x, y):
    return requests.post(
        ‘/foo’, data={‘x’: x, ‘y’: y})

def test_foobaz__atomic__ok():
    resp = foobar(1, 2)
    assert_post(resp, expected_body={'x': 1, 'y': 2})
```

+++
@title[Maintainable: Many assertions]

## Many assertions

```
def foobar(x, y):
    return 42 if y > 0 else '%s,%s' % (
       x, y,
    )

def test_foobar__all_options__ok():
    assert foobar(1, -1) == 42
    assert foobar(-1, 1) == '1,-1'
```

+++
@title[Maintainable: Less assertions]

## Less assertions

```
def foobar(x, y):
    return 42 if y > 0 else '%s,%s' % (
       x, y,
    )

def test_foobar__negative_first__ok():
    assert foobar(-1, 1) == '1,-1'

def test_foobar__negative_second__ok():
    assert foobar(1, -1) == 42
```

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
