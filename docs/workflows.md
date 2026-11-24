# MOT workflows

[Product overview](../README.md) · [Architecture](architecture.md) · [Blueprint](blueprint.md)

## Capture and paste a dictation

1. Focus a text field. Hold the configured push-to-talk shortcut, wait for the chime, and speak.
2. Release the shortcut to finish. MOT formats the result according to your settings and checks the original destination before attempting to paste.
3. Use Dictation history to retrieve the text if the destination changes. You can also start from the Dictation screen and copy the result from there.

Microphone access lets MOT hear the dictation. macOS Accessibility permissions allow native paste and related integrations. In Settings → Transcription, choose Local Whisper for on-device recognition or configure your own Google API key for Gemini dictation.

## Record, find and export a meeting

1. Create a note in Notetaker. Choose Portuguese, English or Auto for Portuguese and English.
2. Take notes and start recording. On supported Macs, MOT captures microphone and system audio on separate tracks. macOS permissions are needed for the audio sources you capture.
3. Open the completed meeting. Each segment has a time range and a source label, Me or Others. Others refers to the system track and can include more than one voice.
4. Search the library across notes and transcripts and narrow results with a person or folder filter. Open a meeting to search its transcript and copy individual excerpts.
5. Use **Export transcript** to save a Markdown transcript. Use **Export meeting** to save a JSON record with notes, transcript and any saved summary.

The [search screenshot](../assets/meeting-search.jpg) shows a fictional example. Searching for `Friday` finds a Lunar Garden excerpt at `00:15`.

## Choose a writing style

| Mode or tool | Effect |
| --- | --- |
| Verbatim | Keep the recognized words with minimal editing. |
| Clean | Apply local formatting rules, including configured vocabulary, punctuation and spoken formatting. |
| Smart | Use Google cleanup where supported by the selected engine. The optional additional grammar review is a separate Google request. |
| App styles | Set different defaults for specific apps. |
| Dictionary and snippets | Teach MOT preferred spellings or set up text to expand from a spoken trigger. |
| Transforms | Clean up spacing, capitalize text, split sentences into bullets or remove line breaks locally. |
| Scratchpad | Keep draft notes or capture a microphone voice note. |
| Insights | Review usage and dictation speed derived from local history. |

## Optional Google summaries

Meeting transcription runs locally. Use the summary control after the transcript is available to request a summary from Gemini. MOT asks for confirmation before sending the transcript text and checks the returned quotations against the source excerpts. Google quota and billing belong to your Google project.

## Recovery and support

- If paste cannot go to the original field, open Dictation history and copy the result.
- For an interrupted meeting, recovery and retry depend on the captured audio that MOT was able to save.
- If a local model is unavailable, check the model shown in Transcription settings. Local recognition requires the relevant model on this Mac.
- The public repository documents the Apple silicon preview. Meeting recording requires macOS 15 or later.

