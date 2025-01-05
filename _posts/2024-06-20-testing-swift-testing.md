---
layout: post
permalink: testing-swift-testing
title: 'Testing Swift Testing'
tags: [swift, testing]
---

At WWDC24, Apple introduced Swift Testing, which is a new way to write tests in
Swift, practically replacing XCTest for unit tests. And it's _great_.

There are two sessions that give a great introduction to the new framework, and
I recommend checking them out:

- [Meet Swift Testing](https://developer.apple.com/videos/play/wwdc2024-10179)
- [Go further with Swift Testing](https://developer.apple.com/videos/play/wwdc2024-10195)

<!--more-->

# Introduction

Where with XCTest, we'd need setting up an `XCTestCase` subclass:

```swift
import XCTest

class MyTests: XCTestCase {
    func testFiltering() {
        // test here
    }
}
```

Using Swift Testing requires barely any set up at all. At it's simplest, we can
do the following:

```swift
import Testing

@Test func filtering() {
    // test here
}
```

And where with XCTest, we had a whole range of `XCTAssert*` functions, Swift
Testing has one "assert" to rule them all: `#expect` is built using the power
of macros, and is _nice_:

```swift
@Test func filtering() {
    let input = [1, 3, 2]
    let expected = [1, 2, 3]

    #expect(input.sorted() == expected)
}
```

If we'd have forgotten to call `.sorted()`, we'd get failure output:

```swift
@Test func filtering() {
    let input = [1, 3, 2]
    let expected = [1, 2, 3]

    #expect(input == expected)
    // Expectation failed: (input → [1, 3, 2]) == (expected → [1, 2, 3])
}
```

... which also works nicely with things that wouldn't get rich diagnostics in
XCTest, like `.contains`:

```swift
@Test func filtering() {
    let input = [1, 3, 2]

    #expect(input.contains(5))
    // Expectation failed: (input → [1, 3, 2]).contains(5)
}
```

# Beyond the basics

Swift Testing supports _parameterized testing_, allowing us to pass multiple
parameters to test at once, where before we'd need to define either individual
test functions or loop over a sequence. What's particularly neat about it is
that Xcode makes it so you can see exactly which parameter input may have
failed, and allows you to rerun that one input separately.

```swift
@Test(arguments: [1, 2, 3, 4])
func filtering(expected: Int) {
    let input = [1, 3, 2]

    #expect(input.contains(expected))
    // Expectation failed: (input → [1, 3, 2]).contains(expected → 4)
}
```

Which show up in the Test inspector as such:

![Test inspector showing the `filtering(expected:)` test with its four parameters. One through three are marked as passing, four as failing. All can individually be re-run.](./assets/blog-assets/parameterized-test-results.png)

# How do I..?

Some parts you might be familiar with in XCTest have different names under Swift
Testing, which may require a bit to get used to. Here's some:

```swift
let result = try XCTUnwrap(myOptional)

// becomes

let result = try #require(myOptional)
```

```swift
XCTFail("You shall not pass")

// becomes

Issue.record("You shall not pass")
```

```swift
XCTAssertTrue(
    true, 
    file: #file, // StaticString
    line: #line // UInt
)

// becomes

#expect(
    true,
    sourceLocation: SourceLocation(
        fileID: #fileID, // String
        filePath: #filePath, // String
        line: #line, // Int
        column: #column // Int
    )
)
```

```swift
XCTAssertEqual(1.0, 1.0, accuracy: 0.1)

// becomes... tricky. Apple recommends to use `isApproximatelyEqual()` from
// its `swift-numerics` package.
```

# Grouping tests

You can group tests with tags, part of the _trait_ system of Swift Testing.
You can do so across package boundaries, which makes it so you'll probably
want to create a package to define said tags, which you'd do like this:

```swift
extension Tag {
    @Tag static var subscriptions: Self
}
```

To apply this to a test or test suite, use

```swift
@Test(.tags(.subscriptions))
```

or in a group of tests

```swift
@Suite(.tags(.subscriptions))
struct Filtering {
    @Test func filter() {}
}
```

Tags show up in the test inspector, similar to what we saw before with
parameterized tests. They can be run from there, or inspected to see if a change
you made may have impacted related tests.

![Test inspector showing a "Tags" section showing one entry, "subscriptions".](./assets/blog-assets/test-tags.png)

# I don't / can't use Xcode 16 yet!

... bummer. That means you'll have to wait to start using Swift Testing,
although if you have the time, you could _use_ Xcode 16 to start converting
tests, and still merge them into your Xcode 15-branch like so:

```swift
#if compiler(>=6.0)
import Testing

@Test func filter() {}
#endif
```

... which may or may not be useful for your project. You can otherwise keep
things in a separate branch.

# Closing thoughts

Only having scratched the surface, it's _fun_ to write tests with Swift Testing.
I'd recommend you try it out and see how it can clean up some of your tests,
perhaps starting with those taking multiple arguments.

I'd recommend taking a look at the documentation on [migrating a test from
XCTest](https://developer.apple.com/documentation/testing/migratingfromxctest),
which mentions a bunch of great comparisons between the two frameworks that'll
help get you started.

Let me know how you get on!
