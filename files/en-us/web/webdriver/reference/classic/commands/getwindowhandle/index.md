---
title: Get Window Handle
short-title: Get Window Handle
slug: Web/WebDriver/Reference/Classic/Commands/GetWindowHandle
page-type: webdriver-command
browser-compat: webdriver.classic.GetWindowHandle
sidebar: webdriver
---

The _Get Window Handle_ [command](/en-US/docs/Web/WebDriver/Reference/Classic/Commands) of the [WebDriver](/en-US/docs/Web/WebDriver) API returns the window handle of the current top-level browsing context. A window handle is a string that uniquely identifies a window or tab.

The command returns the handle of the window or tab, even if the session has switched to a frame with [Switch To Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToFrame).

To select this window or tab again later, pass its handle to [Switch To Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToWindow). To get the handles of all open windows and tabs, use [Get Window Handles](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/GetWindowHandles).

The command doesn't handle user prompts: it returns the handle while a prompt is open, and the prompt stays open.

## Syntax

| Method                                              | URI template                   |
| --------------------------------------------------- | ------------------------------ |
| [`GET`](/en-US/docs/Web/HTTP/Reference/Methods/GET) | `/session/{session id}/window` |

### URL parameters

- `session id`
  - : Identifier of the session.

### Return value

A string containing the window handle of the current window or tab.

### Errors

- [`invalid session id`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidSessionID)
  - : Session does not exist.
- `no such window`
  - : The [`window`](/en-US/docs/Web/API/Window) object has been discarded, indicating that the tab or window has been closed.

## Examples

### Getting the handle of the current window

With a WebDriver server running on `localhost:4444`, assume an active session has been created. To get the handle of the current window, use the following command, replacing `ID` with the `sessionId` from the [New Session](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NewSession) response:

```bash
curl -i http://localhost:4444/session/ID/window
```

The server responds with the window handle:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":"93ee5bd6-d256-4608-a002-9a8995cc0e5f"}
```

If the session switches to a frame in this window with [Switch To Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToFrame), the command still returns the same handle.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [Get Window Handles](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/GetWindowHandles) command
- [Switch To Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToWindow) command
- [New Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NewWindow) command
- [Close Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/CloseWindow) command
