---
layout: post
permalink: optimizing-assistive-technology
title: 'Optimizing for VoiceOver and Voice Control'
tags: [swift, accessibility]
---

I've [spoken about the layering system in accessibility](https://www.youtube.com/watch?v=-RVvjDUhUA0), 
with VoiceOver support being a good first step toward supporting Voice Control,
and Voice Control in turn being a good start in supporting Full Keyboard Access.

I stand by this! Things like labels, traits and values get you most of the way
there for Voice Control. We optimize for Voice Control (and Full Keyboard
Access) using input labels, and then we can further optimize Full Keyboard
Access verifying our keyboard support.

But with this great power comes great responsibility...

<!--more-->

A while ago, I was building a component to allow users to participate in a
survey, and it was an example where I ran into issues trying to optimize for
both VoiceOver and Voice Control, _because_ the two systems lean on each other.

# Building a survey component

Based on certain conditions, we'd show a cell that would show a title, a
description, and two buttons: one to launch the survey, and another to dismiss
the survey cell. Simplified code as follows:

```swift
VStack {
    VStack {
        title
        description
    }
    surveyButton
}
.overlay(alignment: .topTrailing) {
    dismissButton
}
```

With the component looking something like this[^1]:

![The survey cell showing a title, description, dismiss button and "take survey" button.](./assets/blog-assets/survey-cell.png)

Initially, all elements were their own elements. The title, description, and two
buttons were separate.

The accessibility tree looked like this:

```
Dismiss, button
>
We'd love your feedback
>
What do you think of Save for Later?
>
Take Survey, button
```

Let's make sure the dismiss button is navigated to last using
`.accessibilitySortPriority`:

```swift
dismissButton
    // To make sure assistive technologies navigate to the dismiss button
    // within this view last, essentially ignoring the heuristic of this
    // being the "first" element in the view.
    .accessibilitySortPriority(-1)
```

Then, I combined the title and description into one label. A VoiceOver user
would be unlikely to "skip" the description at any point "on their way" to the
button anyway, and the title and description together gave a better idea of what
button the user would interact with next.

```swift
VStack {
    VStack {
        title
        description
    }
    .accessibilityElement(children: .combine)
    // outer VStack stuff...
}
```

So far so good — a reminder that Voice Control only exposes elements that are
buttons, so this would affect VoiceOver but not Voice Control.

# Optimizing for VoiceOver

To make a further improvement, I wanted to expose the two buttons as actions,
making "take survey" the default action and "dismiss" a secondary one. In that
way, we wouldn't have to deal with modifying the sort order to make sure the
survey button would preceed the (visually preceding) dismiss button.

Additionally, it would collapse the more complex cell into one element for the
user to interact with.

```swift
VStack {
    VStack {
        title
        description
    }
    surveyButton
}
.overlay(alignment: .topTrailing) {
    dismissButton
}
.accessibilityElement(children: .combine)
```

Talking about "with great power comes great responsibility"... the
`.accessibilityElement(children: .combine)` is _technically_ all we need here,
but restricts optimizations.

In this example, it does two major things:

- It groups the `Text` children to form its label, like we did with the same
code on the inner `VStack` previously.
- It exposes the `Button` children as accessibility actions.

Great..? Well, I'm not entirely sure what users that use VoiceOver would prefer,
but I was planning to schedule a user interview to find out.

... planning to schedule?

Well, yes. As after making this change, it dawned on me this might make Voice
Control interaction awkward.

# Voice Control

In VoiceOver, this could be spoken as "We'd love your feedback, What do you
think of Save for Later?, button, actions available". It would indicate that the
text is a button that can be interacted with, _and_ that the button has (other)
actions.

But for Voice Control, we now have a... challenge. How can we interact with
either button, now that we have just one element?

Technically, the whole element is a button. So we could try to activate
something that way? But what would that something be and do?

Well, Voice Control will generate an event... in the center of the view. Which
in our case is just some text. Not the dismiss button, not the survey button.

Well, that's awkward.

Now, _technically_ the user could in this case say "show actions for
`[element]`", for which we can at least optimize the `[element]`, which is now
that long label:

```swift
VStack {
    // element details
}
.overlay(alignment: .topTrailing) {
    dismissButton
}
.accessibilityElement(children: .combine)
.accessibilityInputLabels(["Take survey"])
```

Now the user can at least say "show actions for take survey", and they would
then be able to either take the survey or dismiss it.

Unfortunately, the only way to visually indicate there are actions for an
element is to use "show numbers". And only on iOS[^2] does it indicate a
"double arrow"... which as far as I know is undocumented. In other words, a
user would somehow need to be using "show numbers" (and not "show labels")
_and_ know what this double arrow represents.

But that leaves us with "Take survey" as a "ghost" action. Despite being the
most prominent option, it now doesn't actually do anything.

I can see a world where "Take survey" would result in actually showing the two
underlying actions, but that comes with its own array of problems, so perhaps
it's not that great of an idea in practice.

And so with that, I decided to revert to the version that does combine title and
description, but otherwise leaves the component alone. Perhaps not the "perfect"
solution for VoiceOver, but a decent one that then also allows for a great
Voice Control experience.

```swift
VStack {
    VStack {
        title
        description
    }
    .accessibilityElement(children: .combine)
    surveyButton
}
.overlay(alignment: .topTrailing) {
    dismissButton
        // To make sure assistive technologies navigate to the dismiss button
        // within this view last, essentially ignoring the heuristic of this
        // being the "first" element in the view.
        .accessibilitySortPriority(-1)
}
```

# Closing thoughts

It'd be great if we can see some additional affordances in Voice Control that
make it a bit easier to work with — especially as a user. Documented indications
for actions that work across platforms, for example.

But for now, we'll have to make do with some limitations, especially when trying
to optimize for multiple assistive technologies.

---

<sub>Thanks _so much_ to [Swaan](https://github.com/swaan-miller)
for proofreading!</sub>

[^1]: I'm clearly not (cut out to be) a designer.
[^2]: As far as I know this only works on iOS. It does not work on macOS; I'm not entirely sure of platforms like visionOS, tvOS, and watchOS.
