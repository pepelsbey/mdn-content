---
title: Maximize Window
short-title: Maximize Window
slug: Web/WebDriver/Reference/Classic/Commands/MaximizeWindow
page-type: webdriver-command
browser-compat: webdriver.classic.MaximizeWindow
sidebar: webdriver
---

The _Maximize Window_ [command](/en-US/docs/Web/WebDriver/Reference/Classic/Commands) of the [WebDriver](/en-US/docs/Web/WebDriver) API maximizes the operating system window that contains the current top-level browsing context. This typically enlarges the window to the largest size available on the screen, without making it fullscreen.

The command applies to the window of the current tab, even if the session has switched to a frame with [Switch To Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToFrame). If the page in the current tab is in fullscreen mode, for example after [Fullscreen Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FullscreenWindow) or {{domxref("Element.requestFullscreen()")}}, the browser exits fullscreen first. If the window is minimized, the browser restores it first. If the window manager has no concept of maximizing a window, the browser enlarges the window to the largest size that the window manager allows on the current screen.

The command returns once the window is maximized, or, if that takes too long, after a timeout that depends on the browser. Either way, it returns the window's size and position.

## Syntax

| Method                                                | URI template                            |
| ----------------------------------------------------- | --------------------------------------- |
| [`POST`](/en-US/docs/Web/HTTP/Reference/Methods/POST) | `/session/{session id}/window/maximize` |

### URL parameters

- `session id`
  - : Identifier of the session.

### Return value

An object that describes the size and position of the window.
It contains the following fields:

- `x`
  - : The horizontal position of the left edge of the window, in CSS pixels, relative to the screen.
    This is the same value as {{domxref("Window.screenX")}}.
- `y`
  - : The vertical position of the top edge of the window, in CSS pixels, relative to the screen.
    This is the same value as {{domxref("Window.screenY")}}.
- `width`
  - : The width of the window, in CSS pixels, including the browser's interface and the window's borders.
    This is the same value as {{domxref("Window.outerWidth")}}.
- `height`
  - : The height of the window, in CSS pixels, including the browser's interface and the window's borders.
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
  - : The browser can't maximize the window.
    A session whose `setWindowRect` [capability](/en-US/docs/Web/WebDriver/Reference/Capabilities) is `true` supports all the commands that resize and position windows, including this one.

## Examples

### Maximizing the window

With a WebDriver server running on `localhost:4444`, assume an active session has been created. To maximize the window, use the following command, replacing `ID` with the `sessionId` from the [New Session](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NewSession) response:

```bash
curl -i -H "Content-Type: application/json" -d '{}' http://localhost:4444/session/ID/window/maximize
```

Once the window is maximized, the server responds with its size and position:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":{"x":0,"y":0,"width":1366,"height":768}}
```

To give the window a size of your choice, use the [Set Window Rect](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SetWindowRect) command.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [Minimize Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/MinimizeWindow) command
- [Fullscreen Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FullscreenWindow) command
- [Get Window Rect](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/GetWindowRect) command
- [Set Window Rect](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SetWindowRect) command
