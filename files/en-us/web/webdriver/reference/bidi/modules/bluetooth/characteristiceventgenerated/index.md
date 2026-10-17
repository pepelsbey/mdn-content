---
title: "`bluetooth.characteristicEventGenerated` event"
short-title: characteristicEventGenerated
slug: Web/WebDriver/Reference/BiDi/Modules/bluetooth/characteristicEventGenerated
page-type: webdriver-event
spec-urls: https://bluetooth.spec.whatwg.org/#cddl-type-bluetoothcharacteristiceventgenerated
sidebar: webdriver
---

The `bluetooth.characteristicEventGenerated` [event](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules#events) of the [`bluetooth`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth) module fires when a page reads or writes the value of a GATT characteristic of a simulated Bluetooth device, or starts or stops its notifications, unless the page's own checks settle the call first.
The operation then waits for you to simulate the device's response.

## Event data

The `params` field in the event notification is an object with the following fields:

- `address`
  - : A string that contains the Bluetooth address of the simulated device that has the characteristic.
- `characteristicUuid`
  - : A string that contains the UUID of the characteristic, as you specified it in [`bluetooth.simulateCharacteristic`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic).
- `context`
  - : A string that contains the ID of the context in which the page started the operation.
    Context IDs are returned by commands such as [`browsingContext.getTree`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/getTree).
    When the page in a [top-level context](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext#top-level_context) started the operation, this is the `context` to specify in [`bluetooth.simulateCharacteristicResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristicResponse).
- `data` {{optional_inline}}
  - : An array of integers between `0` and `255` that contains the bytes that the page wrote.
    This field is included when the `type` field value is `write-with-response` or `write-without-response`.
- `serviceUuid`
  - : A string that contains the UUID of the service that has the characteristic, as you specified it in [`bluetooth.simulateService`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService).
- `type`
  - : A string that indicates the type of the operation.
    It has one of the following values:
    - `read`: The page reads the characteristic's value, with {{domxref("BluetoothRemoteGATTCharacteristic.readValue()", "readValue()")}}.
    - `subscribe-to-notifications`: The page starts notifications, with {{domxref("BluetoothRemoteGATTCharacteristic.startNotifications()", "startNotifications()")}}.
    - `unsubscribe-from-notifications`: The page stops notifications, with {{domxref("BluetoothRemoteGATTCharacteristic.stopNotifications()", "stopNotifications()")}}.
    - `write-with-response`: The page writes the characteristic's value, with {{domxref("BluetoothRemoteGATTCharacteristic.writeValueWithResponse()", "writeValueWithResponse()")}}.
    - `write-without-response`: The page writes the characteristic's value, with {{domxref("BluetoothRemoteGATTCharacteristic.writeValueWithoutResponse()", "writeValueWithoutResponse()")}}.

## Description

When a page reads the value of a simulated characteristic, writes it with {{domxref("BluetoothRemoteGATTCharacteristic.writeValueWithResponse()", "writeValueWithResponse()")}} or {{domxref("BluetoothRemoteGATTCharacteristic.writeValueWithoutResponse()", "writeValueWithoutResponse()")}}, or starts or stops notifications, the operation goes to the simulated device.
The page's own checks can settle the call first: for example, it rejects at once if the page isn't connected to the device or if the [GATT blocklist](https://bluetooth.spec.whatwg.org/#gatt-blocklist) excludes the operation, `startNotifications()` resolves at once if notifications are already started, and `stopNotifications()` resolves at once if they aren't.
An operation that goes to the device waits until you answer it with `bluetooth.simulateCharacteristicResponse` (see [GATT operations](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#gatt_operations)).
If the connection to the device ends first, the call rejects, except for `stopNotifications()`.

If you're [subscribed](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to `bluetooth.characteristicEventGenerated`, you receive this event for each operation that goes to the device.
To answer the operation, send `bluetooth.simulateCharacteristicResponse` with the event's `address`, `serviceUuid`, and `characteristicUuid`, which identify the characteristic.
For its `type`, use the event's `type`, or `write` for both types of write.
If the page in a top-level context started the operation, also use the event's `context`.

## Examples

### Receiving a read

Assume you have a [WebDriver BiDi connection](/en-US/docs/Web/WebDriver/How_to/Create_BiDi_connection), an [active session](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/new), and a [subscription](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to `bluetooth.requestDevicePromptUpdated`, `bluetooth.gattConnectionAttempted`, and `bluetooth.characteristicEventGenerated`.
Suppose the tab with the context ID `93ee5bd6-d256-4608-a002-9a8995cc0e5f` has a page from `https://example.com`, and you simulated a powered-on adapter for it with [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter).
With [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral), you added a heart rate monitor with the address `09:09:09:09:09:09` and the heart rate service in its `knownServiceUuids`.
The page called {{domxref("Bluetooth.requestDevice()", "navigator.bluetooth.requestDevice()")}} with a filter for the heart rate service in response to a user's action, and you chose the monitor in the [device prompt](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_device_prompt) with [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt).
When the page called {{domxref("BluetoothRemoteGATTServer.connect()", "device.gatt.connect()")}}, you answered the `bluetooth.gattConnectionAttempted` event with [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse) and the code `0`, so the page is connected to the monitor.
You then gave the monitor a heart rate service with `bluetooth.simulateService`, and the service a body sensor location characteristic whose properties include `read` and `write` with `bluetooth.simulateCharacteristic`.

When the page calls `readValue()` on the body sensor location characteristic, the browser sends the following event:

```json
{
  "type": "event",
  "method": "bluetooth.characteristicEventGenerated",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a38-0000-1000-8000-00805f9b34fb",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "read"
  }
}
```

The `readValue()` call waits.
To answer it, send a `bluetooth.simulateCharacteristicResponse` command with the event's `address`, `characteristicUuid`, `context`, `serviceUuid`, and `type`, the code `0`, and the value's bytes in `data`, such as `[1]`, which means Chest for this characteristic.
`readValue()` then resolves with a {{jsxref("DataView")}} that holds the byte `1`.

### Receiving a write, with the bytes written

Using the same connection, session, and subscription, with the page still connected to the monitor, suppose the page calls `writeValueWithResponse()` with the byte `2` on the body sensor location characteristic.
The browser sends the following event, with the bytes that the page wrote in `data`:

```json
{
  "type": "event",
  "method": "bluetooth.characteristicEventGenerated",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a38-0000-1000-8000-00805f9b34fb",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "data": [2],
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "write-with-response"
  }
}
```

To let the write succeed, send a `bluetooth.simulateCharacteristicResponse` command with the event's `address`, `characteristicUuid`, `context`, and `serviceUuid`, the `type` `write`, and the code `0`.
`writeValueWithResponse()` then resolves.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [`bluetooth.simulateCharacteristicResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristicResponse) command
- [`bluetooth.simulateCharacteristic`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic) command
- [`bluetooth.descriptorEventGenerated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/descriptorEventGenerated) event
- {{domxref("BluetoothRemoteGATTCharacteristic")}} interface
