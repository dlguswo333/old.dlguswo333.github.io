---
layout: post
toc: true
title: "Determining HEVC (H.265) Support On Web Browser"
categories: ["Programming"]
tags: ["Web", "JavaScript"]
author:
  - 이현재
---

<p align="center">
  <img height="50" src="https://www.chromium.org/_assets/customLogo.png" alt="chromium-logo">
</p>

# 1. Abstraction
We are going to learn how to determine whether a device
supports HEVC videos on Web Browser, mainly recent versions of Chromium.
<!--more-->

# 2. Introduction
On recent releases of Chromium, starting from version 107,
HEVC, H.265 video format is being supported.

![caniuse-hevc](/img/2023-01-12-determining-hevc-support-on-web-browser/caniuse-hevc.png)

In detail, HEVC videos are playable if you are using chromium 107 or later,
and Windows 8 or later, or Big Sur if you are on Mac, or Chromium > 108
if you are on Linux, and **with Hardware Support**.
Check out <https://caniuse.com/?search=hevc> if you want to see it by yourself.
Also, you can check if your browsers can play HEVC videos in the link:
<https://cconcolato.github.io/media-mime-support/#other_video_codecs>

It's quite surprising to see Chromium support started very recently
considering that HEVC videos has become ubiquitous and most devices
have started supporting HEVC natively for more than half a decade.

Anyway, to provide Web users with the best quality videos,
Web developers might want to provide H.265 videos over
H.264 ones for those who are qualified.
But Also, you need to provide fallback H.264 videos for those who are not qualified.

You may think of a `video` element with several `source` elements inside.

```html
<video>
    <source src="/h265.mp4" type="video/mp4">
    <source src="/h264.mp4" type="video/mp4">
</video>
```
If you specify HEVC video source first, Web Browser will try to show the HEVC video
first, then H.264 video.
**However**, if you test on devices without HEVC support,
you might witness that it malfunctions, showing the HEVC video,
on recent Chromium browsers.

This is because you did not provide detailed `type` of the video.
Web browsers, at least Chromiums, do not know or care whether
a video file is actually playable or not.
You need to specify what `codecs` the video is encoded with.
Thus, you need to find a way to specify HEVC codecs.

Most of online resources tell you that
you can put `codecs="hvc1"`, like below.

```html
<source src="/h265.mp4" type='video/mp4; codecs="hvc1"'>
```

> **NOTE** You can alternatively use `type="video/mp4; codecs=&quot;hvc1&quot"`
> If you want to wrap the string inside double quotes.

...or `codecs="hev1"`. `hvc` and `hev` are synonyms, at least
in the world of `source` elements.

However, **this is again wrong**. Test the following JavaScript code on Chromium browsers
with HEVC support.

```js
const video = document.createElement('video');
console.log(video.canPlayType('video/mp4; codecs="hvc1"'))
```

On Chrome 108, it logs an empty string `''` meaning that the browser cannot play
that type of videos.
However, On Safari on Monterey M1 Mac, it shows `'probably'`!
Why does this happen?

# 3. Why `codecs="hvc1"` doesn't work on Chromium
This is because Chromium's intention to strictly oppose
using ambiguous codec identifiers for HEVC.
Refer to Chromium's test code on `canPlayType`.
As you can see from the link, this is source code of Chromium 108.

<https://chromium.googlesource.com/chromium/src/+/refs/tags/108.0.5359.181/content/test/data/media/canplaytype_test.js>

```cc
// Copyright 2020 The Chromium Authors
// Use of this source code is governed by a BSD-style license that can be
// found in the LICENSE file.
...
// Don't allow incomplete/ambiguous codec ids for HEVC.
// Codec string must have info about codec level/profile, as described in
// ISO/IEC FDIS 14496-15 section E.3, for example "hev1.1.6.L93.B0"
'hev1',
'hvc1',
```

This clearly shows that Chromium outputs an empty string to suppress the use of
incomplete codecs identifiers. There are numerous codecs and profiles within HEVC.
Saying `probably` on ambiguous specifications may result in unintended errors.
What if the device does support HEVC 8 bit depth videos
but not HEVC 10 bit depth videos?
What if the video resolution is so huge, like 8K (*7680x4320*)?
Does every device with H.265 support support that large resolution?

Therefore, in order to *correctly* determine whether a device supports your videos,
it's safe to specify the detailed codecs.
This philosophy has been applied way back, at least Chromium 62.

<https://chromium.googlesource.com/chromium/src.git/+/62.0.3178.1/content/browser/media/media_canplaytype_browsertest.cc>

# 4. Solution
Therefore, Find the exact codecs your videos are encoded with.
If you don't know what codecs your videos use or
it's just because you are too lazy for this,
pick a codec that is absurdly high so that if a device support
that high-end codec, it's absolutely nonsense that
the device does not support your videos.

There are some codec identifier examples on <https://dashif.org/codecs/video/>.
They are the organization to help build MPEG standards and
create interoperability guidelines.
It consists of well-known companies, such as Microsoft and Google.
I would pick `hvc1.2.4.L153.B0` to 99.9% make sure
that browsers support your HEVC videos.

By the way, there is another powerful JavaScript API you can use to
get detailed codec support information
with which you can specify content type, resolutions,
and even bitrate called `Media Capabilities`.

<https://developer.mozilla.org/en-US/docs/Web/API/Media_Capabilities_API>
