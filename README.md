# MOT

![MOT app icon](assets/mot-icon.png)

Dictate into the text field you're already using, or record a meeting and find what was said later. MOT is a macOS app for Apple silicon.

## What it does

- **Dictation.** Hold the shortcut, speak, and release. MOT pastes the text when the field stays in focus. You can also copy it from Dictation.
- **Meeting recording.** Capture the microphone and system audio on separate tracks. MOT transcribes the recording and saves the transcript in a searchable library. Reopen, copy, or export it as Markdown.
- **Meeting notes.** Keep your own notes with the transcript. The development preview adds a Markdown export that combines notes, summary, decisions, action items, and transcript in one file.

## Data handling

MOT can transcribe meetings with local models. Generating an AI summary is a separate action that asks before sending transcript text to Google.

## Requirements and status

Meeting recording requires macOS 15 or later. This repository documents the project. The app is in a private development preview.

Electron · React · Swift · SQLite · Whisper · Parakeet
