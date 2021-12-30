---
layout: post
permalink: getting-started-dynamic-type
title: 'Getting Started With Accessibility: Dynamic Type'
tags: [swift, accessibility]
---

Dynamic Type lets you support different font sizes in your app, so that users
can use a font size that works best for them — from smaller than the system
default, to a whole bunch larger.

<!--more-->

... and there's some smarts built into the system to support those cases where
you're limited on space and can't really show a larger font.

---
<br />
- [Getting Started With Accessibility: VoiceOver](/getting-started-voiceover)
- [Improving Accessibility: VoiceOver](/improving-voiceover)
- [Verifying VoiceOver: Accessibility Inspector](/verifying-voiceover)
- [Improving Accessibility: Voice Control](/improving-voice-control)
- Getting Started With Accessibility: Dynamic Type (this post)

## Introduction

The [typography](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/typography/#dynamic-type-sizes)
documentation in Apple's Human Interface Guidelines gives you a good idea of
the thought process behind typography as a whole, and Dynamic Type specifically,
with [another page](https://developer.apple.com/design/human-interface-guidelines/accessibility/overview/text-size-and-weight/)
giving more accessibility-specific information on how to deal with text.

The good news about Dynamic Type is that it _almost_ comes out of the box. The
bad news is the _almost_.

To make sure Dynamic Type is supported by your elements, verify that the
[`adjustsFontForContentSizeCategory`](https://developer.apple.com/documentation/uikit/uicontentsizecategoryadjusting/1771731-adjustsfontforcontentsizecategor)
property for your element is set to `true`:

```swift
let label = UILabel()
label.font = .preferredFont(forTextStyle: .body)
label.numberOfLines = 0
label.adjustsFontForContentSizeCategory = true
```

Make note of a few things:

- We use the `preferredFont(forTextStyle:)` API that provides a system font.
If you're using custom fonts, you'll have to make sure you are properly
supporting Dynamic Type using [`UIFontMetrics`](https://developer.apple.com/documentation/uikit/uifontmetrics).
- We set the label's `numberOfLines` property to zero. While this is not a
strict requirement, you can image that with a larger font, more number of lines
may be used to properly layout your text.
- Elements automatically adjust their content size when the preferred content
size changes.

## Testing and Making Improvements

With this setup for your elements, you'll be able to take a look at your app as
a whole with a different Dynamic Text size; either by changing the system-wide
text size (`Settings > Accessibility > Display & Text Size > Larger Text Size`),
or on a per-app basis (with iOS 15): `Settings > Accessibility > Per-App
Settings > Add App > Larger Text`.

You'll probably note certain places in your app where, because of either the
use of a much smaller (or much larger) font, certain layouts either break or
become a little awkward.

While it's challenging to keep all layouts optimal regardless of the user's
text size, one useful API is [`isAccessibilityCategory`](https://developer.apple.com/documentation/uikit/uicontentsizecategory/2897444-isaccessibilitycategory),
which allows you to query if an accessibility text size is being used. With
that information, you may consider switching your layout from something
horizontal to something vertical, giving you more space to gracefully handle
the larger text size.

<video width="750" controls alt="Switching between a horizontal to a vertical layout">
  <source src="/assets/blog-assets/layout.mp4" type="video/mp4">
</video>

### Large Content Viewer

Unfortunately, certain elements are built in such a way that they are not
expected to increase beyond a certain size. Examples are tab bar items,
segmented controls and things like overlays.

To solve this, Apple introduced the [Large Content Viewer](https://developer.apple.com/videos/play/wwdc2019/261/),
in, I think, iOS 11. In iOS 13, an API was introduced, too, enabling us to adopt
the large content viewer for custom controls. The Large Content Viewer is shown
based on the Dynamic Type settings: when using an accessibility text size,
elements that can't grow in size are expected to show it; the previously
mentioned tab bar items and segmented control work out of the box.

Note that at the time of writing this, Large Content Viewer is unfortunately not
supported in SwiftUI; you'll have to implement it for custom controls using
UIKit.

## Conclusion

Supporting Dynamic Type may seem to require only few changes, yet in reality
most apps won't come with a great experience for it just like that.

Luckily, there are APIs that let us customize our layouts based on text size,
allowing us to improve that experience for smaller and larger text sizes.

Let me know your thoughts on this post, and if you have any questions,
I'd love to help!

---
<br />
- [Getting Started With Accessibility: VoiceOver](/getting-started-voiceover)
- [Improving Accessibility: VoiceOver](/improving-voiceover)
- [Verifying VoiceOver: Accessibility Inspector](/verifying-voiceover)
- [Improving Accessibility: Voice Control](/improving-voice-control)
- Getting Started With Accessibility: Dynamic Type (this post) 
