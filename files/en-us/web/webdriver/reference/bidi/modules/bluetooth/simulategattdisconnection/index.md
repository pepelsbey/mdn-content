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

The `bluetooth.simulateGattDisconnection` [command](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules#commands) of the [`bluetooth`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth) module disconnects a simulated Bluetooth device from a page.
If the page's connection attempt to the device is still waiting for a response, the attempt fails instead, as if the device couldn't respond.

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
    - A required parameter is missing or has an invalid type.
    - `context` is not a top-level context.
    - The context has no simulated adapter.
    - The simulated adapter has no device with the specified `address`.
- `no such frame`
  - : No context with the given `context` ID is found.

## Description

The effect depends on the state of the page's [connection](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#gatt_connections) to the device:

- If the page called {{domxref("BluetoothRemoteGATTServer.connect()", "device.gatt.connect()")}} and you haven't answered the attempt with [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse), the attempt fails as if the device couldn't respond.
  The `connect()` call rejects with a `NetworkError` {{domxref("DOMException")}}.
- If the page is connected to the device, the browser disconnects it.
  {{domxref("BluetoothRemoteGATTServer.connected", "device.gatt.connected")}} becomes `false`, and the page receives a `gattserverdisconnected` event on the {{domxref("BluetoothDevice")}} object.

The device stays on the simulated adapter, and the page can connect to it again.

## Examples

### Disconnecting a connected device

Assume you have a [WebDriver BiDi connection](/en-US/docs/Web/WebDriver/How_to/Create_BiDi_connection), an [active session](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/new), and a [subscription](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to [`bluetooth.requestDevicePromptUpdated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/requestDevicePromptUpdated) and [`bluetooth.gattConnectionAttempted`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/gattConnectionAttempted).

Assume also that a tab is open, showing a page from `https://example.com`.
You simulated a powered-on adapter for the tab using [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter).
You then added a heart rate monitor with the address `09:09:09:09:09:09` using [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral).
The page called {{domxref("Bluetooth.requestDevice()", "navigator.bluetooth.requestDevice()")}} in response to a user's action.
You chose the monitor in the [device prompt](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_device_prompt) using [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt).
The page listens for the device's `gattserverdisconnected` event.
When the page called `device.gatt.connect()`, you received a `bluetooth.gattConnectionAttempted` event.
You answered it using [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse) with the code `0`, so the page is connected to the monitor.

Send the following message to disconnect the monitor, as if it went out of range:

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

Using the same connection, session, and subscriptions as in the first example, suppose the page calls `device.gatt.connect()` again.
The browser sends a [`bluetooth.gattConnectionAttempted`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/gattConnectionAttempted) event for the device.
The page's `connect()` call waits for the response.

Instead of answering with [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse), send the following message to make the attempt fail as if the device couldn't respond:

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
