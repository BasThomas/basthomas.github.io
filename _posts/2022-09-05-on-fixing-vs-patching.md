---
layout: post
permalink: on-fixing-vs-patching
title: 'On Fixing vs Patching'
tags: [programming]
---

We programmers have — most likely — all fixed a bunch of bugs in our time. That edge case that was overlooked. That out of bounds error that we thought could never occur. That early return being hit because that one thing could be `nil` after all. That crash because the constant that wouldn’t change... did in fact change.

Not that I'm speaking from experience... why'd you ask?

---
<br />

These things happen. And arguably, it’s OK for them to happen. Yes, there’s a miriad of things we do — conciously or not — to _prevent_ these things from happening. In the end, however, it’s _because_ of bugs, of issues, of changes, that we programmers are, well, programmers. We’d not be needed otherwise, I’d argue.

# Fixing

One of my favorite things in programming is being able to write a piece of software that is well-defined — as well as moulding a thought, requirement, or project to become well-defined.

It’s then, with that knowledge, we can work in a structured way. We can define logic that can be tested. Well tested. With test code that not only becomes as important as the code we ship, but also as understandable, as readable.

The magic moment kicks in when an issue or (other) edge case is found — as it eventually always will, even though our software was so well-defined. We’re humans after all, and we do seem to make mistakes.

I digress... but now we can write a new test — with our now known failing input — and have that test fail. Of course. But this is great! We have a newly defined input with a certain unexpected output. We use our newfound knowledge to fix the bug, _et voilà_, the test passes. What a wonderful feeling.

---
<br />

A perfect scenario, one might argue, but one that isn’t always as straightforward. Not as straightforward to write that (as) testable code. Not as straightforward to understand the underlying issue and apply a fix. Not as straightforward to find that issue or unexpected output in the first place.

We could see the code and the accompanying test that fixes the issue as some form of “pure” documentation: you can’t argue, by reading the fix and the test, that the issue _isn’t_ fixed. And you can logically reason about it. (Of course, this isn’t exactly true. We could introduce a new issue with our fix, of course, especially when dealing with more complex challenges. Nor should we expect anyone to take a fix — however trivial — for granted, or to understand said fix in exactly the ways our mind does).

# Documentation

While our code is _a_ form of documentation, we’re all humans, and we all think differently. Have a different perspective.

When it comes to documentation, different things work (better) for different people. There’s no one way to document things. One might prefer video content over documentation that is written. Examples can make something tangible. That oh-so-understandable code does not hold up when someone unfamiliar with code is looking into things. Or your future self, not having used that programming language in a while.

Having things documented in multiple forms can help keep things able to be picked up by different people, at their own speed, in their own way. And having that kind of track record of how we work will help us keep a better understanding of our software (this, of course, applies to _much more_ than just fixing issues, like processes, decisions, and proposals.)

This applies to finding issues, non-expected outputs, and bugs, too. We certainly don’t always have a well-defined bug report, failing input, or other tangible information to guide us in the correct — or even _a_ — direction. And so we should be extra careful in tracing our steps, documenting our findings, and understanding our path from an issue being flagged to understanding it. To fixing it.

# Patching

... or, not. It just seems to happen that we’ll not always find the underlying issue. Understand what’s responsible for that crash. Get the time to investigate something not criticial enough at that point in time.

What I’ve seen happen in this case, from time to time, is that rather than _fixing_ the issue, we _patch_ it. Whilst you could argue that patching something would also fix it, what I mean with patching is the fact of preventing the issue at hand without having a full understanding of why it happens in the first place.

For example, if we’re unexpectedly returning `nil` given some unknown input, we can default to returning, say, an empty string `””`. If we know that sometimes we index into an array but go out of bounds, we can return early if we determine we would go out of bounds.

As we can see, however, these examples don’t _fix_ the issue. They dodge it, move around it, _ignore_ it. It’s that I see as a _patch_. And, alongside it, as something that _could_, down the line, cause undefined behavior — as in, it can cause us to evaluate code at a later point with input we don’t expect. Luckily for us, we have (among others) [assertions](/but-that-should-work) to help us there.

---
<br />

Patching issues isn’t something objectively bad. Sometimes we deal with a complex piece of code and we’ll have to make do with what we _do_ know. Add that extra piece of logging to help catch that gnarly input causes us problems. Write a workaround because an API that’s not in our hands causes issues.

It's in those cases that proper documentation (and potential follow-up steps) is written. That means, first of all, to know and understand the difference between a patch and a fix. And the ideal goal of that documentation would be, as I'd argue the ideal goal of _any_ documentation would be, is for anyone else (within reason, like others within the company, or other people in the team) to be able to go through it, learn from it, and figure out next steps.

Oh and as it turns out, doing that isn't easy. Isn't straightforward. Takes time, energy, experience and communication. But we can all try and learn.

# Conclusion

Working on projects, we'll all at some point have to deal with bugs, unexpected behavior, and other issues. They will all be of different magnitude and size, and we'll have to approach them accordingly.

Whereas in cases we can be confident of a fix, and document it, in other cases fixing something isn't straightforward, or (in the short term) feasable at all. It's then that we may be _patching_ issues, and making sure there's a shared understanding of what has (not been) done, as well as providing documentation on the process and next steps, is a crucial step that should not be neglected.
