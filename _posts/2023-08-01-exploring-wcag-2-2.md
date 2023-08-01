---
layout: post
permalink: exploring-wcag-2-2
title: 'Exploring the new Success Critera in the Web Content Accessibility Guidelines (WCAG) 2.2'
tags: [accessibility]
---

The Web Content Accessibility Guidelines (WCAG) 2.2 has moved to its so called
"[Proposed Recommendation](https://www.w3.org/standards/types/#PR)" phase.

In this post I want to explore the new success criteria WCAG 2.2 extends from
WCAG 2.1, particularly from a mobile lens.

<!--more-->

> A Proposed Recommendation is a specification that has been accepted by W3C as
of sufficient quality to become a Standard. [It] has been reviewed by software
developers, [..] W3C Groups and other interested parties. It is published for
a final review [..] before approval by the W3C Director.

### Web Content Accessibility Guidelines 2.2's new Success Criteria

WCAG 2.2 comes with nine new success criteria. They span across the three
different conformance levels:

- Two of the new success criteria are level A;
- Four of the new success critera are level AA;
- The three remaining new critera are level AAA.

The WCAG consists of four major areas: Perceivable, Operable, Understandable
and Robust. These new success criteria are additions to the Operable and
Understandable areas.

### Operable

Operable sees five new success criteria:

- 2.4.11 Focus Not Obscured (Minimum) (AA)
- 2.4.12 Focus Not Obscured (Enhanced) (AAA)
- 2.4.13 Focus Appearance (AAA)
- 2.5.7 Dragging Movements (AA)
- 2.5.8 Target Size (Minimum) (AA)

#### 2.4.11 Focus Not Obscured (Minimum) (AA)

When a user interface component receives keyboard focus, the component is not
entirely hidden due to author-created content.

#### 2.4.12 Focus Not Obscured (Enhanced) (AAA)

Same as the above, but no part of the component is hidden by author-created
content.

---
<br />
Make sure that content is scrolled into or out of view properly when selecting
text fields, for example.

#### 2.4.13 Focus Appearance (AAA)

When the keyboard focus indicator is visible, an area of the focus indicator
meets all the following:

- is at least as large as the area of a 2 CSS pixel thick perimeter of the
unfocused component or sub-component, and
- has a contrast ratio of at least 3:1 between the same pixels in the focused
and unfocused states.

Exceptions:

- The focus indicator is determined by the user agent and cannot be adjusted by
the author, or
- The focus indicator and the indicator's background color are not modified by
the author.

---
<br />
Another one that should generally be covered by mobile — although a focus
indicator may be colored by the user and therefore could clash depending on
colors used in your app.

#### 2.5.7 Dragging Movements (AA)

All functionality that uses a dragging movement for operation can be achieved
by a single pointer without dragging, unless dragging is essential or the
functionality is determined by the user agent and not modified by the author.

---
<br />
This should be covered on mobile using native implementations — which I sure
hope you are using when dealing with drag-and-drop.

#### 2.5.8 Target Size (Minimum) (AA)

The size of the target for pointer inputs is at least 24 by 24 CSS pixels,
except where:

- **Spacing**: Undersized targets (those less than 24 by 24 CSS pixels) are
positioned so that if a 24 CSS pixel diameter circle is centered on the
bounding box of each, the circles do not intersect another target or the
circle for another undersized target;
- **Equivalent**: The function can be achieved through a different control on
the same page that meets this criterion;
- **Inline**: The target is in a sentence or its size is otherwise constrained
by the line-height of non-target text;
- **User agent control**: The size of the target is determined by the user
agent and is not modified by the author;
- **Essential**: A particular presentation of the target is essential or is
legally required for the information being conveyed.

---
<br />
It feels like this is something that is generally covered and accepted on
mobile; [a 44x44pt minimum is expected on Apple platforms](https://developer.apple.com/design/human-interface-guidelines/buttons#Best-practices)
(and 60x60pt in visionOS). Android uses [a minimum width and height of at least 48dp](https://support.google.com/accessibility/android/answer/7101858?hl=en).

### Understandable

Understandable sees four new success criteria:

- 3.2.6 Consistent Help (A)
- 3.3.7 Redundant Entry (A)
- 3.3.8 Accessible Authentication (Minimum) (AA)
- 3.3.9 Accessible Authentication (Enhancend) (AAA)

#### 3.2.6 Consistent Help (A)

If a Web page contains any of the following help mechanisms, and those
mechanisms are repeated on multiple Web pages within a set of Web pages, they
occur in the same order relative to other page content, unless a change is
initiated by the user:

- Human contact details;
- Human contact mechanism;
- Self-help option;
- A fully automated contact mechanism.

---
<br />
I feel most websites and apps have this one covered; it's nevertheless a great
clarification to have as a success criteria.

#### 3.3.7 Redundant Entry (A)

Information previously entered by or provided to the user that is required to
be entered again in the same process is either:

- auto-populated, or
- available for the user to select.

Except when:

- re-entering the information is essential,
- the information is required to ensure the security of the content, or
- previously entered information is no longer valid.

---
<br />
Sounds like a fantastic improvement to those pesky resume upload websites that
then require you to repeat all the content from your resume on the next page.

#### 3.3.8 Accessible Authentication (Minimum) (AA)

A cognitive function test (such as remembering a password or solving a puzzle)
is not required for any step in an authentication process unless that step
provides at least one of the following:

**Alternative**
Another authentication method that does not rely on a cognitive function test.

**Mechanism**
A mechanism is available to assist the user in completing the cognitive function test.

**Object Recognition**
The cognitive function test is to recognize objects.

**Personal Content**
The cognitive function test is to identify non-text content the user provided to the Web site.

#### 3.3.9 Accessible Authentication (Enhancend) (AAA)

Same as the above, but only these two exceptions are allowed:

**Alternative**
Another authentication method that does not rely on a cognitive function test.

**Mechanism**
A mechanism is available to assist the user in completing the cognitive function test.

---
<br />
Improving authentication like required here should be great for everyone, and
be a welcome improvement that will help us get rid of those awkward "find the
traffic cones" puzzles that make us feel like we're training some big
corporation's machine learning model. If not more than feel like we are.

It should also make things more secure; an example here would be allowing
two-factor authentication with one time passcodes that are built into modern
operating systems.

### Final Thoughts

This new set of success criteria feels like a welcome refinement of the WCAG
2.1 standard. I'm particularly keen to see the authentication enhancements,
particularly because I feel this is a great example of where accessibility is
clearly helping everyone.
