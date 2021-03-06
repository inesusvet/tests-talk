@title[Introduction]

## The test which will save your day

#### by Ivan Styazhkin @ DataRobot

+++
@title[Disclaimer]

## I am not selling
- No particular technology or methodology
- I want to talk about very basic principles
- This is based on my experience and knowledge
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
- You know hard way that tests are good for your project
- You do read & write tests on daily basis
- You will think ahead about testability of new system

+++
@title[References]

## Further reading & watching
- "The art of unit testing" by Roy Osherove
- "Extreme programming: TDD by example" by Kent Beck
- "JB Rainsberger Integrated Tests Are A Scam" @youtube
- "Clean Code" video series by Robert C. Martin

---
@title[5 whys]

# Why do we need tests?

+++
@title[5 because]

## We need tests
- To detect program errors faster
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
@title[Readable: Clear name]

### Not so clear name

It's the very first thing which will see a developer

```
def build_cookie(name, value):
    return '%s=%s' % (name, value)

def test_test():
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
def build_cookie(name, value):
    return '%s=%s' % (name, value)

def test_build_cookie__regular_name_valid_value__ok():
    name, valid_session = 'sessionid', 1001
    assert build_cookie(name, valid_session) == 'sessionid=1001'
```

+++
@title[Readable: Not so clear steps]

### Not so clear steps

What's mocked here? Gimme answer in 5 seconds!

```
def test_foobar__false_result__ok():
    result = authorize('vasya', 'superstrongpassword')
    assert result.is_ok
    patch('requests.post').start()
    result.send('token')
    assert result.sessionid == ‘token’
```

+++
@title[Readable: Clear steps]

### Clear steps

Save your time

```
def test_authorize__bad_user_and_password__not_authorized():
    setup_auth_ok(‘failed’)

    result = authorize('bad-user’, 'bad-password')

    assert result is False
```

+++
@title[Readable: Magic]

### Magic

No one can understand that.
Only one person can debug it.
This person can't afford vacations

```
def test_foobar__watch_my_magic__ok():
    assert foobar(42) == '3,141529'
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

def test_authorize__bad_session_id_for_a_user__error():
    uid, bad_session, err_code = 1, -1, 42
    result = authorize(uid, bad_session)
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
    # assert foobar(-1, -1) == 43
    assert foobar(1, -1) == 42
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
@title[Maintainable: Helpers]

### Use helpers, Luke

Be a lazy developer

```
def test_auth__app_failed__error():
    message = ‘not good’
    setup_error(message)

    response = authorize('username', 'badpassword')

    assert_error(response, message)
```

+++
@title[Maintainable: Atomic]

### Not so atomic test

Why this test poorly maintainable?

```
def geo_proxy(x, y):
    return requests.post(
        ‘/geo’, data={‘x’: x, ‘y’: y})

def test_geo_proxy__not_atomic__ok(requests_mock, db_mock):
    resp = geo_proxy(1, 2)

    requests_mock.post.assert_called_once_with(
        '/geo', data={'x': 1, 'y': 2})
    assert db_mock.query.call_count == 1
```

+++
@title[Maintainable: Atomic]

### Atomic test

Check only one aspect of work at once

```
def geo_proxy(x, y):
    return requests.post(
        ‘/geo’, data={‘x’: x, ‘y’: y})

def test_geo_proxy__atomic__ok(requests_mock):
    resp = geo_proxy(1, 2)

    requests_mock.post.assert_called_once_with(
        '/geo', data={'x': 1, 'y': 2})
```

+++
@title[Maintainable: Many assertions]

### Many assertions

Don't be a lazy developer

```
def build_session_cookie(x, y):
    return 42 if y > 0 else '%s=%s' % (x, y)

def test_build_session_cookie__check_all_options__ok():
    assert build_session_cookie('uid', 1) == 42
    assert build_session_cookie('sessionid', -1) == 'sessionid=-1'
```

+++
@title[Maintainable: Less assertions]

### Less assertions

Make it simple & clear

```
def validate_sessionid(username, value):
    return 42 if value < 0 else (hash(username) == value)

def test_validate_sessionid__negative_session__special_value():
    assert validate_sessionid('admin', -1) == 42

def test_foobar__positive_valid_session__ok():
    assert validate_sessionid('user', 6468734353329665493) == True
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
@title[Trustworthy: Logic is bad for you]

### Logic

Why this test isn't trustworthy?

```
def build_cookie(name, value):
    return '%s=%s' % (name, value)

def test_build_cookie__replay_logic__ok():
    result = build_cookie('uid', 123456)
    assert result == '%s=%s' % ('uid', 123456)
```

+++
@title[Trustworthy: Randoms often doesn't help]

### Random logic

Why this change don't make things better?

```
from random import randint, sample
from string import ascii_letters

def build_cookie(name, value):
    return '%s=%s' % (name, value)

def test_build_cookie__random_logic__ok():
    name, value = sample(ascii_letters, 10), randint(0, 10)
    result = build_cookie(name, value)
    assert result == '%s=%s' % (name, value)
```

It is not a _fuzzy testing_. It's brittle test

+++
@title[Trustworthy: Blackbox]

### Blackbox

Check system's output with known input often quite simple

```
def validate_new_phone(user_id, new_phone):
    if not re.match(RE_VALID_PHONE_NUMBER_FORMAT, new_phone):
        return False
    return Blackbox.validate_phone_number(user_id, new_phone)

def test_check_phone__regular_user__ok():
    uid = 1
    setup_user(uid)

    assert validate_new_phone(uid, '+79998005475') is True
```

Even if system isn't simple

+++
@title[Trustworthy: Whitebox]

### Whitebox

Check internals often lead to brittle tests

```
def validate_new_phone(user_id, new_phone):
    if not re.match(RE_VALID_PHONE_NUMBER_FORMAT, new_phone):
        return False
    return Blackbox.validate_phone_number(user_id, new_phone)

def test_check_phone__regular_user__ok():
    uid, phone_number = 1, '+79998005475'
    setup_user(uid)
    blackbox_mock = setup_blackbox_mock()

    assert validate_new_phone(uid, phone_number) is True
    assert blackbox_mock.called_with(
        method='GET', uid=uid, body={
            'phone_number': phone_number},
    )
```

+++
@title[Trustworthy: What the coverage means?]

### "Good coverage"

Why this test isn't trustworthy?

```
def get_full_username(first_name, last_name):
    return '%s %s' % (first_name, last_name)

def test_get_full_username__good_coverage__ok():
    assert get_full_username('Snoop', 'Dogg')
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
@title[Thank you]

## Thank you!

Spread the word
`gitpitch.com/inesusvet/tests-talk`

ivan.styazhkin@datarobot.com

fb.com/ivan.nesusvet
