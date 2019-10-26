---
layout: post
permalink: on-code-reviews
title: 'On Code Reviews'
tags: [programming]
---

I wanted to share some insights into how I review code, learning from others,
helping others, and getting a better overview of the code base which ultimately
allows me to write better[^1] code. This post is inspired by a recent
[tweet](https://twitter.com/kristoferbaxter/status/1186290473322741760), a]
similar [blog post](https://techbeacon.com/app-dev-testing/10-commandments-navigating-code-reviews)
(by [Angie Jones](https://twitter.com/techgirl1908)) and a [talk](https://www.youtube.com/watch?v=Ea8EiIPZvh0)
on the topic (by [April Wensel](https://twitter.com/aprilwensel)) from 2018's
try! Swift NYC.

- **I point out the good things.** This goes for anything really; not just code
reviews. If there's something you like or learned, let the author know. I
believe this will make a big difference on how a review is perceived, and is a
win-win all around.<br />
This is what I believe to be the **single most important part of a review**.
- **I read the pull request's description _and_ ticket.** Yes, also the ticket.
Seldom does that not help me getting a better understanding of the code. And
understanding is key. This makes everyone involved's time a lot more valuable.
If something in the description or ticket is unclear to me, I try to reach out
the the author before continuing a review.
- **I provide context.** If I come across something that "might better be
represented by a function rather than a computed property", I explain why I
would suggest that in the given case.
- **I provide references.** Whether it's a blog post, piece of code, or
documentation I reference, I link it. This allows the author to dig deeper into
the topic if they want, and may be helpful for those who read the comment(s) in
the future.
- **I take the time to try and understand the code.** When I feel like I'm
having a hard time grasping some of the decisions that have been made, or the
code that has been written in this pull request, I often feel asking about it
in writing is difficult. If this happens, I either take a step away from the
computer and try again later, or ask to go over it in person (face-to-face or
in a video call, _not_ a phone call. Being able to see each other aids
understanding.)
- **I ask questions.** I would call myself a curious person. If I see something
I don't know about, or want to know more about (eg. a piece of code, business
logic), I ask. If the review already has many comments, I might do this
outside the review itself.
- **I re-read your review before submitting it.** I don't always do this, but I
know it helps. I've seen the author ask about comments I wrote, and having a
hard time understanding what I meant. I feel that this has been happening less
and less over time, though! But communication is hard.
- **I feel responsibility for the code I review,** meaning I use "we", not
"you". "We could consider adding a comment explaining why we need to ignore X",
not "you".
- **I review my own code.** Especially in larger pull requests, I look through
the code before assigning a reviewer, either asking open questions to the
reviewer, making notes and/or making some changes. Seeing the "bigger" picture
often helps me catch issues/improvements to be made early, hopefully making the
code review more enjoyable in the process.

---

Keep in mind that this is not an exhaustive list, but rather a bunch of the
principles I try to keep in mind while reviewing. :)

Bonus: I'm a big fan of a tool we built at work to get a random reviewer for
your code. It's called `lotto`, and picks a random reviewer outside of your
team. It's great.

## On Non-Code Reviews

Oh, and most of this also applies reviewing other things, like blog posts and
documentation. For those in particular, I prefer printing the documents (so it
is easy to make both corrections and remarks), also giving me an opportunity to
find another place to work, away from my computer. Natural light helps!

If possible, I always discuss it in person afterwards.

---

If you're interested in code review or proofreading of a document, I'm more
than happy to help out. Just send me a direct message [on Twitter](https://twitter.com/{{ site.twitter_username }}).[^2]

 april wensel

[^1]: I suppose "better" is always subjective here, but I can take the learnings from these reviews and the insights of the current code base to help make certain decisions.
[^2]: I find it frustrating that asking for these things myself is arguably one of my weaker points. 🤷‍♂️
