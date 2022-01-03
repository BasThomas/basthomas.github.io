---
layout: post
permalink: getting-started-voiceover
title: 'Getting Started With Accessibility: VoiceOver'
tags: [swift, accessibility, voiceover]
---

I've received quite a few questions along the lines of "I'm a developer/quality
assurance engineer / ... and am interested in accessibility; where do I start?"

I wanted to write some thoughts on this topic. For one, because I think it's
others than the people that have reached out to me would be interested in, and
to have a reference for those reaching out.

<!--more-->

More specifically, what I want to look at is not "what APIs are there to help
make an app accessible?", but rather "how do I use the available APIs to make
my app accessible as best as possible?". For the former, I highly, highly
recommend [Rob Whitaker](https://twitter.com/RobRWAPP)'s blog,
[Mobile A11y](https://mobilea11y.com), as well as the [Developing Inclusive
Mobile Apps](https://link.springer.com/book/10.1007/978-1-4842-5814-9) book he
wrote, which covers both iOS & Android, which can give another layer of both
appreciation and insights into accessibility.

---
<br />
- Getting Started With Accessibility: VoiceOver (this post)
- [Improving Accessibility: VoiceOver](/improving-voiceover)
- [Verifying VoiceOver: Accessibility Inspector](/verifying-voiceover)
- [Improving Accessibility: Voice Control](/improving-voice-control)
- [Getting Started With Accessibility: Dynamic Type](/getting-started-dynamic-type)

## VoiceOver

In this post, I want to look at — what I would say — one of the three major
assistive technologies you may want to pay attention to on Apple platforms.
There's Dynamic Type, VoiceOver and Voice Control.

So you're interested in improving the VoiceOver experience for your users.
VoiceOver is Apple's screen reader, which also comes with support for
[Braille](https://en.wikipedia.org/wiki/Braille).

Where do we start? Probably the best way to start is to get a feel of the
technology by using it yourself. As neatly put into words by
[Tommy Edison](https://www.youtube.com/channel/UCld5SlwHrXgAYRE83WJOPCw):

> Try and use accessibility on your phone or computer for a day. When you get to
experience some of the frustrations of a site that's not done properly or an app
that doesn't work for you.

Also — I highly, highly recommend taking a look at Tommy's YouTube channel to
get a better understanding of how users rely on VoiceOver (and much more).

---
<br />
The easiest way to turn VoiceOver on (and off again), would probably be through
Siri: "Hey Siri, turn on VoiceOver" will get you off to the races. What's
trickier is knowing how to navigate in VoiceOver. Apple Support made a great
introduction video, that, honestly, is a must watch:

[![How to navigate your iPhone or iPad with VoiceOver](http://img.youtube.com/vi/qDm7GiKra28/0.jpg)](https://www.youtube.com/watch?v=qDm7GiKra28 "How to navigate your iPhone or iPad with VoiceOver")

Now that you've seen some basic usage of VoiceOver — moving forward (with
swipe-right), backward (with swipe-left), and activating items (by
double-tapping), try it out in your own app.

### Making _Your_ App Accessible with VoiceOver

A great place to start making your app more accessible is here, with VoiceOver.
It is not unlikely you'll be navigating past items that will be announced as
nothing else than "button" to VoiceOver. Or, perhaps, you'll navigate to
elements that are decorative, and do not add (but rather distract) from the
VoiceOver experience. You'll improve this using [`accessibilityLabel`](https://developer.apple.com/documentation/objectivec/nsobject/1615181-accessibilitylabel/),
which we'll get to in just a second.

Then, perhaps, your actionable cells aren't announced as buttons to VoiceOver,
your images aren't announced as such, or a header isn't, well, a header.
This is another crucial VoiceOver need — both as information for the user,
but also the system... so it can do some really neat work under the hood.

For this information, rather than adding this to your label, you'll be wanting
to use [`accessibilityTraits`](https://developer.apple.com/documentation/uikit/uiaccessibility/uiaccessibilitytraits) —
ranging from the aforementioned `.button`, `.image`, `.header`, to `selected`,
`.notEnabled`, and `.adjustable`.

Take another pass through your app, or another (system app), and note these
traits being part of the respective elements.

---
<br />
So now the task is for you to go through these elements that aren't accessible
as expected — adding `accessibilityLabel`s, as well as `accessibilityTraits`.

Note that the latter is an `OptionSet` — an element can have multiple traits.
APIs like UIKit and SwiftUI often give great defaults, so you'll (most often)
want to _insert_ new traits, rather than override the traits completely.

```swift
myCustomControl.accessibilityTraits.insert(.button)
// rather than
myCustomControl.accessibilityTraits = .button
```

For your labels, try to keep these short. At a later stage, we'll be adding
more information to elements, but not necessarily in the form of a label.

VoiceOver users navigate the app mainly based on these labels, and if they are
very long, or contain a lot of information, that makes an app a lot harder to
parse and use. For a tweet, for example, the label might include the author
and the tweet itself, but exclude the amount of retweets and likes.

## Conclusion

There's a lot more to learn about VoiceOver, and a bunch more you can do to
further enhance the experience; we're only partially there.

Yet, you've learned a thing or two about using VoiceOver, navigated your app
using the technology, and perhaps made some improvements using labels and
traits. For now, try to use your app with these improvements in place and ask
yourself: what information is missing for these users? How _would_ I express
this information to them?

Then, we'll take a look at custom actions, custom content, performing escapes,
hints, and values, next.

Let me know your thoughts on this introduction, and if you have any questions,
I'd love to help!

---
<br />
- Getting Started With Accessibility: VoiceOver (this post)
- [Improving Accessibility: VoiceOver](/improving-voiceover)
- [Verifying VoiceOver: Accessibility Inspector](/verifying-voiceover)
- [Improving Accessibility: Voice Control](/improving-voice-control)
- [Getting Started With Accessibility: Dynamic Type](/getting-started-dynamic-type)
