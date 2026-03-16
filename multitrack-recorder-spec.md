# Multitrack Voice/Instrument Recorder — PWA Spec

## Overview

A Progressive Web App (PWA) for layered multitrack audio recording via the phone microphone. Primary use case: recording harmony vocals and guitar parts one at a time, hearing previous tracks during recording to stay in sync. Designed to run in Chrome on a Samsung Android phone.

## Core User Flow

1. User opens app in Chrome on Android
2. User grants microphone permission
3. User records **Track 1** (e.g. guitar or base vocal)
4. User presses record for **Track 2** — all existing tracks play back through the speaker during recording so the user can synchronize
5. User repeats for as many tracks as needed
6. User plays all tracks simultaneously to hear the full harmony
7. (Optional) User can export/download the mix

> **Note on mic bleed**: Since there are no wired headphones yet (USB-C adapter on the way), playback comes from the speaker and will bleed into the mic during recording. This is acceptable for now as a working prototype. Once the adapter arrives, the user will use wired headphones and bleed will be eliminated.

## Technical Stack

- **Single HTML file** — no build toolchain, no framework required. Vanilla JS + Web Audio API.
- Must work offline after first load (PWA manifest + service worker for caching)
- Deployable to GitHub Pages or Vercel for free
- "Add to Home Screen" installable on Android Chrome

## Audio Implementation

- Use `getUserMedia({ audio: true })` for microphone access
- Use the **Web Audio API** (`AudioContext`) for all playback and recording
- Use `AudioContext.currentTime` as the master clock for all track timestamps — this ensures sync accuracy regardless of any processing delays
- Record each track as a `Blob` (WebM/Opus via `MediaRecorder`) and store in memory (and optionally IndexedDB for persistence across reloads)
- On playback, decode each blob to an `AudioBuffer` and schedule them all with `audioContext.currentTime` as the start point
- Per-track **volume control** (gain node)
- Per-track **mute** toggle

## Features — MVP

- [ ] Record new track (with all existing tracks playing back simultaneously)
- [ ] Stop recording
- [ ] Play all tracks together
- [ ] Stop playback
- [ ] Per-track volume slider
- [ ] Per-track mute button
- [ ] Delete a track
- [ ] Track list showing track number and duration
- [ ] Visual recording indicator (e.g. pulsing animation or level meter) so the user knows recording is active
- [ ] Microphone permission handling with clear error message if denied

## Features — To Decide in Claude Code Session

- Metronome / click track with BPM setting and count-in bars
- Loop mode (fixed-length recording that loops automatically)
- Waveform visualization per track
- Export / download the final mix as a WAV or WebM file
- Bluetooth latency compensation (calibration step — tap when you hear the beep)
- IndexedDB persistence (tracks survive page refresh)
- Maximum number of tracks (or unlimited?)
- PWA manifest and service worker (add to home screen, offline use)

## UI / Design Notes

- Designed primarily for **portrait mode on a phone** — large touch targets
- Dark theme preferred (studio/recording aesthetic)
- The design should feel like a real tool, not a toy — clean and functional
- Key actions (Record, Play, Stop) should be prominent and easy to tap with one thumb
- Track list should be scrollable if many tracks are added

## Constraints & Known Issues

- **No wired headphone jack on the phone** (temporarily) — playback goes through speaker during recording, causing mic bleed. Acceptable for now.
- Bluetooth headphone latency (~150–300ms) makes sync unreliable — do not use BT headphones while recording. Wired USB-C adapter is the solution.
- Web Audio latency on Android: playback latency of existing tracks is low and acceptable. Input monitoring (hearing yourself through the phone) is NOT needed and should NOT be implemented.
- Safari / iOS is NOT a target — Chrome on Android only for now.
