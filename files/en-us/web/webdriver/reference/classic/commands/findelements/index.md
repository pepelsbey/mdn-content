---
title: Find Elements
short-title: Find Elements
slug: Web/WebDriver/Reference/Classic/Commands/FindElements
page-type: webdriver-command
browser-compat: webdriver.classic.FindElements
sidebar: webdriver
---

The _Find Elements_ [command](/en-US/docs/Web/WebDriver/Reference/Classic/Commands) of the [WebDriver](/en-US/docs/Web/WebDriver) API finds every element in the current document that matches the specified selector and returns references to them. This is similar to calling {{domxref("Document.querySelectorAll()")}} in JavaScript, with a choice of location strategies besides CSS selectors.

The command searches the document of the current browsing context: the window or tab selected with [Switch To Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToWindow), or the frame selected with [Switch To Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToFrame). It doesn't search the documents of frames within it, or shadow trees. To get only the first matching element, use [Find Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElement). To search within an element, use [Find Elements From Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElementsFromElement).

If no element matches, the command keeps searching until at least one does or until the session's implicit wait timeout ends. This is the `implicit` value of the session's [timeouts](/en-US/docs/Web/WebDriver/Reference/Classic/Timeouts#implicit), which [Set Timeouts](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SetTimeouts) changes. It is `0` by default, so the command returns an empty array at once. If it is `null`, the command waits until an element matches.

## Syntax

| Method                                                | URI template                     |
| ----------------------------------------------------- | -------------------------------- |
| [`POST`](/en-US/docs/Web/HTTP/Reference/Methods/POST) | `/session/{session id}/elements` |

### URL parameters

- `session id`
  - : Identifier of the session.

### Payload

The input is an object with the following fields:

- `using`
  - : The location strategy, which determines how `value` is matched.
    It is one of the following strings:
    - `"css selector"`: `value` is a [CSS selector](/en-US/docs/Web/CSS/Guides/Selectors), matched as by {{domxref("Element.querySelectorAll()")}}.
    - `"link text"`: `value` is the text of a link.
      It matches {{HTMLElement("a")}} elements whose rendered text, with leading and trailing whitespace removed, is equal to `value`.
    - `"partial link text"`: `value` is part of the text of a link.
      It matches `<a>` elements whose rendered text contains `value`.
    - `"tag name"`: `value` is an element name, matched as by {{domxref("Element.getElementsByTagName()")}}.
    - `"xpath"`: `value` is an [XPath](/en-US/docs/Web/XML/XPath) expression, evaluated as by {{domxref("Document.evaluate()")}}.
      The expression must select only elements.

    The rendered text of a link is its text as displayed, as returned by [Get Element Text](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/GetElementText).
    For example, a link styled with `text-transform: uppercase` matches only an uppercase `value`.

- `value`
  - : A string containing the selector, matched according to `using`.

### Return value

An array of web element references, one for each matching element, in document order.
The array is empty if no element matches by the time the implicit wait timeout ends.

A web element reference is an object with a single field, `element-6066-11e4-a52e-4f735466cecf`, whose value is a string identifying the element.
Element commands take this string in their URL as the `element id` parameter.
Commands such as [Switch To Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToFrame) and [Execute Script](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/ExecuteScript) take the whole object in their payload.

Within a session, an element always has the same reference.
The reference works only in the browsing context where it was returned: in another frame or window, element commands return a `no such element` error for it.
If the element is no longer in its document, or its document is no longer the one displayed (for example after a reload or a navigation), they return a [`stale element reference`](/en-US/docs/Web/WebDriver/Reference/Errors/StaleElementReference) error for it.

### Errors

- [`invalid session id`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidSessionID)
  - : Session does not exist.
- [`invalid argument`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidArgument)
  - : The `using` field is missing or isn't one of the location strategies, or the `value` field is missing.
- [`invalid selector`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidSelector)
  - : The `value` isn't a valid CSS selector or XPath expression.
    This error is also returned if an XPath expression selects nodes other than elements, such as attributes or text.
- `no such element`
  - : The current document has no root element.
- `no such window`
  - : The [`window`](/en-US/docs/Web/API/Window) object has been discarded, indicating that the tab or window has been closed.
    This error is also returned if the current frame has been removed from its document.
- `unexpected alert open`
  - : A user prompt, such as [`window.alert`](/en-US/docs/Web/API/Window/alert), is open, and the session's user prompt handler reports it.
    By default, the browser dismisses the prompt and returns this error, except for a `beforeunload` prompt, which it accepts without an error.
    The `unhandledPromptBehavior` [capability](/en-US/docs/Web/WebDriver/Reference/Capabilities) changes how prompts are handled.

## Examples

### Finding all elements that match a CSS selector

With a WebDriver server running on `localhost:4444`, assume an active session has navigated to `https://example.org/products` using the [Navigate To](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NavigateTo) command. The page lists three products, each in an `<li>` element with the `product` class.

To find every product, send a CSS selector in the request payload, replacing `ID` with the `sessionId` from the [New Session](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NewSession) response:

```bash
curl -i -H "Content-Type: application/json" \
  -d '{"using": "css selector", "value": "li.product"}' \
  http://localhost:4444/session/ID/elements
```

The server responds with an array of three web element references, one for each product, in the order they appear on the page:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":[{"element-6066-11e4-a52e-4f735466cecf":"3be28343-afd3-4dea-a2b6-a863fbbb80e1"},{"element-6066-11e4-a52e-4f735466cecf":"f5a28998-91ba-47c6-a022-d409cad945a5"},{"element-6066-11e4-a52e-4f735466cecf":"3aa8f199-1935-4b1c-a317-c1e05ed9ef45"}]}
```

### Finding elements when none match

Using the same server and session as in the first example, none of the page's buttons is disabled. Send the following XPath expression to find disabled buttons:

```bash
curl -i -H "Content-Type: application/json" \
  -d '{"using": "xpath", "value": "//button[@disabled]"}' \
  http://localhost:4444/session/ID/elements
```

The server responds with an empty array:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":[]}
```

Unlike [Find Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElement), which returns a `no such element` error when no element matches, Find Elements succeeds with no references.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [Find Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElement) command
- [Find Elements From Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElementsFromElement) command
- [Set Timeouts](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SetTimeouts) command
- [`browsingContext.locateNodes`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/locateNodes) BiDi command
