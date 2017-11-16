---
layout: post
permalink: switches-and-cases
title: Switches & Cases
---

A few days ago I was reviewing a pull request and came across the following code:

```swift
private func updateInsets() {
  if currentImage != nil && currentTitle == nil {
    contentEdgeInsets = Layout.contentEdgeInsets.image
  else if currentImage == nil && currentTitle != nil {
    contentEdgeInsets = Layout.contentEdgeInsets.title
  } else if currentImage != nil && currentTitle != nil {
    titleEdgeInsets = Layout.titleEdgeInsets.imageWithTitle
    contentEdgeInsets = Layout.contentEdgeInsets.imageWithTitle
  }
}
```

It was a piece of code that was really hard to parse for me. It also could be refactored to be exhaustive, adding some safety, but that less of a concern.

I came up with [the following](https://gist.github.com/BasThomas/d9f535ac48e72c215048a95c712ded07):

```swift
func switcher(_ a: Int?, _ b: Int?) {
    switch (a, b) {
    case (nil, nil):
        print("nothing")
    case (let thing?, nil):
        print("lhs", thing)
    case (nil, let thing?):
        print("rhs", thing)
    case (let left?, let right?):
        print(left, right)
    }
}
```

Don't mind the naming; the point is switching on a tuple, where both arguments are optional. This - in my opinion - improves the readability and gives us some compile-time safety.

As in our use case we didn't need to use the values, this is what we ended up with:

```swift
private func updateInsets() {
  switch (currentImage, currentTitle) {
  case (.some, .none):
    contentEdgeInsets = Layout.contentEdgeInsets.image
  case (.none, .some):
    contentEdgeInsets = Layout.contentEdgeInsets.title
  case (.some, .some):
    titleEdgeInsets = Layout.titleEdgeInsets.imageWithTitle
    contentEdgeInsets = Layout.contentEdgeInsets.imageWithTitle
  case (.none, .none):
    break
  }
}
```

Neat!

---

And now that we're talking about `switch`es and `case`s, there's also the `if case` syntax. This can be useful in case (no pun intended) you want to only act differently for a specific `case`.

This was the example in question:

```swift
switch message.transmissionState {
case .failed:
  print("oops!")
default:
  print("all good!")
}
```

As you can see, this is pretty much like an `if`-`else`... without the `if` and `else`. To make that intent more clear, we use `if case`:

```swift
func doSomething(with messageState: Message.State) {
  if case .failed = messageState {
    print("oops!")
  } else {
    print("all good!")
  }
}
```

That's better.

---

I really like these easy wins that improve readability and better convey intent. I would love to hear from you if you have run into something similar recently. You can reach out to me on Twitter: [@basthomas](https://twitter.com/basthomas).
