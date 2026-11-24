# MOT architecture

[Product overview](../README.md) · [Product blueprint](blueprint.md)

MOT has two audio pipelines with different lifetimes. Dictation is a short session that usually ends in another app's text field. A meeting is a recording with two audio tracks, notes, timestamped excerpts, and a durable record that can be searched later.

[Full architecture diagram](../README.md#architecture) · [Editable Excalidraw source](mot-architecture.excalidraw)

## Runtime boundaries

| Layer | Responsibility |
| --- | --- |
| Electron renderer · React | Dictation history, meeting notes and transcripts, settings, and search. The renderer uses a preload bridge for desktop operations. |
| Electron main process | Capture and recognition lifecycle, engine selection, meeting state, storage, native helper coordination, and delivery. |
| Native macOS helpers · Swift | System integrations such as meeting audio capture via ScreenCaptureKit. The desktop app receives microphone and system audio as distinct tracks. |
| Local recognition | Whisper runs locally. The meeting path can use a Core ML recognizer when ready and can fall back to Whisper for final transcription. Live drafts depend on the Core ML recognizer being available. |
| Google Gemini API | Optional streaming dictation, separate grammar review, and optional meeting summaries. The user's Google project provides the API key and determines usage limits. |
| SQLite and read workers | Local state for dictation and meetings. Dedicated workers handle history and meeting-library reads. |
| Local MCP server | Opt-in standard input/output interface. A connected client can read saved dictation and meeting text through read-only database connections. |

## Dictation

1. The user starts capture with a shortcut or the app's dictation control. When the shortcut starts a session, MOT remembers the intended destination.
2. The selected engine processes the microphone input. Local Whisper handles recognition on the Mac. In Google mode, MOT streams dictation audio to the Google API.
3. The selected style affects how the recognized text is prepared. Dictionary and snippets provide user-defined forms; optional Smart grammar review can use a separate Google request.
4. MOT tries to paste into the original field only when it can verify the destination. The result remains in local dictation history and can be copied manually.

## Meetings

1. The user creates a meeting note and starts recording. Capture preserves the microphone and system audio tracks separately.
2. The meeting recognizer processes the tracks locally. The app uses the channel to label timestamped segments **Me** and **Others**. Multiple remote participants can share the **Others** channel.
3. Completed segments and meeting notes live in SQLite. The library indexes text with FTS5 and can page through results using a cursor. A read worker serves library queries separately from capture and writes.
4. A user can search across meetings or within one transcript, copy an excerpt, and export the transcript as Markdown. Full meeting export uses JSON and includes the transcript, notes, and available summary content.
5. To generate a summary, the user confirms a separate Google request from the meeting. MOT sends transcript text for the summary. Summary quotations are checked against source excerpts before the result is saved.

### How library search works

The query can combine search text, a person filter and a folder filter. The FTS5 trigram index narrows candidates for eligible queries, then the reader checks the canonical text for a match. Results include an excerpt when one is available. A cursor carries the position for the next page.

## Local MCP interface

An MCP client connected from the desktop preview can follow these read-only flows.

| Task | Tool sequence |
| --- | --- |
| Retrieve a dictation | Use `search_dictations` and pass the chosen item ID to `get_dictation`. |
| Read a meeting | Use `list_meetings` for a day, choose a result and call `get_meeting_transcript`. Follow the transcript cursor for additional excerpts. |
| Read a saved summary | Use the same meeting ID with `get_meeting_summary`. |

The server reads from the local profile. A connected client can receive the text it requests, so connect clients you trust to read saved dictations and meetings.

## Data handling by operation

| Operation | Local input | External transmission |
| --- | --- | --- |
| Local Whisper dictation | Microphone audio | Recognition stays local. |
| Google dictation | Microphone audio | Dictation audio goes to Google's API in the selected Google mode. |
| Optional Smart grammar review | Recognized dictation text | Text goes to Google's API when that feature is enabled. |
| Recording and transcribing a meeting | Microphone and system audio | The meeting recording and recognition pipeline stays local. |
| Explicit Google meeting summary | Saved transcript text | Transcript text goes to the Google API after in-app confirmation. |
| Search and Markdown export | Local meeting records | Local queries and file export. |

## Technical choices

- **Keep channel labels grounded in capture.** Separate tracks preserve the difference between what the Mac played and what the local microphone heard.
- **Separate recognition from summarization.** A local meeting transcript stays usable without a Google key. Cloud summarization is a deliberate follow-up.
- **Keep the destination attached to dictation.** A focus change can leave the text in history instead of pasting into an unrelated field.
- **Keep search off the capture path.** Indexed, paginated reads help keep browsing a large library separate from active recording.

## Preview scope

The public repository contains product documentation and screenshots from an isolated MOT profile with fictional data. The architecture describes the preview's implementation. Model availability, permissions, and provider settings affect which options the app can use.

