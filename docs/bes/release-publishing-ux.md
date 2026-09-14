# Release Publishing and Fast Settings UX

## Purpose

This specification defines the release-to-social publishing flow and the fast settings panel used by daily operators. It focuses on reliable Telegram delivery, saved publication accounts, instant controls, live preview, autosave, and reversible changes.

## Telegram Release Publication Flow

The publication pipeline must be observable end to end so a failed Telegram post always exposes a clear user-facing reason.

1. **Release loading**: fetch the release by ID, verify that title, release date, cover image, artist, links, and channel selection are present, and return a validation error before any external call when required data is missing.
2. **Post generation**: render the image and text from the selected preset/template, collect warnings for optional missing fields, and store a preview snapshot with the exact payload that will be sent.
3. **Telegram delivery**: send through the Telegram Bot API with the saved account credentials, channel ID, idempotency key, generated caption, image, and formatting mode.
4. **Error handling**: normalize Telegram API failures into actionable categories: authentication, permissions, invalid channel, rate limit, malformed payload, media upload, network timeout, and unknown response.
5. **Retry policy**: retry only temporary failures such as `429`, `5xx`, connection reset, and timeout with exponential backoff, jitter, and a maximum attempt count. Do not retry permanent errors such as missing bot rights or invalid chat ID.
6. **Audit log**: persist every attempt with timestamp, release ID, account ID, request type, Telegram status code, normalized error code, safe error message, attempt number, next retry time, and final status.
7. **User feedback**: when publication fails, show the normalized reason, the failed step, the last safe Telegram message, and the suggested fix in the release panel.

## Saved Publication Accounts

After the first successful account binding, the account must be saved automatically and appear in a searchable dropdown.

Each saved account stores:

- avatar URL or generated placeholder;
- display name;
- username;
- type: `channel`, `group`, or `bot`;
- Telegram chat ID or bot ID;
- favorite flag;
- last used timestamp;
- permission status and last permission check result.

The dropdown groups accounts in this order: favorites, recently used, then all accounts. Each item shows avatar, display name, username, and type badge. Quick search matches display name, username, and type.

## Instant Settings Controls

The settings panel replaces modal-heavy editing with inline controls. Changes apply immediately and are autosaved.

| Setting               | Control             | Behavior                                                       |
| --------------------- | ------------------- | -------------------------------------------------------------- |
| Show logo             | Checkbox            | Toggles logo visibility in preview and payload.                |
| Add caption           | Checkbox            | Toggles post caption rendering.                                |
| Automatic publication | Checkbox            | Enables publication after approval.                            |
| Use template          | Checkbox + dropdown | Enables the selected text/image template.                      |
| Watermark             | Checkbox            | Toggles watermark layer.                                       |
| Compress images       | Checkbox            | Enables image compression before upload.                       |
| Crop image            | Checkbox            | Enables crop controls and preview overlay.                     |
| Use cache             | Checkbox            | Uses cached generated assets when source inputs are unchanged. |

Every control emits a settings patch, updates local state, refreshes the preview, writes an autosave record, and appends an undo-history entry.

## Fast Size and Layout Controls

Image and layout values use sliders and stepper buttons instead of raw numeric entry by default.

Supported quick controls:

- logo size;
- avatar size;
- cover size;
- release image size;
- watermark size;
- padding;
- border radius;
- opacity.

Each control exposes preset steps such as `24`, `32`, and `48` pixels where relevant, plus fine-grained slider movement. The current numeric value remains visible for precision.

## Live Preview

The right side of the panel must update without pressing Save and must show:

- final generated image;
- final post text;
- logo position and size;
- caption state;
- publication date;
- text formatting and link formatting;
- Telegram account target;
- validation and delivery warnings.

Preview rendering uses the same post-generation service as publication to avoid differences between preview and final delivery.

## Presets

The panel provides presets for Telegram, VK, Discord, Instagram, YouTube, and Custom. Selecting a preset applies all mapped defaults immediately: image ratio, caption rules, formatting mode, compression, watermark defaults, padding, border radius, and recommended account type.

## Autosave, Undo, and Redo

Every settings change creates an autosave revision and a history entry. Users can undo with `Ctrl+Z` and redo with `Ctrl+Shift+Z`.

History entries include setting key, previous value, next value, timestamp, actor, source control, and preview snapshot ID. The history panel shows the latest changes and allows restoring a specific revision.

## Acceptance Criteria

- Telegram publication failures identify the failing step and show a clear reason to the user.
- Temporary Telegram failures are retried, while permanent failures are not retried.
- Every Telegram attempt is captured in the publication error log.
- Bound accounts are saved automatically and are available in a searchable dropdown with favorites and recently used groups.
- Checkbox, dropdown, slider, stepper, and preset changes update the preview instantly and autosave without a Save button.
- Undo, redo, and history restore prior settings reliably.
