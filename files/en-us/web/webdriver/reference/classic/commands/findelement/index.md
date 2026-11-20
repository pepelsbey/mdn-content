---
title: Find Element
short-title: Find Element
slug: Web/WebDriver/Reference/Classic/Commands/FindElement
page-type: webdriver-command
browser-compat: webdriver.classic.FindElement
sidebar: webdriver
---

The _Find Element_ [command](/en-US/docs/Web/WebDriver/Reference/Classic/Commands) of the [WebDriver](/en-US/docs/Web/WebDriver) API finds the first element in the current document that matches the specified selector and returns a reference to it. This is similar to calling {{domxref("Document.querySelector()")}} in JavaScript, with a choice of location strategies besides CSS selectors.

The command searches the document of the current browsing context: the window or tab selected with [Switch To Window](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToWindow), or the frame selected with [Switch To Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToFrame). It doesn't search the documents of frames within it, or shadow trees. Elements are matched in document order.

To get every matching element, use [Find Elements](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElements). To search within an element, use [Find Element From Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElementFromElement).

If no element matches, the command keeps searching until one does or until the session's implicit wait timeout ends. This is the `implicit` value of the session's [timeouts](/en-US/docs/Web/WebDriver/Reference/Classic/Timeouts#implicit), which [Set Timeouts](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SetTimeouts) changes. The timeout is `0` by default, so the command returns a `no such element` error at once. If the timeout is `null`, the command waits until an element matches.

## Syntax

| Method                                                | URI template                    |
| ----------------------------------------------------- | ------------------------------- |
| [`POST`](/en-US/docs/Web/HTTP/Reference/Methods/POST) | `/session/{session id}/element` |

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

A web element reference to the first matching element.

A web element reference is an object with a single field, `element-6066-11e4-a52e-4f735466cecf`, whose value is a string identifying the element.
Element commands take this string in their URL as the `element id` parameter.
Commands such as [Switch To Frame](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SwitchToFrame) and [Execute Script](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/ExecuteScript) take the whole object in their payload.

Within a session, an element always has the same reference.
The reference works only in the browsing context where it was returned: in another frame or window, element commands return a `no such element` error for it.
If the element is no longer in its document, or its document is no longer the one displayed (for example after a reload or a navigation), element commands return a [`stale element reference`](/en-US/docs/Web/WebDriver/Reference/Errors/StaleElementReference) error for it.

### Errors

- [`invalid session id`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidSessionID)
  - : Session does not exist.
- [`invalid argument`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidArgument)
  - : The `using` field is missing or isn't one of the location strategies, or the `value` field is missing.
- [`invalid selector`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidSelector)
  - : The `value` isn't a valid CSS selector or XPath expression.
    This error is also returned if an XPath expression selects nodes other than elements, such as attributes or text.
- `no such element`
  - : No element matches the selector, and the implicit wait timeout has ended.
    This error is also returned if the current document has no root element.
- `no such window`
  - : The [`window`](/en-US/docs/Web/API/Window) object has been discarded, indicating that the tab or window has been closed.
    This error is also returned if the current frame has been removed from its document.
- `unexpected alert open`
  - : A user prompt, such as [`window.alert`](/en-US/docs/Web/API/Window/alert), is open, and the session's user prompt handler reports it.
    By default, the browser dismisses the prompt and returns this error, except for a `beforeunload` prompt, which it accepts without an error.
    The `unhandledPromptBehavior` [capability](/en-US/docs/Web/WebDriver/Reference/Capabilities) changes how prompts are handled.

## Examples

### Finding an element by CSS selector

With a WebDriver server running on `localhost:4444`, assume an active session has navigated to `https://example.org/products` using the [Navigate To](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NavigateTo) command. The page has a search form with a search field, followed by a list of products.

To find the search field, send a CSS selector in the request payload, replacing `ID` with the `sessionId` from the [New Session](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NewSession) response:

```bash
curl -i -H "Content-Type: application/json" \
  -d '{"using": "css selector", "value": "input[type=search]"}' \
  http://localhost:4444/session/ID/element
```

The server responds with a web element reference to the search field:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":{"element-6066-11e4-a52e-4f735466cecf":"3be28343-afd3-4dea-a2b6-a863fbbb80e1"}}
```

Finding the field again, with this or another selector, returns the same reference.

### Finding a link by its text

The page also has a "View cart" link after the list of products. Using the same server and session as in the first example, send the link's text with the `link text` strategy to find it:

```bash
curl -i -H "Content-Type: application/json" \
  -d '{"using": "link text", "value": "View cart"}' \
  http://localhost:4444/session/ID/element
```

The server responds with a web element reference to the link:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":{"element-6066-11e4-a52e-4f735466cecf":"f5a28998-91ba-47c6-a022-d409cad945a5"}}
```

The `link text` strategy matches the whole text of a link. To match part of it, use `partial link text`: for example, Find Element with the value `"mug"` returns the "Red mug" link, the first of the links whose text contains `mug`.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [Find Elements](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElements) command
- [Find Element From Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElementFromElement) command
- [Get Active Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/GetActiveElement) command
- [Set Timeouts](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SetTimeouts) command
- [`browsingContext.locateNodes`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/locateNodes) BiDi command
