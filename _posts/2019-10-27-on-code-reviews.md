---
layout: post
permalink: on-code-reviews
title: 'On Code Reviews'
tags: [programming]
---

I wanted to share some insights into how I review code, learning from others,
helping others, and getting a better overview of the code base which ultimately
allows me to write better[^1] code. This post is inspired by a recent
[tweet](https://twitter.com/kristoferbaxter/status/1186290473322741760), a
similar [blog post](https://techbeacon.com/app-dev-testing/10-commandments-navigating-code-reviews)
(by [Angie Jones](https://twitter.com/techgirl1908)) and a [talk](https://www.youtube.com/watch?v=Ea8EiIPZvh0)
on the topic (by [April Wensel](https://twitter.com/aprilwensel)) from 2018's
[try! Swift NYC](https://www.tryswift.co) (which you should totally attend if
you get the chance).

- **I point out the good things.** This goes for anything really; not just code
reviews. If there's something you like or learned, let the author know. I
believe this will make a big difference in how a review is perceived, and is a
win-win all around.<br />
This is what I believe to be the **single most important part of a review**.
- **I assume the best intentions.** I've written questionable code before, or
have overlooked mistakes. That happens. Always be kind and approach a review
positively. 
- **I read the pull request's description _and_ ticket.** Yes, also the ticket.
This makes the time of everyone involved a lot more valuable. Seldom does this
not help me gain a better understanding of the code. And understanding is key.
If something in the description or ticket is unclear to me, I try to reach out
to the author before continuing a review.
- **I provide context.** For instance, if I come across a `struct` that only
serves as a namespace, I wouldn't only suggest to use a caseless `enum` instead,
but explain why I think that is the case (no pun intended): a caseless `enum`
has a non-accessible `init` by default, while a `struct` can still be
initialized without a `private init`.
- **I provide references.** Whether it's a blog post, piece of code, or
documentation I reference, I link it. In the example on context above, I would
link to [a blog post](https://www.natashatherobot.com/swift-enum-no-cases/)
explaining the advantages (of a caseless `enum`). This allows the author to dig
deeper into the topic if they want, and might be helpful for those who read the
comment(s) in the future.
- **I take the time to try and understand the code.** When I feel like I'm
having a hard time grasping some of the decisions that have been made, or the
code itself, I often feel asking about it in writing is difficult. If this
happens, I either take a step away from the computer and try again later, or ask
to go over it in person (face-to-face or in a video call, _not_ a phone call.
Being able to see each other aids understanding).<br />
An exception to this would be a pull request I feel is introducing too many
concepts at once, in which case I'll either ask if we could break up the pull
request in more bite-sized pieces, offering to help do so in person if need be.
- **I ask questions.** I would call myself a curious person. If I see something
I don't know about, or want to know more about (e.g. a piece of code, business
logic), I ask. If the review already has many comments, I might do this
outside the review itself.<br />
Also, if I see code that appears to be copied, where I feel improvements can be
made, I ask if this is something that has been considered/if this is something
for us to consider in a follow-up pull request.
- **I re-read a review before I submit it.** I don't always do this, but I
know it helps. I've seen the author ask about comments I wrote, not (fully)
understanding what I meant. Though communication is hard, I feel this has
been happening less over time.
- **I feel responsible for the code I review,** meaning I use "we", not
"you". "We could consider adding a comment explaining why _we_ need to ignore
X", not "you".
- **I review my own code.** Especially in larger pull requests, I look through
the code before assigning a reviewer, asking open questions to the reviewer,
making notes and/or making some changes. Seeing the "bigger" picture often helps
me catch issues/improvements to be made early, hopefully making the code review
more enjoyable in the process.
- **I don't use words like "easily", "simply", or other downplaying words.**
The fact something would be any of those is always an assumption to whoever is
reading it, and serves no purpose.

---

Keep in mind that this is not an exhaustive list, but rather a bunch of the
principles I try to keep in mind while reviewing, and there's still so much to
learn. If you have any thoughts on this post, like guidelines that you're using
that I've missed here, I'd love to hear about them!

Bonus: I'm a big fan of a tool we built at work to get a random reviewer for
your code. It's called `lotto`, and picks a random reviewer outside of your
team. It's great.

## On Non-Code Reviews

Oh, and most of this also applies to reviewing other things, like blog posts and
documentation. For those in particular, I prefer printing the documents (so it
is easy to make both corrections and remarks). That also gives me an
opportunity to find another place to work, away from my computer. Natural light
helps!

If possible, I always discuss it in person afterward.

---

If you're interested in having me do code review or proofread a document, I'm more
than happy to help out. Just send me a direct message [on Twitter](https://twitter.com/{{ site.twitter_username }}).[^2]

<sub>Thanks _so much_ to [João](https://twitter.com/NSMyself),
[Barbie](https://twitter.com/barbieinbeta), [Kristaps](https://twitter.com/fassko)
and [Donny](https://twitter.com/DonnyWals) for proofreading!</sub>

[^1]: I suppose "better" is always subjective here, but I can take the learnings from these reviews and the insights of the current code base to help make certain decisions.
[^2]: I find it frustrating that asking for these things myself is arguably one of my weaker points. 🤷‍♂️
