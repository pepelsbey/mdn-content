---
title: "`bluetooth.simulateGattDisconnection` command"
short-title: simulateGattDisconnection
slug: Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattDisconnection
page-type: webdriver-command
status:
  - experimental
browser-compat: webdriver.bidi.bluetooth.simulateGattDisconnection
sidebar: webdriver
---

{{SeeCompatTable}}

The `bluetooth.simulateGattDisconnection` [command](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules#commands) of the [`bluetooth`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth) module makes a simulated Bluetooth device drop its connection with a page, or fail to respond to the page's connection attempt.

## Syntax

```json-nolint
{
  "method": "bluetooth.simulateGattDisconnection",
  "params": {
    "address": "09:09:09:09:09:09",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f"
  }
}
```

### Parameters

The `params` field contains:

- `address`
  - : A string that contains the Bluetooth address of the simulated device, such as one that you added with [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral).
- `context`
  - : A string that contains the ID of the [top-level context](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext#top-level_context) whose [simulated adapter](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#simulated_adapter) has the device.
    Context IDs are returned by commands such as [`browsingContext.getTree`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/getTree).

### Return value

The `result` field in the response is an empty object (`{}`).

### Errors

- [`invalid argument`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidArgument)
  - : Thrown in any of the following cases:
    - `address` or `context` is missing or is not a string.
    - `context` is not a top-level context, or the context has no simulated adapter.
    - The simulated adapter has no device with the specified `address`.
- `no such frame`
  - : No context with the specified `context` ID is found.

## Description

The effect depends on the state of the page's [connection](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#gatt_connections) to the device:

- If the page called {{domxref("BluetoothRemoteGATTServer.connect()", "device.gatt.connect()")}} and you haven't answered the attempt with [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse), the attempt fails as if the device couldn't respond, and `connect()` rejects with a `NetworkError` {{domxref("DOMException")}}.
- If the page is connected to the device, the device is disconnected: {{domxref("BluetoothRemoteGATTServer.connected", "device.gatt.connected")}} becomes `false`, and the page receives a `gattserverdisconnected` event on the {{domxref("BluetoothDevice")}} object.

The device stays on the simulated adapter, and the page can connect to it again.

## Examples

### Disconnecting a connected device

Assume you have a [WebDriver BiDi connection](/en-US/docs/Web/WebDriver/How_to/Create_BiDi_connection), an [active session](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/new), and a [subscription](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to [`bluetooth.requestDevicePromptUpdated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/requestDevicePromptUpdated) and [`bluetooth.gattConnectionAttempted`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/gattConnectionAttempted).
Suppose the tab with the context ID `93ee5bd6-d256-4608-a002-9a8995cc0e5f` has a page from `https://example.com`, and you simulated a powered-on adapter for it with [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter).
You added a heart rate monitor with the address `09:09:09:09:09:09` with `bluetooth.simulatePreconnectedPeripheral`.
The page called {{domxref("Bluetooth.requestDevice()", "navigator.bluetooth.requestDevice()")}} in response to a user's action, you chose the monitor in the [device prompt](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_device_prompt) with [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt), and the page listens for the device's `gattserverdisconnected` event.
When the page called `device.gatt.connect()`, you received a `bluetooth.gattConnectionAttempted` event and answered it with `bluetooth.simulateGattConnectionResponse` and the code `0`, so the page is connected to the monitor.

To test how the page behaves when the monitor goes out of range, send the following message:

```json
{
  "id": 1,
  "method": "bluetooth.simulateGattDisconnection",
  "params": {
    "address": "09:09:09:09:09:09",
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

The page receives the `gattserverdisconnected` event, and `device.gatt.connected` is `false`.

### Failing a connection attempt

Using the same connection, session, and subscription, suppose the page calls `device.gatt.connect()` again.
The browser sends a `bluetooth.gattConnectionAttempted` event for the device, and the page waits for the response.

To test how the page handles a device that doesn't respond, send the following message instead of answering with `bluetooth.simulateGattConnectionResponse`:

```json
{
  "id": 2,
  "method": "bluetooth.simulateGattDisconnection",
  "params": {
    "address": "09:09:09:09:09:09",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f"
  }
}
```

The browser responds with an empty `result` object:

```json
{
  "id": 2,
  "type": "success",
  "result": {}
}
```

`connect()` rejects with a `NetworkError` `DOMException`, and `device.gatt.connected` stays `false`.
The page receives no `gattserverdisconnected` event, because it wasn't connected.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse) command
- [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral) command
- {{domxref("BluetoothRemoteGATTServer.disconnect()")}} method
