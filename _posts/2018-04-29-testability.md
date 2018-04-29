---
layout: post
permalink: testability
title: Testability
tags: [programming, swift, testing]
---

I found an inconsistency in an app, which had to do with the color of a certain action's title.

Easy fix, I thought. I can fix this without too much work.

<!--more-->

So that's what I did. And I was right — it was an easy fix. However I wanted to go a step further, to prevent this from happening in the future, and to validate the current behavior.

## The problem

The problem was a `UIAlertAction` having a style of `.default` (making the button blue), where it was expected to be `.destructive` (which would make it red). The action was deleting a post, after all. The code looked something like this, and was inside a `UIViewController` subclass:

```swift
func presentActionsAlert(for input: [String]) {
  let alertController = UIAlertController(title: nil, message: nil, preferredStyle: .actionSheet)

  for value in input {
    switch value {
    case "share":
      let action = UIAlertAction(title: "share", style: .default)
      alertController.addAction(action)
    case "delete":
      let action = UIAlertAction(title: "delete", style: .default)
      alertController.addAction(action)
    default:
      break
    }
  }

  let cancelAction = UIAlertAction(title: "cancel", style: .cancel)
  alertController.addAction(cancelAction)

  present(alertController, animated: true)
}
```

The fix was easy:

```diff
- let action = UIAlertAction(title: "delete", style: .default)
+ let action = UIAlertAction(title: "delete", style: .destructive)
```

So I committed the fix, but before opening a pull request I wanted to check if all tests still succeeded (as to not waste resources on our continuous integration infrastructure).

Guess what. All tests passed. 😱

So I took another look at the function, and thought... this makes sense. How would I ever test this?

## Improving testability

So I refactored the function to only create the `UIAlertController`, without the side effect of presenting it.

```diff
- func presentActionsAlert(for input: [String]) {
+ func actionsAlert(for input: [String]) -> UIAlertController {

- present(alertController, animated: true)
+ return alertController
```

What is nice about this, is that we reduced our function to a function that does not do more than getting our `input` and providing us with an `output`[^1].

At this point, I was ready to write a unit test, that would validate some assumptions — in this case checking that if we pass in a `"delete"` as input, expecting a `.destructive` style as the output. If this would be changed in the future, the corresponding unit test would fail.

Lets take a look at this unit test.

```swift
class MyTest: XCTestCase {

  let myViewController = MyViewController()

  func test_that_aDeleteInput_has_aDestructiveStyle() {
    let input = "delete"
    let output = myViewController.actionsAlert(for: [input])

    XCTAssertEqual(output.actions.first { $0.title == input }?.style, .destructive, "A `delete` input should have a `.destructive` style.")
  }
}
```

And there we go: our assumption is being validated in a test[^2]. 🎉

## Conclusion

It is good to take a look and see if you can improve a piece of code, especially when it involved a contained fix like this one. Check if there is something to be done to leave the code a bit cleaner than you found it[^3].

[^1]: If that makes you think about functional programming: you're right. In fact, in its current form, this is a [pure function](https://en.wikipedia.org/wiki/Functional_programming#Pure_functions). And if pure functions are awesome for something, it is testability!
[^2]: In the real test case I wrote, I validated some other things, like there being a `.cancel` action present, as well as the `actions`'s count when we pass in some input that would end up in the `default` case.
[^3]: Now that the function does not rely on anything within the `UIViewController`, we could even take it out of there. Tearing down Massive ViewControllers one function at a time.
