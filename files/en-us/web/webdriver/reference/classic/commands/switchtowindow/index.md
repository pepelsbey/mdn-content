---
title: Switch To Window
short-title: Switch To Window
slug: Web/WebDriver/Reference/Classic/Commands/SwitchToWindow
page-type: webdriver-command
browser-compat: webdriver.classic.SwitchToWindow
sidebar: webdriver
---

The _Switch To Window_ [command](/en-US/docs/Web/WebDriver/Reference/Classic/Commands) of the [WebDriver](/en-US/docs/Web/WebDriver) API selects the window or tab that later commands apply to, given its window handle. The selected window or tab becomes the session's current top-level browsing context.

If the command succeeds, later commands apply to the top-level document of the selected window or tab, even if the session was in a frame of that window before. In a tabbed browser, the selected tab typically becomes the visible tab of its window, but the command doesn't change which operating system window has focus.

To get the handles of the open windows and tabs, use [Get Window Handles](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/GetWindowHandles). [New Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NewWindow) returns the handle of the window or tab it opens, but doesn't select it.

The command also works when the current window or tab has been closed, so after [Close Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/CloseWindow) you can use it to select one of the remaining windows or tabs.

The command doesn't handle user prompts. If a prompt is open in the window or tab you leave and doesn't prevent the switch, the prompt stays open and is still there when you switch back.

## Syntax

| Method                                                | URI template                   |
| ----------------------------------------------------- | ------------------------------ |
| [`POST`](/en-US/docs/Web/HTTP/Reference/Methods/POST) | `/session/{session id}/window` |

### URL parameters

- `session id`
  - : Identifier of the session.

### Payload

The input is an object:

- `handle`
  - : A string containing the window handle of the window or tab to select, as returned by [Get Window Handle](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/GetWindowHandle) or [Get Window Handles](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/GetWindowHandles), or in the `handle` field of the [New Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NewWindow) response.

### Return value

`null` if successful.

### Errors

- [`invalid session id`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidSessionID)
  - : Session does not exist.
- [`invalid argument`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidArgument)
  - : The `handle` field is missing.
- `no such window`
  - : No window or tab has the specified handle.
- `unexpected alert open`
  - : A user prompt, such as [`window.alert`](/en-US/docs/Web/API/Window/alert), is open and prevents the browser from selecting another window or tab.
    The command then doesn't switch, and the prompt stays open.

## Examples

### Switching to a new tab

With a WebDriver server running on `localhost:4444`, assume an active session has navigated to `https://example.org/` using the [Navigate To](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NavigateTo) command. Assume also that you then opened a second tab using the [New Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NewWindow) command, which returned the handle of the tab. To switch to the new tab, send its handle in the request payload, replacing `ID` with the `sessionId` from the [New Session](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NewSession) response:

```bash
curl -i -H "Content-Type: application/json" \
  -d '{"handle": "5e5e96e8-5247-4f22-9b35-a4a2d841cbaa"}' \
  http://localhost:4444/session/ID/window
```

The server responds with a `null` value to indicate success:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":null}
```

Later commands apply to the new tab. For example, [Get Window Handle](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/GetWindowHandle) now returns its handle, and [Navigate To](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NavigateTo) loads a page in it.

### Switching back to the first tab

Using the same server, session, and tabs as in the first example, assume you saved the handle of the first tab using [Get Window Handle](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/GetWindowHandle) before you opened the second one. To switch back to the first tab, send its handle:

```bash
curl -i -H "Content-Type: application/json" \
  -d '{"handle": "93ee5bd6-d256-4608-a002-9a8995cc0e5f"}' \
  http://localhost:4444/session/ID/window
```

The server responds with a `null` value to indicate success:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":null}
```

Commands apply to the first tab again, which still shows `https://example.org/`. For example, [Get Title](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/GetTitle) returns the title of that page.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [Get Window Handle](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/GetWindowHandle) command
- [Get Window Handles](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/GetWindowHandles) command
- [New Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NewWindow) command
- [Close Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/CloseWindow) command
- [Switch To Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToFrame) command
