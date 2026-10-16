---
title: "`bluetooth.simulateGattConnectionResponse` command"
short-title: simulateGattConnectionResponse
slug: Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse
page-type: webdriver-command
status:
  - experimental
browser-compat: webdriver.bidi.bluetooth.simulateGattConnectionResponse
sidebar: webdriver
---

{{SeeCompatTable}}

The `bluetooth.simulateGattConnectionResponse` [command](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules#commands) of the [`bluetooth`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth) module answers a page's attempt to connect to a simulated Bluetooth device, by simulating the device's response.

## Syntax

```json-nolint
{
  "method": "bluetooth.simulateGattConnectionResponse",
  "params": {
    "address": "09:09:09:09:09:09",
    "code": 0,
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f"
  }
}
```

### Parameters

The `params` field contains:

- `address`
  - : A string that contains the Bluetooth address of the simulated device, such as one that you added with [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral).
- `code`
  - : A non-negative integer that specifies the response code, one of the HCI error codes defined in the Bluetooth Core Specification (Vol 2, Part D, "List of Error Codes").
    - `0`: The connection succeeds: unless the page called {{domxref("BluetoothRemoteGATTServer.disconnect()", "disconnect()")}} in the meantime, {{domxref("BluetoothRemoteGATTServer.connect()", "connect()")}} resolves, and {{domxref("BluetoothRemoteGATTServer.connected", "connected")}} is `true`.
    - Any other value: The connection fails, and `connect()` rejects with a `NetworkError` {{domxref("DOMException")}}.
- `context`
  - : A string that contains the ID of the [top-level context](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext#top-level_context) whose [simulated adapter](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#simulated_adapter) has the device.
    When the page in a top-level context calls `connect()`, this is the `context` value of the `bluetooth.gattConnectionAttempted` event for the attempt.

### Return value

The `result` field in the response is an empty object (`{}`).

### Errors

- [`invalid argument`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidArgument)
  - : Thrown in any of the following cases:
    - A parameter is missing or has an invalid type, or `code` is not a non-negative integer.
    - `context` is not a top-level context, or the context has no simulated adapter.
    - The simulated adapter has no device with the specified `address`.
- `invalid element state`
  - : No connection attempt to the device is waiting for a response in the specified context.
- `no such frame`
  - : No context with the specified `context` ID is found.

## Description

When a page calls {{domxref("BluetoothRemoteGATTServer.connect()", "device.gatt.connect()")}} for a device that isn't connected, and its top-level context has a [simulated adapter](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#simulated_adapter), the [connection attempt](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#gatt_connections) waits for a simulated response.
If you're [subscribed](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to `bluetooth.gattConnectionAttempted`, you receive an event with the ID of the context in which the page called `connect()` and the `address` of the device.
The `connect()` call waits until you answer the attempt with this command, which sets the outcome for this attempt only.
To fail the attempt as if the device couldn't respond, you can also use [`bluetooth.simulateGattDisconnection`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattDisconnection).

If the device is already connected, `connect()` resolves at once, without an event.

## Examples

### Accepting a connection attempt

Assume you have a [WebDriver BiDi connection](/en-US/docs/Web/WebDriver/How_to/Create_BiDi_connection), an [active session](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/new), and a [subscription](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to `bluetooth.requestDevicePromptUpdated` and `bluetooth.gattConnectionAttempted`.
Suppose the tab with the context ID `93ee5bd6-d256-4608-a002-9a8995cc0e5f` has a page from `https://example.com`, and you simulated a powered-on adapter for it with [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter).
You added a heart rate monitor with the address `09:09:09:09:09:09` with `bluetooth.simulatePreconnectedPeripheral`.
The page called {{domxref("Bluetooth.requestDevice()", "navigator.bluetooth.requestDevice()")}} in response to a user's action, and you chose the monitor in the [device prompt](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_device_prompt) with `bluetooth.handleRequestDevicePrompt`.

When the page calls `device.gatt.connect()`, the browser sends the following event:

```json
{
  "type": "event",
  "method": "bluetooth.gattConnectionAttempted",
  "params": {
    "address": "09:09:09:09:09:09",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f"
  }
}
```

Send the following message to let the connection succeed:

```json
{
  "id": 1,
  "method": "bluetooth.simulateGattConnectionResponse",
  "params": {
    "address": "09:09:09:09:09:09",
    "code": 0,
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f"
  }
}
```

The browser responds with an empty `result` object:

```json
{
  "id": 1,
  "type": "success",
  "result": {}
}
```

`connect()` resolves, and `device.gatt.connected` is `true`.

### Refusing a connection attempt

Using the same connection, session, and subscription, suppose you disconnected the device with [`bluetooth.simulateGattDisconnection`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattDisconnection), which returned an empty `result` object, and the page calls `device.gatt.connect()` again.
The browser sends another `bluetooth.gattConnectionAttempted` event for the device.

To test how the page handles a failed connection, send the following message with the HCI error code `0x08` (Connection Timeout):

```json
{
  "id": 3,
  "method": "bluetooth.simulateGattConnectionResponse",
  "params": {
    "address": "09:09:09:09:09:09",
    "code": 8,
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f"
  }
}
```

The browser responds with an empty `result` object:

```json
{
  "id": 3,
  "type": "success",
  "result": {}
}
```

`connect()` rejects with a `NetworkError` `DOMException`, and `device.gatt.connected` is `false`.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral) command
- [`bluetooth.simulateGattDisconnection`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattDisconnection) command
- {{domxref("BluetoothRemoteGATTServer.connect()")}} method
