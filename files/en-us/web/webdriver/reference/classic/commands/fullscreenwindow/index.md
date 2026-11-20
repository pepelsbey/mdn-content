---
title: Fullscreen Window
short-title: Fullscreen Window
slug: Web/WebDriver/Reference/Classic/Commands/FullscreenWindow
page-type: webdriver-command
browser-compat: webdriver.classic.FullscreenWindow
sidebar: webdriver
---

The _Fullscreen Window_ [command](/en-US/docs/Web/WebDriver/Reference/Classic/Commands) of the [WebDriver](/en-US/docs/Web/WebDriver) API makes the operating system window that contains the current top-level browsing context fullscreen.

The command applies to the window of the current tab, even if the session has switched to a frame with [Switch To Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToFrame).

If the window is minimized, the browser restores it first. The root element of the top-level document (the {{HTMLElement("html")}} element of an HTML page) then becomes the document's {{domxref("Document.fullscreenElement")}}, similar to calling {{domxref("Element.requestFullscreen()")}} on the root element, but without the user activation that method requires. The window is then in fullscreen mode.

To leave fullscreen mode, use [Maximize Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/MaximizeWindow), [Minimize Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/MinimizeWindow) or [Set Window Rect](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SetWindowRect), which exit fullscreen first.

## Syntax

| Method                                                | URI template                              |
| ----------------------------------------------------- | ----------------------------------------- |
| [`POST`](/en-US/docs/Web/HTTP/Reference/Methods/POST) | `/session/{session id}/window/fullscreen` |

### URL parameters

- `session id`
  - : Identifier of the session.

### Return value

An object that describes the size and position of the window.
It contains the following fields:

- `x`
  - : The horizontal position of the left edge of the window, in {{glossary("CSS pixel", "CSS pixels")}}, relative to the screen.
    This is the same value as {{domxref("Window.screenX")}}.
- `y`
  - : The vertical position of the top edge of the window, in {{glossary("CSS pixel", "CSS pixels")}}, relative to the screen.
    This is the same value as {{domxref("Window.screenY")}}.
- `width`
  - : The width of the window, in {{glossary("CSS pixel", "CSS pixels")}}, including the browser's interface and the window's borders.
    This is the same value as {{domxref("Window.outerWidth")}}.
- `height`
  - : The height of the window, in {{glossary("CSS pixel", "CSS pixels")}}, including the browser's interface and the window's borders.
    This is the same value as {{domxref("Window.outerHeight")}}.

### Errors

- [`invalid session id`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidSessionID)
  - : Session does not exist.
- `no such window`
  - : The [`window`](/en-US/docs/Web/API/Window) object has been discarded, indicating that the tab or window has been closed.
- `unexpected alert open`
  - : A user prompt, such as [`window.alert`](/en-US/docs/Web/API/Window/alert), is open, and the session's user prompt handler reports it.
    The command then doesn't change the window.
    By default, the browser dismisses the prompt and returns this error, except for a `beforeunload` prompt, which it accepts without an error.
    The `unhandledPromptBehavior` [capability](/en-US/docs/Web/WebDriver/Reference/Capabilities) changes how prompts are handled.
- `unsupported operation`
  - : The browser doesn't support fullscreen mode.
    A session whose `setWindowRect` [capability](/en-US/docs/Web/WebDriver/Reference/Capabilities) is `true` supports all the commands that resize and position windows, including this one.

## Examples

### Making the window fullscreen

With a WebDriver server running on `localhost:4444`, assume an active session has been created. To make the window fullscreen, use the following command, replacing `ID` with the `sessionId` from the [New Session](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NewSession) response:

```bash
curl -i -H "Content-Type: application/json" -d '{}' http://localhost:4444/session/ID/window/fullscreen
```

The server responds with the size and position of the window in fullscreen mode:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":{"x":0,"y":0,"width":1366,"height":768}}
```

The window now covers the whole screen.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [Maximize Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/MaximizeWindow) command
- [Minimize Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/MinimizeWindow) command
- [Get Window Rect](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/GetWindowRect) command
- [Set Window Rect](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SetWindowRect) command
- [Fullscreen API](/en-US/docs/Web/API/Fullscreen_API)
