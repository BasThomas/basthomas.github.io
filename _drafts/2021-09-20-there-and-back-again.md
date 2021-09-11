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

While in Objective-C, value types like `NSInteger` can't be nullable (or enums,
for example), in Swift, they can be. This means this can't be bridged back into
ObjC

with great power...

- enums
- generics
- swift-only types
- swift-first, then back (or the other way around? Some people feel that's
easier)
