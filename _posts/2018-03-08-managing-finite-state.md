---
layout: post
permalink: managing-finite-state
title: Managing Finite State
---

⚠️ *This blog post uses Swift 4.1, as it depends on (parts of) [Conditional Conformance](https://github.com/apple/swift-evolution/blob/master/proposals/0143-conditional-conformances.md).* ⚠️

I don't think many people would argue if you tell them that managing state in an application is not the easiest thing in the world. In most applications you'll be dealing with networking, caching, databases, offline functionality... just to name a few.

In this blog post I want to focus on handling finite state and creating a simple state machine.

Consider an app with a list of songs, displayed in a simple `UITableView`. The `ViewController` does not care where the data comes from (be it from the network, from the database, etc.). It would like to update its state and let someone else handle it.

First of all, we have a model `Song`:

```swift
struct Song: Equatable {
  let artist: String
  let title: String
}
```

Nothing fancy. Note the `Equatable` conformance; that will come in useful later.

Then, we need a simple `UITableViewController` to show some songs.

```swift
class TableViewController: UITableViewController {
  private var datasource: [Song] = []
}
```

Again, nothing special. Our `TableViewController` has a `datasource` which is a plain array of our model, `Song`.

Now comes the interesting part. Somehow, we need to fetch some songs.

```swift
struct SongFetcher {
  func fetchSongs() -> [Song] {
    // Use your imagination and think of these Songs coming from a database or network somewhere.
    return [
      Song(artist: "Nils Frahm", title: "All Melody"),
      Song(artist: "Nils Frahm", title: "Sunson")
    ]
  }
}
```

The question that arises at this point... when do you call this function? In `viewDidLoad()`? Somewhere else?

The answer, probably, is "it depends". And that's right. It depends. It depends on state. Lets create it.

```swift
enum State<DataType: Equatable>: Equatable {
  case loading
  case data(DataType)
  case failure(Error)
  case empty

  static func ==(lhs: State, rhs: State) -> Bool {
    switch (lhs, rhs) {
    case let (.data(l), .data(r)):
      return l == r
    case let (.failure(l), .failure(r)):
      return l.localizedDescription == r.localizedDescription
    case (.loading, .loading), (.empty, .empty):
      return true
    // Make the switch exhaustive, and allow the compiler
    // to report an error when adding a new state.
    case (.data, _), (.failure, _), (.loading, _), (.empty, _):
      return false
    }
  }
}
```

As you can see, the `State` type is an `enum` which makes it finite by default. Nice; at a glance, we can see what we're dealing with here.

We have the `State` conform to `Equatable` by default, which requires our `DataType` to be `Equatable` as well, which we did with our `Song` type.

Although I will not cover it in this post, you can use this to check the current state, eg. `if state == .loading` and act upon it.

We should now update our `SongFetcher` to return a state instead of an array of `Songs`.

```swift
struct SongFetcher {
  func fetchSongs(completionHandler: (State<[Song]>) -> Void) {
    // We're opting for a happy path here, but we could also return
    // one of the other states here.
    let state = State.data([
      Song(artist: "Nils Frahm", title: "All Melody"),
      Song(artist: "Nils Frahm", title: "Sunson")
    ])
    // We can't exit this scope in a `.loading` state;
    // we need to return an actionable state.
    assert(state != .loading)
    completionHandler(state)
  }
}
```

The "only" thing left to do: actually handling your state in the `TableViewController`.

```swift
class TableViewController: UITableViewController {
  private var datasource: [Song] = []
  typealias StateType = State<[Song]>
  var state: StateType = .loading {
    didSet {
      handleState(state)
    }
  }

  func handleState(_ state: StateType) {
    switch state {
    case .loading:
      SongFetcher().fetchSongs { self.state = $0 }
    case let .data(songs):
      datasource.append(contentsOf: songs)
      tableView.reloadData()
    case let .failure(error):
      print(error) // show some error
    case .empty:
      () // show some empty view
    }
  }
}
```

The current state of the `TableViewController` will be persisted in the `state` variable, which is directly responsible for acting on a state change.

As the example shows, acting on state changes will then be handled by the `handleState(_:)` function. This will be called everytime you set the `state` variable.

With that in place we have a transparent and type-safe way to set and handle the state of our `TableViewController`. It also provides an easy way for anyone looking at such a class to get a sense of its scope.

---

How do you manage finite states in these situations? Is your approach similar, or is it rather different? Please let me know!

If you have an idea where this specific implementation can be simplified and improved - which it sure can - feel free to reach out as well.

You can find a project with this code [on GitHub](https://github.com/BasThomas/State).

---

If you want to do some further reading on State Machines, you can check out the [SwiftState](https://github.com/ReactKit/SwiftState) library or [@jemmons](https://twitter.com/jemmons)'s blog posts on Swift State Machines ([1](http://www.figure.ink/blog/2015/1/31/swift-state-machines-part-1), [2](http://www.figure.ink/blog/2015/2/1/swift-state-machines-part-2), [3](http://www.figure.ink/blog/2015/2/8/swift-state-machines-part-3-follow-up), [4](http://www.figure.ink/blog/2015/2/9/swift-state-machines-part-4-redirect)).
