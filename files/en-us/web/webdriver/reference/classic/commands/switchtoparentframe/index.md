---
title: Switch To Parent Frame
short-title: Switch To Parent Frame
slug: Web/WebDriver/Reference/Classic/Commands/SwitchToParentFrame
page-type: webdriver-command
browser-compat: webdriver.classic.SwitchToParentFrame
sidebar: webdriver
---

The _Switch To Parent Frame_ [command](/en-US/docs/Web/WebDriver/Reference/Classic/Commands) of the [WebDriver](/en-US/docs/Web/WebDriver) API selects the parent of the current frame as the browsing context that later commands apply to. After [Switch To Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToFrame) selects a frame of the current document, this command selects that document again.

If the top-level browsing context is already selected, the command does nothing and returns `null`. If the current frame has been removed from its document, the command still selects its parent, as long as the parent is still open.

## Syntax

| Method                                                | URI template                         |
| ----------------------------------------------------- | ------------------------------------ |
| [`POST`](/en-US/docs/Web/HTTP/Reference/Methods/POST) | `/session/{session id}/frame/parent` |

### URL parameters

- `session id`
  - : Identifier of the session.

### Return value

`null` if successful.

### Errors

- [`invalid session id`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidSessionID)
  - : Session does not exist.
- `no such window`
  - : The [`window`](/en-US/docs/Web/API/Window) object has been discarded, indicating that the tab or window has been closed.
    This error is also returned if the parent of the current frame has been removed from its document.
- `unexpected alert open`
  - : A user prompt, such as [`window.alert`](/en-US/docs/Web/API/Window/alert), is open, and the session's user prompt handler reports it.
    The command then doesn't switch.
    This error isn't returned when the top-level browsing context is already selected.
    By default, the browser dismisses the prompt and returns this error, except for a `beforeunload` prompt, which it accepts without an error.
    The `unhandledPromptBehavior` [capability](/en-US/docs/Web/WebDriver/Reference/Capabilities) changes how prompts are handled.

## Examples

### Leaving a nested frame

With a WebDriver server running on `localhost:4444`, assume an active session has navigated to `https://example.org/checkout` using the [Navigate To](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NavigateTo) command. The page has an `<iframe>` that loads a payment form from `https://example.com/payment`, which has an `<iframe>` of its own that loads `https://example.com/terms`. Using [Switch To Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToFrame), you selected the payment form's frame. You then selected the terms frame within it.

To select the payment form's frame again, use the following command, replacing `ID` with the `sessionId` from the [New Session](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NewSession) response:

```bash
curl -i -H "Content-Type: application/json" -d '{}' http://localhost:4444/session/ID/frame/parent
```

The server responds with a `null` value to indicate success:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":null}
```

Commands now apply to the payment form's document. Sending the same command again selects the top-level document of the checkout page.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [Switch To Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToFrame) command
- [Switch To Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToWindow) command
- {{domxref("Window.parent")}}
