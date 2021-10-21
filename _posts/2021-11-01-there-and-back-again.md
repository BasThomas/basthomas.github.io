---
layout: post
permalink: there-and-back-again
title: 'Dancing with Dinosaurs II: Exposing your Swift types to Objective-C'
tags: [swift, objc, interop]
---

- [Dancing with Dinosaurs I: Auditing Objective-C for Swift Interoperability](/objc-swift-interop-auditing)
- Dancing with Dinosaurs II: Exposing your Swift types to Objective-C (this post)
- Dancing with Dinosaurs III: Advanced Interoperability (forthcoming)

---
<br />
When you have Objective-C in your codebase, and you made it to properly interoperable with Swift, you may want to write future code in Swift. Yet, you’re still dealing with that Objective-C code, meaning that in some cases, you need to go the opposite direction: write your code in Swift, and make it interoperable with Objective-C.

Like we’ve seen with Objective-C to Swift, a lot of this comes for free. This is the case for Swift to Objective-C, too, but keep in mind that with Swift’s expressiveness, some of it won’t (automatically) be available in Objective-C, like enums with strings as their raw values.

Another limitation of Objective-C is that value types can’t be nullable; something like a `CGRect`, for example, or `NSInteger` can be an optional in Swift… but not in Objective-C.

Alas, with great power in Swift, comes great responsibility briding it to Objective-C.

- enums
- generics
- swift-only types
- swift-first, then back (or the other way around? Some people feel that's easier)
