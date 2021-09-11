---
layout: post
permalink: advanced-interop
title: 'Dancing with Dinosaurs III: Advanced Interoperability'
tags: [swift, objc, interop]
---

- [Dancing with Dinosaurs I: Auditing Objective-C for Swift Interoperability](/objc-swift-interop-auditing)
- [Dancing with Dinosaurs II: Exposing your Swift types to Objective-C](there-and-back-again)
- Dancing with Dinosaurs III: Advanced Interoperability (this post)

---
<br />

# Header Documentation

- `///` supported in ObjC+Swift

- (out of scope?) https://pspdfkit.com/blog/2017/even-swiftier-objective-c/

- `NSNotification`s: live in class (i.e. `NSWindow.myNotification`), not `extension NSNotification.Name`
  - `extern NSNotificationName const myObjCSpeakingRateDidDecreaseNotification NS_SWIFT_NAME(SpeakingRate.didDecrease)`

# Async Await

- async? Even better if written in ObjC because auto-bridging?!

# Generic types
- Generics (`<__covariant ObjectType>`)
