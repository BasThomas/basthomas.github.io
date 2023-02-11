---
layout: post
permalink: cheating-the-system-for-fun-and-profit
title: 'Cheating the System for Fun and Profit'
tags: [accessibility]
---

Okay, that title might just be a little bit of clickbait. The idea is to use
macOS VoiceOver to navigate through Xcode's simulator, helping test your (iOS)
app without having to take out your iOS device... mostly. But we'll get to that.

<!--more-->

## macOS VoiceOver?! I've never used that!

So yeah, this trick is certainly a bit for power users. If you build apps for
iOS, there's a chance you're not familiar with VoiceOver on macOS — I wasn't
until joining the macOS Accessibility team at Apple. There's certainly a bit of
a learning curve, which people familiar with iOS VoiceOver will probably know.

System Settings (or System Preferences if you're not yet running macOS 13
Ventura) > Accessibility > VoiceOver > Open VoiceOver Training... is a good
place to start, but let's go over an even quicker quickstart.

### macOS VoiceOver quickstart

To turn on VoiceOver on macOS, given a keyboard with Touch ID, hold `⌘`,
then triple-press the Touch ID button. You can also ask Siri to turn it on or
off.

Then, as you can imagine, VoiceOver navigation is going to be quite different
from iOS. No touch screen, but a keyboard to interact with things. Oh, the
possibilities!

The first thing to know is there's the so called "VoiceOver modifier" keys. By
default that'll be either Caps Lock or `⌃`+`⌥`. I've gotten used to the latter.
To navigate, hold your modifier key(s), and use the arrow keys. That's like a
right (or left) swipe on iOS, and honestly, you're starting to get off to the
races. Try it out!

---
<br />

Activating things like buttons, as you might have guessed, is done with the
modifiers keys and the space bar.

That concludes our quickstart for now, let's get back to the (fun and) profit.

## (Fun and) profit

Macs with Apple's M-chips can run iOS apps on the Mac — which is practically
the same thing the simulator does, and how Catalyst apps work. And so we can
leverage this with the Simulator app, simply by navigating our macOS VoiceOver
cursor... to our iOS app!

![Navigating into the iOS app running on the simulator using macOS VoiceOver.](./assets/blog-assets/(into-ios.mov)

You can already tell the... awkward bits that come with this. No idea why the
VoiceOver cursor is off — it normally works okay, but apparently not in the
simplest of demo applications. Rest assured, things work. We can activate the
button, and we can verify VoiceOver labels and elements.

Voice Control also works, but the element position is out of whack there too,
by the looks of it.

![Navigating the iOS app running on the simulator using macOS Voice Control.](./assets/blog-assets/(voice-control-macos.mov)

What's neat about this, is that it gives us a little more options than with the
Accessibility Inspector, although as you can see, either are going to have some
tricky things to work around. Some things that the simulator can't do, but using
VoiceOver on macOS can, is to navigate and inspect `AXCustomContent`, for
example, or to perform the "Zorro gesture", or maybe more commonly known as the
"accessibility escape" by telling Voice Control to "go back".

### An `AXCustomContent` example

Alright, let me also give an example using custom content, as that is something
that you can't verify with the accessibility inspector. It also requires a tiny
bit more macOS VoiceOver magic. Let's take a look first:

![Showing custom content in an iOS app with macOS VoiceOver.](./assets/blog-assets/(custom-content.mov)

You might have been able to read along with the caption panel, but to show the
entries for custom content, use your VoiceOver modifier keys, plus `⌘`, plus
`/`. Ah, the power of macOS VoiceOver... perhaps that'll leave you wanting to
explore it further.

## With great power...

So all in all, I admit this is a rather niche way of testing your iOS apps for
accessibility. And beware, as, like (unfortunately) is also true for the
accessibility inspector, only an actual iOS device is going to give you the
exact experience your user has — macOS is, as we know, quite different from iOS,
and looking at the aforementioned iOS on Mac and Catalyst and how they work,
they will change certain behaviors compared to on iOS. So: always test on actual
iOS devices, too, and take those as your source of truth.

And oh yeah... I've just been reminded how lucky we are that the iOS screen
recording automatically includes Voice Control and VoiceOver sounds... which
is not true for macOS. Guess I'll add that to the pile of Feedbacks to file as
a result of writing this.

---
<br />

I'd love to hear from you if you've tried this out! 
