---
title: "`network` module"
short-title: network
slug: Web/WebDriver/Reference/BiDi/Modules/network
page-type: listing-page
browser-compat: webdriver.bidi.network
sidebar: webdriver
---

The **`network`** module contains commands and events related to network requests.

## Authentication

When a server asks for [authentication](/en-US/docs/Web/HTTP/Guides/Authentication) and the browser is about to ask for credentials, it sends the [`network.authRequired`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/network/authRequired) event.
The browser doesn't send it when it already has credentials for the realm, for example credentials provided earlier, and sends them with the request.

To answer the server from the client, add an intercept for the `authRequired` phase with [`network.addIntercept`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/network/addIntercept).
The request then waits until you send one of the following commands:

- [`network.continueWithAuth`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/network/continueWithAuth), to provide credentials, to go on without them so that the page receives the server's response, or to let the browser handle the challenge itself
- [`network.continueResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/network/continueResponse), to provide credentials or change the response
- [`network.provideResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/network/provideResponse), to replace the response

If the server rejects the credentials and asks again, you receive another `network.authRequired` event with the same request ID.

## Changing responses

Two commands change what the page receives for a blocked request:

- [`network.continueResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/network/continueResponse) changes the status, headers, or cookies of the response that the server sent, and keeps its body.
  It works for a request blocked in the `responseStarted` or `authRequired` phase.
- [`network.provideResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/network/provideResponse) provides a response of your own for a request blocked in any phase.
  In the `beforeRequestSent` phase, the browser doesn't send the request, and the page receives the response that you specify.
  In the later phases, the status, headers, cookies, and body that you specify replace those of the server's response.

## Commands

- [`network.continueResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/network/continueResponse)
- [`network.continueWithAuth`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/network/continueWithAuth)
- [`network.provideResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/network/provideResponse)

## Events

- [`network.authRequired`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/network/authRequired)

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}
