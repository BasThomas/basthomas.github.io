---
layout: post
permalink: recording-voiceover
title: 'Recording VoiceOver on iOS & macOS'
tags: [accessibility, macos, ios, voiceover]
---

We're lucky that on iOS, recording the sound output of VoiceOver does not
require extra steps. On macOS, we're not so lucky, but we can use some tools to
route audio and achieve the same result — no background noise in recordings!

<!--more-->

# VoiceOver

VoiceOver is Apple's screen reader that allows low vision and blind people to
navigate their devices by haering what's on screen. I've been working on many
different applications improving and implemnting this functionality.

Sometimes, you want to demo or share the output and experience a VoiceOver user
might encounter, but it's not as straightforward to do this.

# iOS

On iOS, the built-in recording functionality you'll find in Control Centre
actually takes care of this. Even if you don't record audio, VoiceOver's audio
output will be captured regardless.

Additionally to audio, in Settings > Accessibility > VoiceOver, you can enable
the Caption Panel to show the textual representation of the audio output at the
bottom of the device's screen, too.

# macOS

On macOS, however, if you'd try to do the same using Screenshot.app, the
built-in recording software, the same thing is not true. Either you capture
no sound output for VoiceOver, or it uses your microphone and includes your
background audio. Like on iOS, though, we can show the textual output using the
caption panel, which can be activated through VoiceOver Utility > Visuals >
Panels and Menus > Show caption panel.

As far as I know, there's no way to achieve the same result recording
VoiceOver's sound output as on iOS without third-party software.

The good thing is — there is third party software that can achieve this, and
it's free! [Loopback](https://rogueamoeba.com/loopback/) allows you to set up
audio routing for various inputs, including VoiceOver.

# Loopback

You install and set up Loopback, then add a "New Virtual Device". Add a source,
and under "Special Sources", select VoiceOver. Name the Device in a way you can
recognize it, like "VoiceOver output".

Then, using Screenshot.app, under Options, select the virtual device you just
created as the "Microphone". And that't it; you can now record only the
VoiceOver sound output on macOS!

# Conclusion

If you're working on accessibility on macOS, you'll likely want to use this
sooner or later; recording all your microphone output is cumbersome, the sound
quality isn't great, and privacy-wise it's not a great option either, it
recording all your background noise.

The one caveat though — make sure to switch your microphone to your device — or
other desired input — or you'll end up recording your next presentation without
sound!
