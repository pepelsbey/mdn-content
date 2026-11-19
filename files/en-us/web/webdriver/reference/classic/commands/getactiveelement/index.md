---
title: Get Active Element
short-title: Get Active Element
slug: Web/WebDriver/Reference/Classic/Commands/GetActiveElement
page-type: webdriver-command
browser-compat: webdriver.classic.GetActiveElement
sidebar: webdriver
---

The _Get Active Element_ [command](/en-US/docs/Web/WebDriver/Reference/Classic/Commands) of the [WebDriver](/en-US/docs/Web/WebDriver) API returns a reference to the element of the current document that has focus. This is equivalent to reading the {{domxref("Document.activeElement")}} property in JavaScript.

If no element has focus, the command usually returns the document's {{HTMLElement("body")}} element.

The command reads the document of the current browsing context: the window or tab selected with [Switch To Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToWindow), or the frame selected with [Switch To Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToFrame). If the focused element is in a frame within it, the command returns the element of the current document that holds the frame, such as an {{HTMLElement("iframe")}} element. To get the element that has focus within the frame, switch to the frame first, one level at a time for nested frames.

## Syntax

| Method                                              | URI template                           |
| --------------------------------------------------- | -------------------------------------- |
| [`GET`](/en-US/docs/Web/HTTP/Reference/Methods/GET) | `/session/{session id}/element/active` |

### URL parameters

- `session id`
  - : Identifier of the session.

### Return value

A web element reference to the active element.

A web element reference is an object with a single field, `element-6066-11e4-a52e-4f735466cecf`, whose value is a string identifying the element.
Element commands take this string in their URL as the `element id` parameter.
Commands such as [Switch To Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToFrame) and [Execute Script](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/ExecuteScript) take the whole object in their payload.

Within a session, an element always has the same reference.
The reference works only in the browsing context where it was returned: in another frame or window, element commands return a `no such element` error for it.
If the element is no longer in its document, or its document is no longer the one displayed (for example after a reload or a navigation), they return a [`stale element reference`](/en-US/docs/Web/WebDriver/Reference/Errors/StaleElementReference) error for it.

### Errors

- [`invalid session id`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidSessionID)
  - : Session does not exist.
- `no such element`
  - : The document has no active element: {{domxref("Document.activeElement")}} is `null`.
- `no such window`
  - : The [`window`](/en-US/docs/Web/API/Window) object has been discarded, indicating that the tab or window has been closed.
    This error is also returned if the current frame has been removed from its document.
- `unexpected alert open`
  - : A user prompt, such as [`window.alert`](/en-US/docs/Web/API/Window/alert), is open, and the session's user prompt handler reports it.
    By default, the browser dismisses the prompt and returns this error, except for a `beforeunload` prompt, which it accepts without an error.
    The `unhandledPromptBehavior` [capability](/en-US/docs/Web/WebDriver/Reference/Capabilities) changes how prompts are handled.

## Examples

### Getting the focused element

With a WebDriver server running on `localhost:4444`, assume an active session has navigated to `https://example.org/products` using the [Navigate To](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NavigateTo) command. The page has a search field with the [`autofocus`](/en-US/docs/Web/HTML/Reference/Global_attributes/autofocus) attribute, so the field has focus once the page has loaded.

To get the focused element, send the following request, replacing `ID` with the `sessionId` from the [New Session](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NewSession) response:

```bash
curl -i http://localhost:4444/session/ID/element/active
```

The server responds with a web element reference to the search field:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":{"element-6066-11e4-a52e-4f735466cecf":"3be28343-afd3-4dea-a2b6-a863fbbb80e1"}}
```

[Find Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElement) with the CSS selector `input[type=search]` returns the same reference.

### Getting the active element when nothing has focus

Using the same server and session as in the first example, assume the session then navigated to `https://example.org/cart`, a page with no focusable element. Send the same request:

```bash
curl -i http://localhost:4444/session/ID/element/active
```

The server responds with a web element reference to the page's `<body>` element:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":{"element-6066-11e4-a52e-4f735466cecf":"f5a28998-91ba-47c6-a022-d409cad945a5"}}
```

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [Find Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElement) command
- [Switch To Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToFrame) command
- {{domxref("Document.activeElement")}}
