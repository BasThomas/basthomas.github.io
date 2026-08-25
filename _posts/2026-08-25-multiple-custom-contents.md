---
layout: post
permalink: multiple-custom-contents-swiftui
title: 'Dynamically Setting Accessibility Custom Content Pairs in SwiftUI'
tags: [accessibility, ios, swiftui]
---

[Accessibility Custom Content](https://developer.apple.com/documentation/accessibility/customized-accessibility-content) 
was introduced back in the 2020 releases "[to deliver] accessibility
information from complex data sets to your users in measured portions."

Put another way, it allows you to present key-value information pairs to users
they can scan at their leisure, without having to parse this information from
a (long) accessibility label, accessed through the rotor.

<!--more-->

# Custom Content in UIKit

This API was introced for UIKit only at first. [Its API is a tad complex in
set up for UIKit](/improving-voiceover#custom-content), as I wrote about in the
past. Whereas API like `accessibilityCustomActions` allows to be set directly
on an `NSObject` (i.e. `myCell.accessibilityCustomActions = []`),
`.accessibilityCustomContent` comes with some Objective-C baggage, requiring
implementing the `AXCustomContentProvider` protocol and backing storage for
dynamic data.

But the API was there, and a great little way to improve the VoiceOver
experience in your app.

# Custom Content in SwiftUI

A year later, in the 2021 releases, we got a SwiftUI API for custom content.
Whereas in UIKit you'd pass an array to set all custom content on an element,
SwiftUI prefers repeated calls with different labels.

This would look something like the following, loosely mimicking a Notes.app
entry:

```swift
VStack {
    title
    HStack {
        updatedAt
        description
    }
}
.accessibilityElement(children: .combine)
.accessibilityCustomContent("Title", titleValue)
.accessibilityCustomContent("Updated at", updatedAtValue)
.accessibilityCustomContent("Description", descriptionValue)
```

However, this poses a problem when we'd want to set custom content based on
dynamic information, for example information coming from a backend.

The data used to set the custom content is not always static, and depending
on relevancy, might sometimes contain more (or fewer) entries depending on said
data. Data that should probably _not_ live in the `View`.

So what if we have this data from the backend, living in a view model of sorts:

```swift
class NoteViewModel {
    let note: Note

    var accessibilityCustomContents: [(LocalizedStringKey, LocalizedStringKey)] {
        [
            ("Title", note.title),
            ("Updated at", note.updatedAt),
            ("Description", note.description),
            ("Is locked"), note.isLocked ? "Yes" : "No")
        ]
    }
}
```

... now what do we do in the View?

```swift
struct NoteView: View {
    let viewModel: NoteViewModel

    var body: some View {
        VStack {
            // details
        }
        .accessibilityElement(children: .combine)
        // .accessibilityCustomContent( ... what here? we have no way to loop over
        // the view model data.
    }
}
```

# A new API

I broke my head over writing an API to tackle this problem. It's signature felt
clear; it would look something like this:

```swift
func accessibilityCustomContents(
    _ contents: [(LocalizedStringKey, LocalizedStringKey)]
) -> ModifiedContent<Self, AccessibilityAttachmentModifier>
```

But what would the implementation look like? We'd have to loop over our
`contents`, which felt like a return to "I suppose there's a reason we don't
have a public API for this".

I asked AI. It came up with a solution using `AnyView` allowing to apply
multiple calls to `.accessibilityCustomContent`. It _worked_, but relying on
`AnyView` felt costly, and possibly not worth it.

Then [I reached out to Nathan Tannar](https://github.com/nathantannar4). I
worked with Nathan on GitHawk back in the day, and then at Apple on
accessibility. And with the [Turbocharger repository](https://github.com/nathantannar4/Turbocharger),
which "[accelerates] SwiftUI development by providing commonly desired views
and view modifiers", and having seen a bunch for accessibility there, I thought
he might have some ideas.

Indeed: the trick was to use `.accessibilityAddTraits([])` as a no-op function
to compose a new view with the custom contents added:

```swift
func accessibilityCustomContents(
    _ contents: [(LocalizedStringKey, LocalizedStringKey)]
) -> ModifiedContent<Self, AccessibilityAttachmentModifier> {
    var modified = accessibilityAddTraits([])
    for content in customContent {
        modified = modified.accessibilityCustomContent(content)
    }
    return modified
}
```

A clever trick! You can find [its complete implementation in Turbocharger](https://github.com/nathantannar4/Turbocharger/blob/ff2fd081e9ff360cc3ae8ca0438cb7cb029b3e96/Sources/Turbocharger/Sources/ViewModifier/Accessibility%2BExtensions.swift#L258-L319).

# Usage

Now we can use this to implement where we got stuck before:

```swift
struct NoteView: View {
    let viewModel: NoteViewModel

    var body: some View {
        VStack {
            // details
        }
        .accessibilityElement(children: .combine)
        .accessibilityCustomContents(viewModel.accessibilityCustomContents)
    }
}
```

Now the responsibility for determining and setting up the data can live in the
view model, and we can do so dynamically, i.e. with fewer or more entries
depending on context.

# So Now What?

Perhaps we'll see this become public API some day? At least it's good that we
have tools to work around the limitation for now, whether dipping down to
UIKit or this no-op approach using `.accessibilityAddTraits([])`.

Perhaps the public API would use something like a `AccessibilityCustomContentBuilder`
instead, similar to `AccessibilityRotorContentBuilder`? It could then look
something like this:

```swift
.accessibilityCustomContents {
    ForEach(contents) { content in
        AccessibilityContentEntry(content.label, content.key)
    }
}
```

The interesting thing is that this approach for rotors was built _exactly_ to
allow a model to drive it, rather than the view.

# Conclusion

I hope this can help you adopt custom content more broadly in your SwiftUI apps,
improving the VoiceOver experience for users! I'll certainly be using this
myself.
