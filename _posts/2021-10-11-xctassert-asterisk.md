---
layout: post
permalink: xctassert-asterisk
title: 'XCTAssert*'
tags: [swift, testing]
---

Over the years, I've gotten more and more of an appreciation for tests. If you
ask me, there's not a better feeling than having built something with tests,
finding a bug or issue, and being able to write out that faulty scenario in a
test (and having it fail), to then fix the issue and having that (since
unmodified) test pass.

Writing code for Apple platforms, the default (and pretty snazzy!) framework
for testing is XCTest. There are some amazing people working on it, and it has
seen some great improvements over the last few major releases, like throwing
tests, `setUpWithError`, `XCTUnwrap`, etcetera. The team really seems to keep
up well with new (Swift) features, like async/await, making sure that writing
tests is as fun as possible, while also being as understandable as possible.

On that note of understandability: treat your test code as if it were production
code; or at least close to it. If your tests are hard to understand, their
value will eventually be impacted.

One of the things to keep in mind to keep your tests understandable, is their
names. I prefer making them very explicit, to the extent that I introduce
snake-case in them (you read that right!). So, `test_thatFunctionDoesNotThrow`
instead of `testThrowingFunction`, that is more ambiguous.

The ["given, when, then"](https://en.wikipedia.org/wiki/Given-When-Then)
strategy can also greatly help understand (and break up) your tests.

# Assertions

Perhaps even more important than the things mentioned above, is to have
understandable error messages. Your tests will eventually (or more often) fail;
whether in the above example of adding a test for a bug you found, when
refactoring. While the test's structure can help you understand _what_ is being
tested, the most immediate starting point (and thus, arguably, one of the most
important things) is the error message(s) your test produces.

Surely we've seen these alerts indicating "something went wrong". Well, duh!
But what? Luckily, XCTest has a whole set up assertion functions that each
cater to specific, well, assertions, producing helpful and understandable error
messages.

> Note that all tests in this post fail; this is on purpose, so that we can
show and inspect their failure messages!

## Boolean Assertions

The most basic set of assertion functions test booleans. Understandable, as for
any tests, we'll be wanting to verify something _against_ something else. So,
technically, all assertions are boolean assertions. Let's take a look at this
most basic set of assertions in XCTest:

```swift
XCTAssert(false)
// XCTAssertTrue failed

XCTAssertTrue(false)
// XCTAssertTrue failed

XCTAssertFalse(true)
// XCTAssertFalse failed
```

As we can see, these basic assertions come with basic failure messages; simply
because there is not much more information to present.

Having said that, note that every assertion function comes with an optional
parameter `message`, where you can pass a `String` further explaining the
failure:

```swift
struct Engine {
    var isStarted = true // Oops!

    mutating func start() {
        isStarted = true
    }

    mutating func stop() {
        isStarted = false
    }
}

let engine = Engine()
XCTAssertFalse(
    engine.isStarted,
    "The engine isn't expected to have been started yet!"
)
// XCTAssertFalse failed - The engine isn't expected to have been started yet!
```

Having these more specific assertion functions that provide better, more
insightful failure messages, however, will render a bunch of the manual
messages less useful; which is great, as they are arguably a "weak" point in
the test. Imagine changing a test, but forgetting to update the message... that
could quickly become confusing, with all the consequences that may entail.

## Nil Assertions

As we'll dive into further here, we will see how specific assertion functions
become more and more useful, given that we pass them more information to work
with. `nil` isn't that useful for XCTest just yet; it could pretty much be
compared with "true" or "false", but does become a little more useful:

```swift
var myString: String?
myString = "Hello"
XCTAssertNil(myString)
// XCTAssertNil failed: "Hello"

myString = nil
XCTAssertNotNil(myString)
// XCTAssertNotNil failed

_ = try XCTUnwrap(myString)
// XCTUnwrap failed: expected non-nil value of type "String"
```

As we can see, these provide a little more information than just "assertion
failed". Perhaps their error messages could benefit from being a tad more
verbose; I think the second failure message would be easier to parse when it'd
have been the same as `XCTUnwrap`'s failure message. (FB9681950)

While not being used here, `XCTUnwrap` is a particularly neat addition to
XCTest, introduced in Xcode 11. Where before we'd have to manually verify
something would be non-nil, then force unwrap it, now this is "baked into" this
assertion, which will return the unwrapped value if present, and otherwise, as
we can see above, throw an error.

## Equality Assertions

Equality assertions are pretty much just "assertions". As I mentioned earlier,
when we _assert_, we verify _x_ against _y_, and thus, arguably, their
equality. We could "rewrite" the most basic assert:

```swift
XCTAssertEqual(false, true)
// XCTAssertEqual failed: ("false") is not equal to ("true")
```

... and we can see how this impacts the failure message. Anyway, on to some
more descriptive examples:

```swift
var myString: String?
let myOtherString = "Hello"

XCTAssertEqual(myString, myOtherString)
// XCTAssertEqual failed: ("nil")
// is not equal to ("Optional("Hello")")

myString = "Hello"
XCTAssertNotEqual(myString, myOtherString)
// XCTAssertNotEqual failed: ("Optional("Hello")")
// is equal to ("Optional("Hello")")

let myObject = NSDate(timeIntervalSince1970: 10)
let myOtherObject = NSDate(timeIntervalSince1970: 0)

XCTAssertIdentical(myObject, myOtherObject)
// XCTAssertIdentical failed: ("1970-01-01 00:00:10 +0000")
// is not identical to ("1970-01-01 00:00:00 +0000")

XCTAssertNotIdentical(myObject, myObject)
// XCTAssertNotIdentical failed: ("1970-01-01 00:00:10 +0000")
// is identical to ("1970-01-01 00:00:10 +0000")

let percentage = 0.333
let otherPercentage = 0.666

XCTAssertEqual(percentage, otherPercentage, accuracy: 0.1)
// XCTAssertEqualWithAccuracy failed: ("0.333")
// is not equal to ("0.666") +/- ("0.1")

XCTAssertNotEqual(percentage, percentage, accuracy: 0.3)
// XCTAssertNotEqualWithAccuracy failed: ("0.333")
// is equal to ("0.333") +/- ("0.3")
```

## Comparable Assertions

There's equality, and there's comparability. Let's take a look at some of the
examples of the latter below.

```swift
XCTAssertGreaterThan(1, 1)
// XCTAssertGreaterThan failed: ("1") is not greater than ("1")

XCTAssertGreaterThanOrEqual(0, 1)
// XCTAssertGreaterThanOrEqual failed: ("0") is less than ("1")

XCTAssertLessThan(1, 1)
// XCTAssertLessThan failed: ("1") is not less than ("1")

XCTAssertLessThanOrEqual(1, 0)
// XCTAssertLessThanOrEqual failed: ("1") is greater than ("0")
```

I'm not sure if I love how "Objective-C like" XCTest is here in its function
names. Instead of `XCTAssertGreaterThan(1, 1)`, I could imagine an
`XCTAssert(1, greaterThan: 1)` be more readable. The same could apply to
equality, actually. Alas.

## Error Assertions

```swift
struct MyError: Error {}
func throwingFunc(shouldThrow: Bool) throws {
    if shouldThrow {
        throw MyError()
    }
}

try XCTAssertThrowsError(throwingFunc(shouldThrow: false))
// XCTAssertThrowsError failed: did not throw an error

try XCTAssertNoThrow(throwingFunc(shouldThrow: true))
// XCTAssertNoThrow failed: threw error "MyError()"
```

Note that these assertions, like `XCTUnwrap` above, are throwing (and thus
prefixed with `try`). There's no need to wrap these expressions themselves in
a `do/catch` block; instead, the test function itself can be marked `throws`,
making the code less distracting (and prevent too much indentation). Neat!

## Unconditional Assertions

Sometimes, you will need to unconditionally fail a test. The setup is not
possible, for example.

```swift
XCTFail() // failed
```

To the point.

## Expected Failure Assertions

From time to time, a failure may be expected, or something that can't (or
shouldn't) be fixed in a current patch. In Xcode 12.5, it is now possible to
expect these kind of failures, and better reason about them (as well as having
better diagnostics).

```swift
XCTExpectFailure { // Expected failure but none recorded
    XCTAssertFalse(false)
}

XCTExpectFailure {
    XCTAssertFalse(false)
    // Expected failure: XCTAssertFalse failed
}

let options = XCTExpectedFailure.Options()
options.issueMatcher = { issue in
    issue.compactDescription.contains("Hello")
}

XCTExpectFailure(options: options) { // Expected failure but none recorded
    XCTAssertFalse(false, "Hello")
}

XCTExpectFailure(options: options) {
    XCTAssertFalse(true, "Hello")
    // Expected failure: XCTAssertFalse failed - Hello
}
```

I'd be weary of expected failures with complex issue matching. The last example
here, I think, already adds additional overhead that may make things more
complex than they need to be.

## Skipping Assertions

Whilst expected failures can be used when things are (temporarily) expected to
not pass, we can skip assertions entirely, too. We may want to do this, for
example, if a feature isn't implemented on a specific platform.

```swift
try XCTSkipIf(true)
// Test skipped
```

```swift
try XCTSkipUnless(false)
// Test skipped
```

Note also that with expected failures, assertions are still ran. Assertions
below a "skip" function, are entirely skipped, as per the name, so take extra
precaution not to write false-positive expressions within, potentially skipping
tests when you didn't intend to.

---
<br />
I hope this overview gave you some insights into XCTest's various assertion
functions, and how they can help make your test failures more understandable,
especially at a glance.

Perhaps you can adopt `XCTUnwrap` in places previously using `XCTAssertNotNil`
and sequential unwrapping. Or perhaps your assertions exclusively rely on
`XCTAssert()`? Let's hope not, but in that case, you're going to be able to
make some awesome improvements to your test code.
