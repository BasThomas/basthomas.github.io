---
layout: post
permalink: conforming-to-sequence
title: A journey to conforming to Sequence
tags: [programming, swift]
---

So [quite a while ago](https://github.com/BasThomas/Fif) I started working on a
[15-puzzle](https://en.wikipedia.org/wiki/15_puzzle) implementation for tvOS.

I had _a lot_ of fun doing that over a few days but then had some hiccups
trying to port it to iOS and kind of left it there. You know, your standard
side project. (or at least _my_ standard side project...)

Fast forward to a few weeks ago and with this still in the back of my mind,
I wanted to challenge myself to write a solver for this puzzle.

It sounded like an interesting problem to solve (pun intended) and would give
me the opportunity to learn about well, writing a solver, as well as diving
into algorithms, optimization, math, etc... the possibilities are endless!

I started looking into ways to go about solving this puzzle — it is really
interesting to see this pretty much 180 degree change from _you_ solving the
puzzle versus solving it with an algorithm. Long story short, I [watched](https://www.youtube.com/watch?v=5zr1jjSOklo)
a [few](https://www.youtube.com/watch?v=gGQ-vAmdAOI) videos (which I highly
recommend you watching, if you are interested in algorithms!) and was hooked.

## Writing a solver

I did not only want to write a solver that, given input `A`, would return me
output `B`, but rather give me all its steps towards `B`. That is what this
post will dive into.

Imagine the following code:

```swift
struct Board {
  // some implementation
  func solve() {
    // convert "a" to "b" here
  }
}
```

This is the "simple" approach of converting input `A` to output `B`. But we need
more. The `solve()` function should return a solution, including its steps:

```swift
func solve() -> Solution<Board> {
  // return the solution somehow
}
```

This all made sense in my mind. Actually, I wrote this pseudocode after I put
the code down and went to bed, where my brain continued thinking about it.

The next day, I started working on the `Solution` type:

```swift
struct Solution<Problem> {
  struct Step<T> {
    let step: T
  }

  let steps: [Step<Problem>]

  var input: Step<Problem> {
    return steps.first! // safe, as steps will guaranteed to be larger than 0.
  }

  var output: Step<Problem> {
    return steps.last! // safe, as steps will guaranteed to be larger than 0.
  }

  init(steps: [Step<Problem>]) {
    precondition(steps.count > 0, "Solution must contain at least one step.")
    self.steps = steps
  }

  init(steps: Step<Problem>...) {
    self.init(steps: steps)
  }
}
```

As you can see, `Solution` is generic over `Problem`. Now I am not quite sure
yet if I like that name, but with Xcode's ability to refactor it later (🙃),
I am sure I will be fine. (well actually, refactoring does not work in
Playgrounds, where I am experimenting with this. 🤷‍♂️)

⚠️ If you are using Playgrounds on macOS, use a macOS Playground rather than an
iOS Playground if you can get away with it; an iOS Playground will be a lot
slower as it will use a simulator under the hood. ⚠️

That being said, we got a first implementation going! It is still awful to
make the type checker explain[^1], but check it out:

```swift
let solution = Solution<Int>(
  steps: Solution.Step(step: 1), Solution.Step(step: 2), Solution.Step(step: 3)
)

print(solution.input) // Step<Int>(step: 1)
print(solution.output) // Step<Int>(step: 3)
```

And the steps look like this:

```swift
print(solution.steps) // [__lldb_expr_46.Solution<Swift.Int>.Step<Swift.Int>(step: 1), __lldb_expr_46.Solution<Swift.Int>.Step<Swift.Int>(step: 2), __lldb_expr_46.Solution<Swift.Int>.Step<Swift.Int>(step: 3)]
```

Yikes. Although... that was not the point I wanted to make. What I want to be
able to do (and yes, I am actually going to talk about the post's topic now!),
is to look at all steps of my solution.

Simple, you say? Indeed!

```swift
for step in solution.steps {
  print(step)
}

// Step<Int>(step: 1)
// Step<Int>(step: 2)
// Step<Int>(step: 3)
```

But would it not be marvelous if we can do this more magically, like so:

```swift
for step in solution {
  print(step)
}
```

Yes. Yes it would. Enter `Sequence`.

## Conforming to `Sequence`, part I

So "all" we have to do here, is to make sure that our `Solution` conforms to
the [`Sequence`](https://developer.apple.com/documentation/swift/sequence)
protocol. Like the `Array` already does.

So let us look at the documentation.

> **Conforming to the `Sequence` Protocol**
>
> Making your own custom types conform to `Sequence` enables many useful
> operations, like `for-in` looping and the `contains` method, without much
> effort. To add `Sequence` conformance to your own custom type, add a
> `makeIterator()` method that returns an iterator.
>
> Alternatively, if your type can act as its own iterator, implementing the
> requirements of the `IteratorProtocol` protocol and declaring conformance to
> both `Sequence` and `IteratorProtocol` are sufficient.

Well then. I do not know about you, but I was still a bit stuck at this point.
It _does_ feel like `Solution` will act as its own iterator. But how do I create
this "iterator"?

Well, I was lucky. The documentation continued:

> Here’s a definition of a `Countdown` sequence that serves as its own iterator.
> The `makeIterator()` method is provided as a default implementation.

Exactly. We do not have to worry about this iterator, and have an example
implementation.

```swift
struct Countdown: Sequence, IteratorProtocol {
  var count: Int

  mutating func next() -> Int? {
    if count == 0 {
      return nil
    } else {
      defer { count -= 1 }
      return count
    }
  }
}

let threeToGo = Countdown(count: 3)
for i in threeToGo {
    print(i)
}
// Prints "3"
// Prints "2"
// Prints "1"
```

Now let us implement ours.

## Conforming to `Sequence`, part II

This is where the "fun" starts. Let us expand on the `Solution` type, and make
it ready to implement `Sequence` as well as `IteratorProtocol`.

```swift
struct Solution<Problem>: Sequence, IteratorProtocol {

}
```

And now we use the fixit that Xcode will provide us, and implement the missing
implementations, right? Right.

This is what it generates:

```swift
struct Solution<Problem>: Sequence, IteratorProtocol {
  typealias Element = <#type#>

  typealias Iterator = <#type#>

  typealias Element = <#type#>
}
```

Welp. I should [file a JIRA](https://twitter.com/lattner_googles/status/675066035305906176)
for this.

At this point, I gave up on trying to figure this out myself, and came across
[a blog post](https://www.natashatherobot.com/swift-conform-to-sequence-protocol/)
from [NatashaTheRobot](https://twitter.com/NatashaTheRobot) that was exactly
what I was looking for.

So, like in the `Countdown` example, let us implement `next()`... the only
thing we will actually have to implement.

```swift
struct Solution<Problem>: Sequence, IteratorProtocol {

  private var _index: Int? = nil
  mutating func next() -> Step<Problem>? {
    // we're counting up (looping though the steps array), so begin at _index 0.
    if _index == nil { _index = 0 }
    // shadow _index so we do not have to deal with its optionality
    // this is also why the "other" index is underscored.
    var index = _index!
    if index < steps.count {
      // always move the _index forward after we can return a step
      defer { _index! += 1 }
      return steps[index]
    } else {
      // when we're done, reset the _index to nil.
      _index = nil
      return nil
    }
  }
}
```

Et voilà:

```swift
for step in solution {
  print(step)
}

// Step<Int>(step: 1)
// Step<Int>(step: 2)
// Step<Int>(step: 3)
```

Magical. 🧙‍♂️

You can find the implemention of the `Solution` type [in this gist](https://gist.github.com/BasThomas/082b1202f05e341ef6519f116b003108).

[^1]: If you have any ideas on how to improve this the verbosity of initializing a `Solution`, being able to help the type checker, please let me know!
