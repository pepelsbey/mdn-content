---
title: webSocketUrl
slug: Web/WebDriver/Reference/Capabilities/webSocketUrl
page-type: webdriver-capability
sidebar: webdriver
---

With the `webSocketUrl` capability set to `true` a WebSocket server will be started in the browser, supporting bidirectional communication by using the [WebDriver BiDi protocol](https://w3c.github.io/webdriver-bidi/).
When the [New Session](/en-US/docs/Web/WebDriver/Reference/Commands/NewSession) request has the `webSocketUrl` capability set to `true`, and the session starts successfully, the value of the `capabilities` field in the response will have a `webSocketUrl` property set to the URL of the WebSocket server.

## Example

Requesting the WebSocket URL by setting the `webSocketUrl` capability to `true`:

Request:

```http
POST /session HTTP/1.1
{"capabilities": {"alwaysMatch": {"webSocketUrl": true}}}
```

Response:

```json
{"value":{"capabilities":{"webSocketUrl":"ws://localhost:9222/session/571f206f-c3fe-794c-9218-77fa89595eb9", [..]}, "sessionId":"571f206f-c3fe-794c-9218-77fa89595eb9"}}
```

## See also

- [List of WebDriver capabilities](/en-US/docs/Web/WebDriver/Reference/Capabilities)
- [New Session](/en-US/docs/Web/WebDriver/Reference/Commands/NewSession) command
- [Establishing a WebDriver BiDi connection](https://w3c.github.io/webdriver-bidi/#establishing)
