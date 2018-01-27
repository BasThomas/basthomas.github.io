---
layout: post
permalink: managing-finite-state
title: Managing Finite State
---

I don't think many people would argue if you tell them that managing state in an application is not the easiest thing in the world. In most applications you'll be dealing with networking, caching, databases, offline functionality... just to name a few.

I am sorry to break the news to you, but this blog post is not going to offer a solution or way to make it easy; instead I want to focus on handling finite state and creating a simple state machine.

Consider an app with a list of songs, displayed in a simple `UITableView`. The `ViewController` does not care where the data comes from (be it from the network, from the database, etc.), but is would like to update its state and let someone else handle it. Here's the whole thing:

```swift
import UIKit

struct Song: Equatable {
  let name: String
}

class TableViewController: UITableViewController {
  private var datasource: [Song] = []
  enum State {
    case loading
    case data([Song])
    case failure(Error)
  }

  var state: State = .loading {
    didSet {
      handleState(state)
    }
  }
}

private extension TableViewController {
  func handleState(_ state: State) {
    switch state {
      case .loading:
        let newState = fetchSongs()
        assert(newState != .loading)
        self.state = newState
      case let .data(songs):
        datasource.append(contentsOf: songs)
        tableView.reloadData()
      case let .failure(error):
        showError(error)
    }
  }

  func fetchSongs() -> State { return .data([Song(name: "All Melody")]) }
  func showError(_ error: Error) { }
}

// We might also want to make our `State` conform to `Equatable`,
// if for example we want to check `if state != .loading`.
extension TableViewController.State: Equatable {
  typealias State = TableViewController.State

  static func ==(lhs: State, rhs: State) -> Bool {
    switch (lhs, rhs) {
    case let (.data(l), .data(r)):
      return l == r
    case let (.failure(l), .failure(r)):
      return l.localizedDescription == r.localizedDescription
    case (.loading, .loading):
      return true
    // Make the switch exhaustive, and allow the compiler
    // to report an error when adding a new state.
    case (.data, _), (.failure, _), (.loading, _):
      return false
    }
  }
}
```

The most interesting part is the (finite) state:

```swift
enum State {
  case loading
  case data([Song])
  case failure(Error)
}
```

... which is pretty much the whole idea; simple (in terms of state machines) but effective.

It is intended for this to be the complete range of states for this Controller. The current state of the Controller will is persisted in the `state` variable, which is directly responsible for acting on a state change.

As the example shows, acting on state changes will be then be hanled by the `handleState(_:)` function. This will be called everytime you set the `state` variable.

With that in place we have a transparent and type-safe way to set and handle the state of our Controller. It also provides an easy way for anyone looking at such a class to get a sense of its scope.

---

Please let me know if you find where this can be simplified and improved - it sure can. You can make the `State` enum generic, which would be useful if you have multiple Controllers with a loading / data / failure state machine, where the data type is different among the Controllers.

---

If you want to do some further reading on State Machines, you can check out the [SwiftState](https://github.com/ReactKit/SwiftState) library or [@jemmons](https://twitter.com/jemmons)'s blog posts on Swift State Machines ([1](http://www.figure.ink/blog/2015/1/31/swift-state-machines-part-1), [2](http://www.figure.ink/blog/2015/2/1/swift-state-machines-part-2), [3](http://www.figure.ink/blog/2015/2/8/swift-state-machines-part-3-follow-up), [4](http://www.figure.ink/blog/2015/2/9/swift-state-machines-part-4-redirect)).

*The code in this blog post has been written with Xcode 9.3 beta 1 & Swift 4.1. You can find the code in [this gist](https://gist.github.com/BasThomas/c83a5a1f752fbdc79c62963164acab25).*
