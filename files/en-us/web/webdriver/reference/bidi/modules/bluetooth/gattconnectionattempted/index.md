---
title: "`bluetooth.gattConnectionAttempted` event"
short-title: gattConnectionAttempted
slug: Web/WebDriver/Reference/BiDi/Modules/bluetooth/gattConnectionAttempted
page-type: webdriver-event
spec-urls: https://bluetooth.spec.whatwg.org/#cddl-type-bluetoothgattconnectionattempted
sidebar: webdriver
---

The `bluetooth.gattConnectionAttempted` [event](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules#events) of the [`bluetooth`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth) module fires when a page calls {{domxref("BluetoothRemoteGATTServer.connect()", "device.gatt.connect()")}} for a simulated Bluetooth device that isn't connected.

## Event data

The `params` field in the event notification is an object with the following fields:

- `address`
  - : A string that contains the Bluetooth address of the simulated device that the page is connecting to, such as one that you added with [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral).
    To answer the attempt, specify this address as the `address` of a [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse) or [`bluetooth.simulateGattDisconnection`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattDisconnection) command.
- `context`
  - : A string that contains the ID of the context in which the page called `connect()`.
    Context IDs are returned by commands such as [`browsingContext.getTree`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/getTree).
    When the page in a [top-level context](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext#top-level_context) called `connect()`, this is the `context` to specify in `bluetooth.simulateGattConnectionResponse` or `bluetooth.simulateGattDisconnection`.

## Description

When a page calls `connect()` for a device that isn't connected, and its top-level context has a [simulated adapter](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#simulated_adapter), the call waits until you simulate the device's response (see [GATT connections](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#gatt_connections)).
If you're [subscribed](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to `bluetooth.gattConnectionAttempted`, you receive this event, which identifies the device and the context.
You then answer the attempt:

- To let the connection succeed, or to make it fail with an error code so that `connect()` rejects with a `NetworkError` {{domxref("DOMException")}}, send `bluetooth.simulateGattConnectionResponse`.
- To make the attempt fail as if the device couldn't respond, send [`bluetooth.simulateGattDisconnection`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattDisconnection).

If the device is already connected, `connect()` resolves at once, without an event.

## Examples

### Receiving a connection attempt

Assume you have a [WebDriver BiDi connection](/en-US/docs/Web/WebDriver/How_to/Create_BiDi_connection), an [active session](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/new), and a [subscription](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to [`bluetooth.requestDevicePromptUpdated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/requestDevicePromptUpdated) and `bluetooth.gattConnectionAttempted`.
Suppose the tab with the context ID `93ee5bd6-d256-4608-a002-9a8995cc0e5f` has a page from `https://example.com`, and you simulated a powered-on adapter for it with [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter).
You added a heart rate monitor with the address `09:09:09:09:09:09` with `bluetooth.simulatePreconnectedPeripheral`.
The page called {{domxref("Bluetooth.requestDevice()", "navigator.bluetooth.requestDevice()")}} in response to a user's action, and you chose the monitor in the [device prompt](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_device_prompt) with [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt).

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

The `connect()` call waits.
To let the connection succeed, send a `bluetooth.simulateGattConnectionResponse` command with this `address` and `context`, and `code` set to `0`.
`connect()` then resolves, and {{domxref("BluetoothRemoteGATTServer.connected", "device.gatt.connected")}} is `true`.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse) command
- [`bluetooth.simulateGattDisconnection`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattDisconnection) command
- {{domxref("BluetoothRemoteGATTServer.connect()")}} method
