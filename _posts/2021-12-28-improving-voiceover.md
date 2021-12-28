---
layout: post
permalink: improving-voiceover
title: 'Improving Accessibility: VoiceOver'
tags: [swift, accessibility, voiceover]
---

Now that you've gotten some experience with VoiceOver, and made some
improvements to make your app more accessible with VoiceOver, let's now take a
look at what's still missing, and the extra steps you can take.

<!--more-->

We'll take a look at `accessibilityValue`s, `accessibilityHint`s,
`accessibilityPerformEscape()`, `accessibilityCustomAction`s, _and_
`accessibilityCustomContent`. Quite a bit to get through, so let's get started!

---
<br />
- [Getting Started With Accessibility: VoiceOver](/getting-started-voiceover)
- Improving Accessibility: VoiceOver (this post)

## Accessibility Values

Where previously we talked about accessibility labels, there's also the concept
of accessibility _values_. From [the documentation](https://developer.apple.com/documentation/uikit/uiaccessibilityelement/1619583-accessibilityvalue):

> The value is a localized string that contains the current value of an element.
For example, the value of a slider might be 9.5 or 35% and the value of a text
field is the text it contains.

While I think these are two great examples, it may be hard to wrap your head
around what should (and shouldn't) go into the `accessibilityValue`. Note,
though, that it is more than OK (and very likely) for it to be empty. Plus,
it describes a value _of_ the element. So it wouldn't make too much sense
to put in some data of a complicated cell, for example.

Going back to the example of a tweet, it wouldn't make sense to put in, for
example, the amount of likes; this is not a value of the element.

## Accessibility Hints

Accessibility hints are, as they say on the tin, hints. There's two important
things to note about them: one is that they should never contain crucial
information, as a user can turn them off in their settings.

Secondly, there are some guidelines on their contents, as per [the
documentation](https://developer.apple.com/documentation/uikit/uiaccessibilityelement/1619585-accessibilityhint):

> The hint is a brief, localized description of the result of performing an
action on the element without identifying the element or the action. For
example, the hint for a table row that contains an email message might be
“Selects the message,” but not “Tap this row to select the message.”

Particularly, while this documentation doesn't go into great detail, the "tap"
terminology is something to avoid — for accessibility hints and, really, any
other strings alike. Users may not be interacting with your application by
tapping; imagine a Catalyst app running on the Mac, for example, or an iOS app
running on the Mac... or on an iPad with a hardware keyboard with trackpad.
Even for Voice Control this becomes awkward... but we'll talk about that more
in a following post.

## Performing Escapes

VoiceOver has the concept of performing an "escape", and is a way to dismiss
the currently shown view — where sensible. Think a modal view, or popping a
view in a navigation stack.

The system does a great job of giving you this for free when, for example,
using `UINavigationController`. But it'd be good to audit your application and
check if it is possible to perform these escapes where you expect them to —
especially around custom (modally displayed) views.

You may be wondering how a user can perform this escape, though? And thus, how
you can test it? Well...

![Perform an Accessibility Escape on iPhone](/assets/blog-assets/perform-escape.mp4)

With two fingers, draw a "Z" on the screen: the [Zorro gesture](/assets/blog-assets/zorro.gif),
as I like to call it.

In those views that require adding support for escapes, override [`accessibilityPerformEscape()`](https://developer.apple.com/documentation/objectivec/nsobject/1615091-accessibilityperformescape),
implement your dismissal, and return if it succeeded, like so:

```swift
override func accessibilityPerformEscape() {
    dismiss()
    return true
}
```

## Custom Actions

Let's go back to imagining a tweet. It has replies, retweets, and likes; those
are (already) three actions within one element; one cell. When directly
interacting with it (outside of VoiceOver), we can activate those buttons as
we would with any other button.

But in VoiceOver, while we could expose all of these buttons as separate
elements, that would add a lot of elements to swipe through, and with that,
possibly quite a lot of clutter. It's unlikely you'll want to reply to, retweet,
and like every tweet you navigate to. So what can we do? Meet custom actions.

Custom actions let you add, well, custom actions to accessibility elements.
Actually, there's a few places where the system does this automatically already,
such as with `UISwipeActionsConfiguration`s.

To use actions, you'll first need to get acquainted with the rotor, which is
being used in the following video.

[![How to organize apps with the VoiceOver rotor on your iPhone and iPad](http://img.youtube.com/vi/w2Ds-I2L6PI/0.jpg)](https://www.youtube.com/watch?v=w2Ds-I2L6PI "How to organize apps with the VoiceOver rotor on your iPhone and iPad")

Now, knowing how to use the rotor "knob", navigate to an app with those swipe
actions, like Mail.app, Notes.app, or perhaps your own app. If turned on in
VoiceOver settings, note VoiceOver will say "Actions available" for a mail in
Mail.app, a note in Notes.app, and when in the Actions rotor, swiping up or
down with one finger will guide you through the list of custom actions. From
there, you can double tap to activate it.

To add your own custom actions, like in our example with replying, retweeting,
and liking, use [`UIAccessibilityCustomAction`](https://developer.apple.com/documentation/uikit/uiaccessibilitycustomaction).

```swift
let tweetCell = TweetCell()
tweetCell.accessibilityCustomActions = [
    .init(
        name: NSLocalizedString("Reply", comment: ""),
        image: replyImage
    ) { action in
        reply()
        return true
    },
    // don't forget to update the name of this action when it changes;
    // i.e. "Undo retweet"
    .init(
        name: NSLocalizedString("Retweet", comment: ""),
        image: retweetImage
    ) { action in
        showRetweetOptions()
        return true
    },
    // don't forget to update the name of this action when it changes;
    // i.e. "Undo like"
    .init(
        name: NSLocalizedString("Like", comment: ""),
        image: likeImage
    ) { action in
        toggleLike()
        return true
    }
]
```

Note that we pass in an `image`. You may wonder why. Which, if you did wonder,
is a great observation. This would be something that is interesting to other
assistive technologies, like Switch Control. For more information on Switch
Control, I'd highly encourage you to watch [this WWDC session](https://developer.apple.com/videos/play/wwdc2020/10019/),
which, in fact, introduces this exact addition to the custom action API!

## Custom Content

Finally, let's talk custom content. Remember the advice to keep your labels
short, even if that would mean you'd leave out some information?

To prevent long accessibility labels, and to make information accessible in a
more piecemeal fashion, Custom Content was introduced with iOS 14. Let's take a
look at [its documentation](https://developer.apple.com/documentation/accessibility/axcustomcontent):

> An `AXCustomContent` object contains the accessibility strings for the labels
you apply to your accessibility content [..] to allow your users to experience
the content in a more appropriate manner for each assistive technology.

While significantly improving the VoiceOver experience for users, this API is,
in my opinion, rather awkward to work with, requiring the addition of quite
some (unnecessary?) complicated code. While we've seen APIs like
`accessibilityCustomActions`, being available on an(y) `NSObject`, this is not
exactly the case for `AXCustomContent`.

Not only does `AXCustomContent` live in its own framework, `Accessibility`, it
comes with a protocol that needs to be implemented, and some awkward baggage
from it being written in Objective-C, and arguably not bridged to Swift in the
best way possible.

I unfortunately don't know the reasons behind this — all I know is that it
hurts the developer, and I really, _really_ wish it wouldn't have had to.

Because why can't we have

```swift
let tweetCell = TweetCell()
tweetCell.accessibilityCustomContent = [
    .init(
        label: NSLocalizedString("Replies", comment: ""),
        value: String(describing: 30)
    ),
    .init(
        label: NSLocalizedString("Retweets", comment: ""),
        value: String(describing: 9)
    ),
    .init(
        label: NSLocalizedString("Likes", comment: ""),
        value: String(describing: 95)
    )
]
```

... which neatly mirrors the `accessibilityCustomActions` API, but instead have
to go through the following hoops:

```swift
import Accessibility

// Note this is (and has to be) a class, as `AXCustomContentProvider` inherits
// from `NSObjectProtocol`?!
class MyObjectContainingTweetCell: AXCustomContentProvider {
    // Required for storage, as we won't necessarily have all information to
    // compute the custom content when initializing.
    var _accessibilityCustomContent: [AXCustomContent] = []

    // Note this is an implicitly unwrapped optional, as this "supports"
    // `null_resettable` — meaning setting it to `nil` means it internally
    // is expected to (but not guaranteed by the compiler) set itself to `[]`
    var accessibilityCustomContent: [AXCustomContent]! {
        get { _accessibilityCustomContent }
        set { _accessibilityCustomContent = newValue }
    }

    func setupTweetCell(with tweet: Tweet) {
        accessibilityCustomContent = [
            .init(
                label: NSLocalizedString("Replies", comment: ""),
                value: String(describing: tweet.replies.count)
            ),
            .init(
                label: NSLocalizedString("Retweets", comment: ""),
                value: String(describing: tweet.retweets.count)
            ),
            .init(
                label: NSLocalizedString("Likes", comment: ""),
                value: String(describing: tweet.likes.count)
            )
        ]
    }
}
```

... from importing another framework, to requiring classes (although one could
still store the information in, say, a `ViewModel` that is a struct, and pass
it along to the view that is a class), having to deal with an awkward
`null_resettable`, as well as backing storage. _Woof_.

---
<br />
And then there's one more note: the `AXCustomContent` API has a notion of
_importance_. By default, content has an importance of `.default`, and it can
be set to `.important`. I would recommend against using this. What it does is it
will add the _value_ (or values in case you mark multiple custom content items
as important) to the end of your initial VoiceOver output — i.e. after the
label and value.

This can (more often than not) lead to information missing — as
with the tweets, for example, say likes are important, "95" itself doesn't give
enough information as to what this is about. So, if there's important
information, consider making it part of your `accessibilityLabel` instead.

Alas. The good news: like we've seen before with custom actions, users can now
use the rotor to navigate to custom content, then swiping up or down with one
finger to go through this information.

If you want to try out how this works, consider checking out Photos.app — it
uses custom content to give a bunch more information about pictures. And as
with actions — depending on your settings — the system will indicate more
content is available.

For more on custom content, check out Rob Whitaker's [blog post](https://mobilea11y.com/blog/custom-accessibility-content/)
on the topic.

## Conclusion

While after reading all of this (and perhaps applying some of it in your app),
this all might feel like a lot of work. But take a step back, and appreciate
how a lot of the _what_ to do to make your app accessible is, arguably, part
of the design.

Try to take this VoiceOver-mindset to this new feature, or new
design, and think: what are the actions here? Are we using any images to
represent actions? If so, what do these represent, in words? How can we split up
information, making use of custom content?

And with that, you're halfway there already... now the (other) fun part of
implementing it is a lot clearer, and part of the design rather than an
afterthought. Heck — I would be surprised if this doesn't affect your initial
design at all, perhaps making small tweaks that benefit everyone.

Let me know your thoughts on this post, and if you have any questions,
I'd love to help!

---
<br />
- [Getting Started With Accessibility: VoiceOver](/getting-started-voiceover)
- Improving Accessibility: VoiceOver (this post)
