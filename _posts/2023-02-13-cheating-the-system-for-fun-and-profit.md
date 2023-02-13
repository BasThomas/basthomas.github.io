---
layout: post
permalink: cheating-the-system-for-fun-and-profit
title: 'Cheating the System for Fun and Profit (or how to use macOS Assistive Technologies to test in the Simulator)'
tags: [accessibility]
---

Let's take a look at how we can use macOS's assistive technologies, like
VoiceOver and Voice Control, as well as Hover Text, to more easily check some
accessibility in the simulator, without having to deal with the (shortcomings
of) the Accessibility Inspector. This will help you not having to always
immediately run your device on an iOS device to test it... mostly — the
experience on an iOS device should still be your source of truth because of
certain differences between the platforms, even within an iOS app on the Mac.
But we'll get to that.

<!--more-->

## Hover Text

Hover Text is a macOS feature that let's you view text at larger sizes,
typically using a modifier key and hovering over the text or element (hence the
name). What's neat about it, is that is will show you the accessibility label
of any element, not just text, including in the iOS simulator. Meaning Hover
Text can be a rather quick and frictionless way to get an idea of elements not
having an accessibility label, or having an awkward one. You can find Hover Text
under System Settings (or System Preferences if you're not yet running macOS 13
Ventura) > Accessibility > Zoom > Hover Text.

![Hover Text in the macOS system settings.](./assets/blog-assets/hover-text.png)

And this is what Hover Text looks like in practice, here as seen in the
simulator.

![Hover Text in the simulator.](./assets/blog-assets/hover-text-in-simulator.png)

A neat start, but let's take a look at macOS VoiceOver next...

## macOS VoiceOver?! I've never used that!

Using macOS VoiceOver to test your iOS app on the simulator is a bit of a power
user trick. If you build apps for iOS, there's a chance you're not familiar with
VoiceOver on macOS — I wasn't until joining the macOS Accessibility team at
Apple. There's a steep learning curve, which people familiar with iOS VoiceOver
will probably know.

System Settings > Accessibility > VoiceOver > Open VoiceOver Training... is a
good place to start, but let's go over an even quicker quickstart.

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

Activating things like buttons is done with the modifiers keys and the space
bar.

That concludes our quickstart for now, let's get back to the (fun and) profit.

## (Fun and) profit

Macs with Apple's M-chips can run iOS apps on the Mac — which is practically
the same thing the simulator does, and how Catalyst apps work. And so we can
leverage this with the Simulator app, simply by navigating our macOS VoiceOver
cursor... to our iOS app!

![Navigating into the iOS app running on the simulator using macOS VoiceOver.](./assets/blog-assets/into-ios.mov)

You can already tell the... awkward bits that come with this, underlining the
importance of treating iOS devices as the source of truth in all cases. I have
no idea why the VoiceOver cursor is off — it normally works okay, but
apparently not in the simplest of demo applications. Rest assured, things work.
We can activate the button, and we can verify VoiceOver labels and elements.

Voice Control also works, but the element position is out of whack there too,
by the looks of it.

![Navigating the iOS app running on the simulator using macOS Voice Control.](./assets/blog-assets/voice-control-macos.mov)

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

![Showing custom content in an iOS app with macOS VoiceOver.](./assets/blog-assets/custom-content.mov)

You might have been able to read along with the caption panel, but to show the
entries for custom content, use your VoiceOver modifier keys, plus `⌘`, plus
`/`. You can then navigate through them using just arrow the up and down arrow
keys, without any modifiers.

These menu-style actions are what are the equivalent of iOS Rotors. And so as
you might imagine, this only scratches the surface of the capabilities of just
rotors on macOS. Ah, the power of macOS VoiceOver... perhaps that'll leave you
wanting to explore it further. (Pro tip: the hints will help you gradually
explore more options and actions as you come across them, like custom actions
and custom content.)

## With great power...

Making use of the tools we have to help ourselves is something I really like
exploring. Sometimes things don't work out, other times we take away these
(little) things that can help our workflows and more. I've been using these
tricks to drastically improve my testing workflows... and to have a lot of fun!

Whilst these tricks are a niche way of testing your iOS apps for accessibility,
I feel they are great to know about and add to your toolbelt. But beware, as
like (unfortunately) is also true for the accessibility inspector, [only an
actual iOS device](/custom-tab-bar-accessibility) is going to give you the
exact experience your user has.

macOS is, as we know, quite different from iOS, and looking at the
aforementioned iOS on Mac and Catalyst and how they work, they will change
certain behaviors compared to on iOS. A major example is navigation: whilst on
iOS we have a (mostly) flat structure, macOS knows a rich, hierarchical
structure. What does that mean, you might wonder? Well, containers like table
and collection views are to be drilled into. Where on iOS you'll navigate a
table view cell by cell (unless you have a (custom) rotor), on macOS you'll be
drilling into it using the VoiceOver keys plus down arrow (to enter), and up
arrow (to exit).

So: always test on actual iOS devices, too, and take those as your source of
truth.

And oh yeah... I've just been reminded how lucky we are that the iOS screen
recording automatically includes Voice Control and VoiceOver sounds... which
is not true for macOS. Guess I'll add that to the pile of Feedbacks to file as
a result of writing this.

---
<br />

I'd love to hear from you if you've tried this out!

_Special thanks to Chris Wu, Nathan Tannar and Rob Whitaker for their
proofreading and feedback!_
