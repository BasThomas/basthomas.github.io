---
layout: post
permalink: deprecating-workarounds
title: 'Deprecating Workarounds'
tags: [swift]
---

A [tip](https://twitter.com/olebegemann) shared by
[Ole Begemann](https://twitter.com/olebegemann/status/1294583027583123458) some
time ago to check if your workarounds in Swift have been fixed has been super
helpful.

With Swift, the compiler helps you out a bunch — why not use that to make sure
you check if certain bugs have been fixed in newer versions of the language or
the operating system?

Given the following workaround for an imaginary issue in iOS 14.4:

```swift
func workaroundForIssue(_ string: String) -> String {
    // Oh noes; "hello" does not return "world" in iOS 14.4!
    if #available(iOS 14.4, *), string == "hello" {
        return "world"
    } else {
        return string
    }
}
```

We'd want to check if this issue has been fixed in iOS 14.5 or later:

```swift
@available(iOS, deprecated: 14.5, message: """
If this returns 'world' as expected, please update the function to only include
the workaround in those versions that require it. If the issue has not been
resolved yet, please update the deprecated version to the next minor release.
""")
func workaroundForIssue(_ string: String) -> String {
    // Oh noes; "hello" does not return "world" in iOS 14.4!
    if #available(iOS 14.4, *), string == "hello" {
        return "world"
    } else {
        return string
    }
}
```

This will emit a compiler warning once you'd update to a version of Xcode that
supports iOS 14.5 or later.

... if it hasn't yet been fixed in that new version, make sure to update the
"deprecated" version, and don't forget to update your
[Feedback](https://feedbackassistant.apple.com) either ;-)

Assuming the issue _was_ fixed in that newer version, update the function to
only include the workaround in those versions that require it:

```swift
func workaroundForIssue(_ string: String) -> String {
    if #available(iOS 14.5, *) {
        return string
    } else if #available(iOS 14.4, *), string == "hello" {
        // Oh noes; "hello" does not return "world" in iOS 14.4!
        return "world"
    } else {
        return string
    }
}
```

Oh, and as [mentioned](https://twitter.com/steipete/status/1397126874116014082)
by [Peter Steinberger](https://twitter.com/steipete), you'll unfortunately not
be able to use this trick as-is for Objective-C; you'd have to work around the
issue in Swift, and expose that to Objective-C, which you may not always be
able to (or want to) do.
