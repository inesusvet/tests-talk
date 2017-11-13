@title[Introduction]
## The test which will save your day
##### by Ivan Styazhkin from DataRobot
`gitpitch.com/inesusvet/tests-talk`

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

+++
@title[References]

## Further reading
- "The art of unit testing" by Roy Osherove
- "Extreme programming: TDD by example" by Kent Beck

---
@title[5 whys]

# Why do we need tests?

+++
@title[5 because]

## To have
- Less errors
- Easier to maintain (bring new changes)
- Easier to understand/reason about the code
- Easier to communicate about new changes
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

*No one* will care about <span style="color: green; font-weight: bold">green build</span>


---
@title[Trustworthy: Logic is bad for you]

### Logic

Why this test isn't trustworthy?

```
def foobar(x, y):
    return '%s,%s' % (x, y)

def test_foobar__replay_logic__ok():
    result = foobar(1, 2)
    assert result == '%s,%s' % (1, 2)
```

+++
@title[Trustworthy: Randoms often doesn't help]

### Random logic

Why this change don't make things better?

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

### Blackbox

Check system's output with known input often quite simple

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

### Whitebox

Check internals often lead to brittle tests

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

### "Good coverage"

Why this test isn't trustworthy?

```
def foobar(x, y):
    return '%s,%s' % (x, y)

def test_foobar__good_coverage__ok():
    assert foobar(1, 2)
```

+++
@title[Trustworthy]

## Trustworthy

- It's better to make new tests. Not modify existent
- Separate unittests from integration
- Get rid of _brittle_ tests
- Which values to check? Those which are real
- Build tests with no logic (`if`, `for`)
- Know difference between *Whitebox* vs *Blackbox*
- Trust coverage only if you trust your tests

---
@title[Maintainable: Helpers]

### Use helpers, Luke

Be a lazy developer

```
def test_foobar__app_failed__error():
    message = ‘not good’
    setup_error(message)

    response = foobar(1, 2)

    assert_error(response, message)
```

+++
@title[Maintainable: Atomic]

### Not so atomic test

Why this test poorly maintainable?

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

### Atomic test

Check only one aspect of work at once

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

### Many assertions

Don't be a lazy developer

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

### Less assertions

Make it simple & clear

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

+++
@title[Maintainable]

## Maintainable

- Do stateless tests. Start with a blank page _each time_
- Don't do cross-dependencies, any caches and _magic_
- Don't do cross-calls of test from the other test
- Isolate things accurately (stub vs mock)
- Test different layers of abstractions separately
- Build your helpers
- Do atomic tests. One test - one assertion

---
@title[Readable: Clear name]

### Not so clear name

It's the very first thing which will see a developer

```
def foobar(x, y):
    return '%s,%s' % (x, y)

def test_foobar1():
    ...

def test_foobar_failed():
    ...
```

+++
@title[Readable: Better name]

### Better name

Gives more clues what's broken.
Do not fear to use really long names.
Remember that you write that for a human at the first place

```
def foobar(x, y):
    return '%s,%s' % (x, y)

def test_foobar__positive_numbers__ok():
    small, big = 1, 100
    assert foobar(small, big) == '1,100'
```

+++
@title[Readable: Not so clear steps]

### Not so clear steps

What's mocked here? Gimme answer in 5 seconds!

```
def test_foobar__false_result__ok():
    request = Request.from_dict({‘status’: 200,
                                 ‘body’: ‘{“error”:”failed”}’})
    assert request.is_ok == True
    patch(‘foobar.is_on’, False).start()
    assert foobar(request, 1, 2) == ‘0,0’
```

+++
@title[Readable: Clear steps]

### Clear steps

Save your time

```
def test_foobar__false_result__ok():
    setup_foobar_ok(‘failed’)

    response = foobar(‘lool’)

    assert response is None
```

+++
@title[Readable: Magic]

### Magic

No one can understand that.
Only one person can debug it.
This person can't afford vacations

```
def test_foobar__watch_my_magic__ok():
    assert foobar(42) == ‘3,141529'
```

Example from backbone.js

![qr-code](https://github.com/inesusvet/tests-talk/raw/master/assets/bb-test.png)

+++
@title[Readable: Less magic]

### Less magic

Make it "like well-written prose". Readers want to know _why?_ not _how?_

```
def foobar(x, y):
    if y < 0:
        return 42
    return '%s,%s' % (x, y)

def test_foobar__error_code__ok():
    uid, bad_id, err_code = 1, -1, 42
    result = foobar(uid, bad_id)
    assert result == err_code
```

+++
@title[Readable: Noise]

### Reduce the noise

Delete meaningless lines. Burn the dead code with fire

```
def foobar(x, y):
    if y < 0:
        return 42
    return '%s,%s' % (x, y)

def test_foobar__commented_assert__ok():
    # assert foobar(-1,-1) == 43
    assert foobar(1,-1) == 42
```

+++
@title[Readable]

## Readable

- Clear name of test: Given, What, Expected. Make it descriptive
- Clear steps of test: Create, Configure, Act, Assert
- Don't do magic. Make it as simple as you can.
- Eliminate _broken windows_
- Treat tests code as the production one - with love
- Do review and refactoring of test code

---
@title[Thank you]

## Thank you!

Spread the word
`gitpitch.com/inesusvet/tests-talk`

ivan.styazhkin@datarobot.com

fb.com/ivan.nesusvet
