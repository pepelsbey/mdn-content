---
title: Find Elements From Element
short-title: Find Elements From Element
slug: Web/WebDriver/Reference/Classic/Commands/FindElementsFromElement
page-type: webdriver-command
browser-compat: webdriver.classic.FindElementsFromElement
sidebar: webdriver
---

The _Find Elements From Element_ [command](/en-US/docs/Web/WebDriver/Reference/Classic/Commands) of the [WebDriver](/en-US/docs/Web/WebDriver) API searches from the specified element for every element that matches the specified selector and returns references to them. This is similar to calling {{domxref("Element.querySelectorAll()")}} in JavaScript, with a choice of location strategies besides CSS selectors.

With the `css selector`, `link text`, `partial link text` and `tag name` strategies, only descendants of the element match, in document order. A CSS selector is still matched against the whole document: for example, `ul a` matches links within the element even when the `<ul>` element is one of its ancestors. With `xpath`, the element is the context node of the expression, which can select elements outside it: `.//a` selects the links within the element, while `//a` selects the links of the whole document, and `..` selects the element's parent. To get only the first matching element, use [Find Element From Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElementFromElement). To search the whole document, use [Find Elements](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElements).

If no element matches, the command keeps searching until at least one does or until the session's implicit wait timeout ends. This is the `implicit` value of the session's [timeouts](/en-US/docs/Web/WebDriver/Reference/Classic/Timeouts#implicit), which [Set Timeouts](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SetTimeouts) changes. It is `0` by default, so the command returns an empty array at once. If it is `null`, the command waits until an element matches.

## Syntax

| Method                                                | URI template                                          |
| ----------------------------------------------------- | ----------------------------------------------------- |
| [`POST`](/en-US/docs/Web/HTTP/Reference/Methods/POST) | `/session/{session id}/element/{element id}/elements` |

### URL parameters

- `session id`
  - : Identifier of the session.
- `element id`
  - : Identifier of the element within which to search: the value of the `element-6066-11e4-a52e-4f735466cecf` field of a web element reference, such as one returned by [Find Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElement).

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
    The element identified by `element id` is checked first.
- `no such element`
  - : The element identified by `element id` is not known in the current browsing context, for example because it was found in another frame, or the reference doesn't identify an element.
- `no such window`
  - : The [`window`](/en-US/docs/Web/API/Window) object has been discarded, indicating that the tab or window has been closed.
    This error is also returned if the current frame has been removed from its document.
- [`stale element reference`](/en-US/docs/Web/WebDriver/Reference/Errors/StaleElementReference)
  - : The element identified by `element id` is no longer in the document, for example because the page has been reloaded.
- `unexpected alert open`
  - : A user prompt, such as [`window.alert`](/en-US/docs/Web/API/Window/alert), is open, and the session's user prompt handler reports it.
    By default, the browser dismisses the prompt and returns this error, except for a `beforeunload` prompt, which it accepts without an error.
    The `unhandledPromptBehavior` [capability](/en-US/docs/Web/WebDriver/Reference/Capabilities) changes how prompts are handled.
    The prompt is handled before the element identified by `element id` is checked.

## Examples

### Finding all links within a list

With a WebDriver server running on `localhost:4444`, assume an active session has navigated to `https://example.org/products` using the [Navigate To](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NavigateTo) command. The page has a list of three products, each with a link, in a `<ul>` element with the ID `products`. A "View cart" link follows the list.

Assume also that you found the list using [Find Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElement) with the CSS selector `#products`, which returned a web element reference to it.

To find the links within the list, send the following request, replacing `ID` with the `sessionId` from the [New Session](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/NewSession) response and including the list's reference in the URL:

```bash
curl -i -H "Content-Type: application/json" \
  -d '{"using": "tag name", "value": "a"}' \
  http://localhost:4444/session/ID/element/3be28343-afd3-4dea-a2b6-a863fbbb80e1/elements
```

The server responds with an array of web element references to the three product links:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":[{"element-6066-11e4-a52e-4f735466cecf":"f5a28998-91ba-47c6-a022-d409cad945a5"},{"element-6066-11e4-a52e-4f735466cecf":"3aa8f199-1935-4b1c-a317-c1e05ed9ef45"},{"element-6066-11e4-a52e-4f735466cecf":"01a572df-d0f5-465d-9fed-06b702bcbb25"}]}
```

The "View cart" link isn't included, since it isn't within the list. [Find Elements](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElements) with the same strategy returns all four links of the page.

### Finding links by part of their text

Two of the products in the list are mugs: "Red mug" and "Blue mug". Using the same server, session, and list as in the first example, send the following request to find the links within the list whose text contains `mug`:

```bash
curl -i -H "Content-Type: application/json" \
  -d '{"using": "partial link text", "value": "mug"}' \
  http://localhost:4444/session/ID/element/3be28343-afd3-4dea-a2b6-a863fbbb80e1/elements
```

The server responds with references to the links of the first two products, the same references as in the first example:

```http
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8

{"value":[{"element-6066-11e4-a52e-4f735466cecf":"f5a28998-91ba-47c6-a022-d409cad945a5"},{"element-6066-11e4-a52e-4f735466cecf":"3aa8f199-1935-4b1c-a317-c1e05ed9ef45"}]}
```

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [Find Element From Element](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElementFromElement) command
- [Find Elements](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/FindElements) command
- [Set Timeouts](/en-US/docs/Web/WebDriver/Reference/Classic/Commands/SetTimeouts) command
- {{domxref("Element.querySelectorAll()")}}
