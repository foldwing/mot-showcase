# MOT product blueprint

[Product overview](../README.md) · [Architecture](architecture.md) · [Walkthrough](workflows.md)

MOT gives speech two destinations. Dictation sends a short thought to the text field that needed it. The Notetaker turns a meeting into a record you can find and quote later.

## Dictation

| Step | What the user sees | What the app does |
| --- | --- | --- |
| Start | Hold the shortcut in a supported text field, or start from Dictation. | Start a microphone capture session and remember the intended destination for shortcut dictation. |
| Speak | Choose Local Whisper or Google in Transcription settings. | Send microphone audio through the selected recognition path. The chosen writing style and rules apply where supported. |
| Release | Text appears in the original field when delivery succeeds. | Check the destination before paste. Keep the recognized text in history so it can be copied later. |

## Notetaker

| Step | What the user sees | What the app does |
| --- | --- | --- |
| Record | Create a note and start recording. | Keep microphone input and Mac audio on separate tracks. |
| Review | Open timed excerpts grouped by Me and Others. | Run speech recognition locally and save segments with timestamps and source channels. |
| Find | Search across saved meetings or inside one transcript. | Query the local library and return matching meetings and excerpts. |
| Export | Copy an excerpt, export the transcript as Markdown, or export the whole meeting as JSON. | The JSON record includes the transcript, saved notes and any saved summary. |
| Summarize | Confirm the optional Google summary request. | Send transcript text to Google on that request and validate returned quotations against the transcript. |

## Design decisions

| Decision | Reason | What it means in practice |
| --- | --- | --- |
| Separate dictation and meeting engine selection | Short dictations and long recordings have different storage and privacy needs. | Google dictation and local meeting transcription can be used on the same Mac. |
| Track-level labels | The capture source provides a reliable distinction between the mic and system audio. | Me and Others identify audio channels. Several remote speakers can share the Others label. |
| Save the draft before depending on paste | The user may switch focus before recognition finishes. | Dictation history gives the user another way to retrieve the text. |
| Use an indexed local library | Browsing a library should remain separate from recording and writing. | A read worker pages through matches. An FTS5 index narrows candidates for supported queries. |

## Preview status

The repository shows the app's workflow and architecture. The screenshots come from an isolated MOT profile with fictional data. The library smoke test searched for `Friday` and returned the Lunar Garden excerpt at `00:15`.

| Area | Status |
| --- | --- |
| Desktop preview | Apple silicon. Meeting recording requires macOS 15 or later and capture permissions. |
| Local and Google dictation | Engine selection is shown in the screenshots. Google requires the user's Google API key. |
| Meeting library | Search, filtering and transcript display are available in the preview. Feature availability depends on local model setup and permissions. |
| Distribution | Private preview. The public repository contains documentation and images. |

