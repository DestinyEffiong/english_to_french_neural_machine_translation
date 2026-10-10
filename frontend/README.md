## 1. Overview

This directory contains the frontend for the English-to-French Neural Machine Translation application.

The frontend allows a user to:
- Enter English text.
- Request a French translation.
- Read the translated text.
- Copy the translation.
- Clear the input and output.

The frontend communicates with the FastAPI backend to perform translations. The translation itself is handled by the backend, not by the frontend.

## 2. Supported Translation Direction

The application supports one translation direction:

**English → French**

Language selection and translation in other directions are not supported.

## 3. Expected User Interface

The interface must provide:

1. An English text input area.
2. A **Translate** button.
3. An output area for the French translation.
4. A **Copy** button.
5. A **Clear** button.

The output area must be read-only. Users can copy the translation but cannot edit it directly.

## 4. Translation Behavior

### 4.1 Submitting a translation

When the user enters valid English text and clicks **Translate**:

1. Disable the Translate button to prevent repeated submissions while the request is running.
2. Show a skeleton placeholder in the output area to indicate that the translation is being processed.
3. Send a `POST` request to `/translate`.
4. Include the input text in the request body.

Expected request body:

```json
{
  "text": "Good morning. How are you?"
}
```

The backend URL must be configurable rather than permanently hardcoded into the frontend.

### 4.2 Displaying the result

When the backend returns a successful response:

1. Display the French translation in the output area.
2. Remove the loading placeholder.
3. Re-enable the Translate button.

Use the actual response format defined by the backend. Do not assume a response field name without checking the backend implementation.

### 4.3 Copying the translation

When the user clicks **Copy**:

1. Copy the displayed French translation to the clipboard.
2. Show the message `Copied!`.
3. Remove the message after approximately two seconds.

Do not show the success message if copying fails.

### 4.4 Clearing the text

When the user clicks **Clear**:

1. Empty the English input area.
2. Reset the output area to its original placeholder.
3. Clear any displayed translation-related status or error message.

### 4.5 Editing the input after translation

If the user edits the English input after a translation has been displayed, keep the existing French translation visible.

Do not automatically clear the translation when the input changes. The output changes only when the user clicks Translate or Clear.

## 5. Error Handling

Display the following messages for the corresponding backend responses or request failures.

| Condition | Message to display |
|---|---|
| HTTP 400 with detail `text missing, empty, or not a string` | Please enter English text. |
| HTTP 400 with detail `text exceeds 256 tokens` | Input exceeds the 256-token limit. |
| HTTP 500 | Translation service is unavailable. Please try again later. |
| Any other HTTP error status | Something went wrong. Please try again. |
| Request timeout | Request timed out. Please try again. |
| Network connection failure | Unable to connect to the server. Please check your connection. |

The frontend must identify the relevant error condition and display its corresponding message.

For HTTP 400 responses, check the backend's `detail` value to distinguish between the two specified errors.

After a failed translation request, remove the loading placeholder and allow the user to try again.

## 6. Responsive Layout

The interface must remain usable on:

- **Mobile:** Small phone screens.
- **Tablet:** Medium-sized screens.
- **Desktop:** Large screens.

Input and output areas must remain readable, and buttons must remain accessible without requiring horizontal scrolling.

## 7. Accessibility

All interactive controls must meet these requirements:

- Users can navigate to and operate controls using a keyboard.
- Focused controls have a visible focus indicator.
- Input and output areas have clear, programmatically associated labels.
- Text and controls have sufficient color contrast.
- Status and error messages are presented in a way that assistive technologies can identify.

Do not rely on color alone to communicate an error or success state.

## 8. Backend Integration

The frontend must communicate with the FastAPI backend using the documented translation endpoint.

**Endpoint:** `POST /translate`

**Request body:**

```json
{
  "text": "Your English text here"
}
```

Before implementing the integration, inspect the backend code to confirm:

- The complete backend URL and how it is configured.
- The expected request body.
- The actual successful response structure.
- The error response structure.

Do not invent response fields or assume the backend returns a particular JSON structure without verifying it.

## 9. Out of Scope

The frontend must not implement:

- User accounts or authentication.
- Translation history.
- File uploads.
- Language selection or language swapping.
- Text-to-speech.
- Translation between language pairs other than English → French.

Do not add these features unless the project requirements are formally changed.

## 10. Completion Checklist

A frontend implementation is ready for review when all applicable checks below pass.

- [ ] The user can enter English text.
- [ ] Clicking Translate sends the correct `POST /translate` request.
- [ ] The Translate button is disabled while the request is running.
- [ ] A skeleton placeholder appears while the request is running.
- [ ] Successful responses display the French translation.
- [ ] Copy copies the translation and displays `Copied!` for approximately two seconds.
- [ ] Clear resets the input and output.
- [ ] Editing the input does not automatically remove an existing translation.
- [ ] All specified backend errors display the correct messages.
- [ ] Timeout and connection failures display the correct messages.
- [ ] The user can retry after a failed request.
- [ ] The layout is usable on mobile, tablet, and desktop.
- [ ] All controls are keyboard accessible and have visible focus states.
- [ ] Labels and color contrast meet the accessibility requirements.
- [ ] No out-of-scope features have been introduced.

## 11. Contributor Guidance

Before making changes:

1. Read this README.
2. Inspect the existing frontend files.
3. Inspect the backend implementation to verify the API contract.
4. Make changes that satisfy the documented requirements.
5. Test the behavior you changed.
6. Submit a pull request describing the changes and how they were verified.

If the implementation conflicts with this README or the backend contract is unclear, raise the discrepancy before making assumptions.
