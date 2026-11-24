<p align="center">
  <img src="assets/mot-icon.png" width="72" height="72" alt="">
</p>
<h1 align="center">MOT</h1>
<p align="center"><strong>Say it. Find it later.</strong></p>
<p align="center">Voice dictation and searchable meeting transcripts for macOS.</p>
<p align="center"><a href="#see-it-in-action">See it in action</a> · <a href="#transcription-engines">Transcription engines</a> · <a href="#architecture">Architecture</a> · <a href="#availability">Private preview</a></p>
<p align="center">
  <img src="assets/mot-banner.jpg" width="620" alt="A silver ribbon passes through a dark slit and comes out folded.">
</p>

## See it in action

MOT handles two jobs. Dictate into the app you're already using, or record a meeting and come back to the exact words later. The screens below come from MOT with fictional demo data.

### Dictate into the app you already have open

Hold the shortcut, speak, and release. MOT transcribes your speech and pastes it into the original text field when it still has focus. If you switch windows, open Dictation and copy the result from your history.

![MOT Dictation home with the shortcut hint, Start dictation button, and access to dictation history](assets/dictation.jpg)

### Record meetings and search the library

Start a note, write an agenda, and record. The Mac's system audio and your microphone are kept on separate tracks. The library searches across saved meeting notes and transcripts, with filters for people and folders.

![MOT Notetaker with a meeting library, search across notes and transcripts, and a preview of the fictional Lunar Garden meeting](assets/library.jpg)

### Find the line, then take it with you

Within a meeting, search the transcript or filter it by **Me** and **Others**. Copy an excerpt, copy the full transcript, or export Markdown. The preview also supports a full meeting export that gathers notes and available summary sections with the transcript.

![MOT transcript of the fictional Lunar Garden meeting with timestamped Me and Others excerpts, transcript search, and copy and export controls](assets/transcript.jpg)

## Transcription engines

| Workflow | Engine | Where the data goes |
| --- | --- | --- |
| Dictation · Local Whisper | Runs on the Mac with an available Whisper model. | Speech recognition stays on the Mac. |
| Dictation · Google | Streams audio to Google's Gemini API using your own API key. | Google receives audio for recognition. Your Google project determines quota and billing. |
| Meeting transcription | Runs locally. The preview can use a Core ML recognizer for live drafts when its model is ready, with Whisper available for final transcription. | Meeting audio stays on the Mac for transcription. |
| Meeting summary · optional | A separate request uses Google's Gemini API. | Google receives transcript text when you explicitly confirm the summary request. |

Choose **Google** or **Local Whisper** in **Settings → Transcription**. Dictation engine selection and meeting transcription are separate. Optional Google grammar review in Smart dictation is also a separate request.

![MOT Transcription settings with Google and Local Whisper as choices and an empty Google API key field](assets/engines.jpg)

## Writing tools

| Tool | What it's for |
| --- | --- |
| Dictionary | Add preferred spellings and exact substitutions for terms you dictate. |
| Snippets | Expand a spoken shortcut into reusable text. |
| Style | Choose Verbatim, Clean, or Smart formatting for dictation. |
| Technical text | Keep recognition closer to the literal wording in supported terminal apps. |

## Architecture

```mermaid
flowchart LR
  Mic["Dictation mic"] --> Route["Selected dictation engine"]
  Route --> Whisper["Local Whisper"]
  Route --> Gemini["Google Gemini API"]
  Whisper --> Text["Text in focused app or history"]
  Gemini --> Text
  Tracks["Meeting mic + system audio"] --> Local["Local meeting recognizer"]
  Local --> Library["Timestamped transcript in SQLite"]
  Library --> Search["Search, copy, Markdown export"]
  Library --> Consent["Explicit summary confirmation"]
  Consent --> Summary["Google Gemini summary"]
```

- **Desktop and capture.** Electron and React render the app. The Electron main process coordinates capture, engine selection, persistence, and delivery. Swift helpers provide native macOS integration, including meeting audio capture via ScreenCaptureKit.
- **Meeting storage and search.** The local SQLite store keeps notes and timestamped transcript segments. An FTS5 index supports library queries. Separate read workers keep search and history queries off the main process.
- **Text delivery.** Dictation checks the destination before pasting. The result stays in history for manual copy when the app cannot safely return text to the original field.

[Read the architecture and data-flow notes →](docs/architecture.md)

## Availability

MOT is in private preview for Apple silicon. Meeting recording requires macOS 15 or later and the corresponding macOS capture permissions. This repository documents the app and its design.

