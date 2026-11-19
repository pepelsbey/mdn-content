---
title: Switch To Frame
short-title: Switch To Frame
slug: Web/WebDriver/Reference/Classic/Commands/SwitchToFrame
page-type: webdriver-command
browser-compat: webdriver.classic.SwitchToFrame
sidebar: webdriver
---

The _Switch To Frame_ [command](/en-US/docs/Web/WebDriver/Reference/Classic/Commands) of the [WebDriver](/en-US/docs/Web/WebDriver) API selects the frame that later commands apply to. It selects a frame of the current document, or the top-level browsing context of the current window or tab.

With an index or an element, the command selects a frame one level down: a frame within a frame takes one command for each level. To go back one level, use [Switch To Parent Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToParentFrame). To go back to the top-level document from any level, send `null`.

If the command succeeds, later commands such as [Find Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElement) and [Execute Script](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/ExecuteScript) apply to the document of the selected frame. Commands that are about the top-level document, such as [Get Title](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/GetTitle) and [Get Current URL](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/GetCurrentURL), still report the top-level document.

WebDriver isn't bound by the [same-origin policy](/en-US/docs/Web/Security/Defenses/Same-origin_policy), so you can switch to a frame whose document comes from another origin.

## Syntax

| Method                                                | URI template                  |
| ----------------------------------------------------- | ----------------------------- |
| [`POST`](/en-US/docs/Web/HTTP/Reference/Methods/POST) | `/session/{session id}/frame` |

### URL parameters

- `session id`
  - : Identifier of the session.

### Payload

The input is an object:

- `id`
  - : Identifies the frame to select. It is one of the following:
    - `null`, to select the top-level browsing context of the current window or tab.
    - An integer from 0 to 65535, the index of a frame of the current document, in the order of {{domxref("Window.frames")}}.
      `0` is the first frame.
    - A web element reference to an {{HTMLElement("iframe")}} or {{HTMLElement("frame")}} element of the current document, such as one returned by [Find Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElement).
      This is an object whose `element-6066-11e4-a52e-4f735466cecf` field holds a string identifying the element.

### Return value

`null` if successful.

### Errors

- [`invalid session id`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidSessionID)
  - : Session does not exist.
- [`invalid argument`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidArgument)
  - : The `id` field is missing, is not `null`, a number, or a web element reference, or is a number less than 0 or greater than 65535.
- `no such element`
  - : The element referenced in `id` is not known in the current browsing context, for example because it was found in another document, such as the parent document, or the reference doesn't identify an element.
- `no such frame`
  - : The current document has no frame at the index specified in `id`, or the element referenced in `id` is not an `<iframe>` or `<frame>` element.
- `no such window`
  - : The [`window`](/en-US/docs/Web/API/Window) object has been discarded, indicating that the tab or window has been closed.
    When `id` is a number or a web element reference, this error is also returned if the current frame has been removed from its document.
- [`stale element reference`](/en-US/docs/Web/WebDriver/Reference/Errors/StaleElementReference)
  - : The element referenced in `id` is no longer in the document, for example because the page has been reloaded.
- `unexpected alert open`
  - : A user prompt, such as [`window.alert`](/en-US/docs/Web/API/Window/alert), is open, and the session's user prompt handler reports it.
    The command then doesn't switch.
    By default, the browser dismisses the prompt and returns this error, except for a `beforeunload` prompt, which it accepts without an error.
    The `unhandledPromptBehavior` [capability](/en-US/docs/Web/WebDriver/Reference/Capabilities) changes how prompts are handled.

## Examples

### Switching to a frame by its index

With a WebDriver server running on `localhost:4444`, assume an active session has navigated to `https://example.org/checkout` using the [Navigate To](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NavigateTo) command. The page has a single `<iframe>`, which loads a payment form from `https://example.com/payment`. The form's card number field is in the frame's document. While the top-level document is selected, [Find Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElement) returns a `no such element` error for it.

To switch to the frame, send its index, `0`, in the request payload, replacing `ID` with the `sessionId` from the [New Session](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NewSession) response:

```bash
curl -i -H "Content-Type: application/json" \
  -d '{"id": 0}' \
  http://localhost:4444/session/ID/frame
```

The server responds with a `null` value to indicate success:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":null}
```

Find Element now searches the payment form's document, and finds the card number field. The frame's document comes from a different origin than the page's, which doesn't prevent the switch.

### Switching back to the top-level document

Using the same server and session as in the first example, with the payment form's frame selected, send `null` to select the top-level document of the tab again:

```bash
curl -i -H "Content-Type: application/json" \
  -d '{"id": null}' \
  http://localhost:4444/session/ID/frame
```

The server responds with a `null` value to indicate success:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":null}
```

Commands apply to the checkout page again.

### Switching to a frame by its element

Using the same server and session as in the previous examples, the top-level document is selected again. Assume you found the `<iframe>` element using [Find Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElement) with the CSS selector `iframe#payment`, which returned a web element reference. To switch to the frame, send the reference in `id`:

```bash
curl -i -H "Content-Type: application/json" \
  -d '{"id": {"element-6066-11e4-a52e-4f735466cecf": "3be28343-afd3-4dea-a2b6-a863fbbb80e1"}}' \
  http://localhost:4444/session/ID/frame
```

The server responds with a `null` value to indicate success:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":null}
```

Commands now apply to the payment form's document, as in the first example.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [Switch To Parent Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToParentFrame) command
- [Switch To Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToWindow) command
- [Find Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElement) command
- {{domxref("Window.frames")}}
