---
title: "`bluetooth.descriptorEventGenerated` event"
short-title: descriptorEventGenerated
slug: Web/WebDriver/Reference/BiDi/Modules/bluetooth/descriptorEventGenerated
page-type: webdriver-event
spec-urls: https://bluetooth.spec.whatwg.org/#cddl-type-bluetoothdescriptoreventgenerated
sidebar: webdriver
---

The `bluetooth.descriptorEventGenerated` [event](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules#events) of the [`bluetooth`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth) module fires when a page reads or writes the value of a GATT descriptor of a simulated Bluetooth device, unless the page's own checks settle the call first.
The operation then waits for you to simulate the device's response.

## Event data

The `params` field in the event notification is an object with the following fields:

- `address`
  - : A string that contains the Bluetooth address of the simulated device that has the descriptor.
- `characteristicUuid`
  - : A string that contains the UUID of the characteristic that has the descriptor, as you specified it in [`bluetooth.simulateCharacteristic`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic).
- `context`
  - : A string that contains the ID of the context in which the page started the operation.
    Context IDs are returned by commands such as [`browsingContext.getTree`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/getTree).
    When the page in a [top-level context](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext#top-level_context) started the operation, this is the `context` to specify in [`bluetooth.simulateDescriptorResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptorResponse).
- `data` {{optional_inline}}
  - : An array of integers between `0` and `255` that contains the bytes that the page wrote.
    This field is included when the [`type`](#type) field is `write`.
- `descriptorUuid`
  - : A string that contains the UUID of the descriptor, as you specified it in [`bluetooth.simulateDescriptor`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptor).
- `serviceUuid`
  - : A string that contains the UUID of the service that has the characteristic, as you specified it in [`bluetooth.simulateService`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService).
- `type`
  - : A string that indicates the type of the operation.
    It has one of the following values:
    - `read`: The page reads the descriptor's value, with {{domxref("BluetoothRemoteGATTDescriptor.readValue()", "readValue()")}}.
    - `write`: The page writes the descriptor's value, with {{domxref("BluetoothRemoteGATTDescriptor.writeValue()", "writeValue()")}}.

## Description

When a page reads or writes the value of a simulated descriptor, the operation goes to the simulated device.
The page's own checks can settle the call first.
For example, the call rejects at once if the page isn't connected to the device or if the [GATT blocklist](https://bluetooth.spec.whatwg.org/#gatt-blocklist) excludes the operation, as it does for writes to the Client Characteristic Configuration descriptor (`00002902-0000-1000-8000-00805f9b34fb`).
An operation that goes to the device waits until you answer it with `bluetooth.simulateDescriptorResponse` (see [GATT operations](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#gatt_operations)).
If the connection to the device ends first, the call rejects.

If you're [subscribed](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to `bluetooth.descriptorEventGenerated`, you receive this event for each operation that goes to the device.
To answer the operation, send `bluetooth.simulateDescriptorResponse` with the event's `address`, `serviceUuid`, `characteristicUuid`, and `descriptorUuid`, which identify the descriptor.
For its `type`, use the event's `type`.
If the page in a top-level context started the operation, also use the event's `context`.

## Examples

### Receiving a read

Assume you have a [WebDriver BiDi connection](/en-US/docs/Web/WebDriver/How_to/Create_BiDi_connection), an [active session](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/new), and a [subscription](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to [`bluetooth.requestDevicePromptUpdated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/requestDevicePromptUpdated), [`bluetooth.gattConnectionAttempted`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/gattConnectionAttempted), and `bluetooth.descriptorEventGenerated`.

Assume also that a tab is open, showing a page from `https://example.com`.
You simulated a powered-on adapter for the tab using [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter).
Using [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral), you then added a heart rate monitor with the address `09:09:09:09:09:09` and the heart rate service in its `knownServiceUuids`.
The page called {{domxref("Bluetooth.requestDevice()", "navigator.bluetooth.requestDevice()")}} with a filter for the heart rate service in response to a user's action.
You chose the monitor in the [device prompt](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_device_prompt) using [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt).
When the page called {{domxref("BluetoothRemoteGATTServer.connect()", "device.gatt.connect()")}}, you answered the `bluetooth.gattConnectionAttempted` event using [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse) with the code `0`, so the page is connected to the monitor.
You then gave the monitor a heart rate service using [`bluetooth.simulateService`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService).
You gave the service a heart rate measurement characteristic using [`bluetooth.simulateCharacteristic`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic).
You gave the characteristic a Characteristic User Description descriptor (`00002901-0000-1000-8000-00805f9b34fb`) using [`bluetooth.simulateDescriptor`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptor).

When the page calls `readValue()` on the descriptor, the browser sends the following event:

```json
{
  "type": "event",
  "method": "bluetooth.descriptorEventGenerated",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a37-0000-1000-8000-00805f9b34fb",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "descriptorUuid": "00002901-0000-1000-8000-00805f9b34fb",
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "read"
  }
}
```

The `readValue()` call waits.
To answer it, send a [`bluetooth.simulateDescriptorResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptorResponse) command with the event's `address`, `characteristicUuid`, `context`, `descriptorUuid`, `serviceUuid`, and `type`, the code `0`, and the value's bytes in `data`, such as the description "Chest strap" encoded as UTF-8.
`readValue()` then resolves with a {{jsxref("DataView")}} that holds these bytes.

### Receiving a write, with the bytes written

Using the same connection, session, and subscriptions as in the first example, with the page still connected to the monitor, suppose the page calls `writeValue()` on the descriptor with "Left wrist", encoded as UTF-8.
The browser sends the following event, with the bytes that the page wrote in `data`:

```json
{
  "type": "event",
  "method": "bluetooth.descriptorEventGenerated",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a37-0000-1000-8000-00805f9b34fb",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "data": [76, 101, 102, 116, 32, 119, 114, 105, 115, 116],
    "descriptorUuid": "00002901-0000-1000-8000-00805f9b34fb",
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "write"
  }
}
```

To let the write succeed, send a [`bluetooth.simulateDescriptorResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptorResponse) command with the event's `address`, `characteristicUuid`, `context`, `descriptorUuid`, `serviceUuid`, and `type`, and the code `0`.
`writeValue()` then resolves.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [`bluetooth.simulateDescriptorResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptorResponse) command
- [`bluetooth.simulateDescriptor`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptor) command
- [`bluetooth.characteristicEventGenerated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/characteristicEventGenerated) event
- {{domxref("BluetoothRemoteGATTDescriptor")}} interface
