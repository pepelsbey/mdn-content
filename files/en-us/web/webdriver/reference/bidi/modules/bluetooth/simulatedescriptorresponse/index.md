---
title: "`bluetooth.simulateDescriptorResponse` command"
short-title: simulateDescriptorResponse
slug: Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptorResponse
page-type: webdriver-command
status:
  - experimental
browser-compat: webdriver.bidi.bluetooth.simulateDescriptorResponse
sidebar: webdriver
---

{{SeeCompatTable}}

The `bluetooth.simulateDescriptorResponse` [command](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules#commands) of the [`bluetooth`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth) module answers a read or a write that the page started on a GATT descriptor of a simulated Bluetooth device.

## Syntax

```json-nolint
/* With required parameters */
{
  "method": "bluetooth.simulateDescriptorResponse",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a37-0000-1000-8000-00805f9b34fb",
    "code": 0,
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "descriptorUuid": "00002901-0000-1000-8000-00805f9b34fb",
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "write"
  }
}

/* With required and optional parameters */
{
  "method": "bluetooth.simulateDescriptorResponse",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a37-0000-1000-8000-00805f9b34fb",
    "code": 0,
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "data": [72, 101, 97, 114, 116, 32, 114, 97, 116, 101],
    "descriptorUuid": "00002901-0000-1000-8000-00805f9b34fb",
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "read"
  }
}
```

### Parameters

The `params` field contains:

- `address`
  - : A string that contains the Bluetooth address of the simulated device, such as one that you added with [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral).
    This is the `address` value of the [`bluetooth.descriptorEventGenerated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/descriptorEventGenerated) event for the operation.
- `characteristicUuid`
  - : A string that contains the UUID of the service's characteristic that has the descriptor, such as one that you added with [`bluetooth.simulateCharacteristic`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic).
    This is the `characteristicUuid` value of the event.
- `code`
  - : A non-negative integer that specifies the response code, `0` for success or one of the ATT error codes defined in the Bluetooth Core Specification (Vol 3, Part F, "Error Response").
    - `0`: The operation succeeds.
      Unless the connection to the device ended in the meantime, the page's call resolves, with the bytes in `data` for a read.
    - Any other value: The operation fails, and the page's call rejects.
- `context`
  - : A string that contains the ID of the [top-level context](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext#top-level_context) whose [simulated adapter](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#simulated_adapter) has the device.
    When the page in a top-level context starts the operation, this is the `context` value of the event.
- `data` {{optional_inline}}
  - : An array of integers between `0` and `255` that specifies the bytes of the descriptor's value that the page reads.
    Specify this when the [`type`](#type) field is `read`.
- `descriptorUuid`
  - : A string that contains the UUID of the descriptor, such as one that you added with [`bluetooth.simulateDescriptor`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptor).
    This is the `descriptorUuid` value of the event.
- `serviceUuid`
  - : A string that contains the UUID of the device's service that has the characteristic, such as one that you added with [`bluetooth.simulateService`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService).
    This is the `serviceUuid` value of the event.
- `type`
  - : A string that specifies the type of operation to answer.
    This is the `type` value of the event.
    It can take one of the following values:
    - `read`: Answers a read of the descriptor's value, from {{domxref("BluetoothRemoteGATTDescriptor.readValue()", "readValue()")}}.
    - `write`: Answers a write of the descriptor's value, from {{domxref("BluetoothRemoteGATTDescriptor.writeValue()", "writeValue()")}}.

### Return value

The `result` field in the response is an empty object (`{}`).

### Errors

- [`invalid argument`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidArgument)
  - : Thrown in any of the following cases:
    - A required parameter is missing, or a parameter has an invalid type.
    - `type` is not `read` or `write`.
    - `code` is not a non-negative integer.
    - `context` is not a top-level context.
    - The context has no simulated adapter.
    - The simulated adapter has no device with the specified `address`.
    - The device has no service with the specified `serviceUuid`.
- `invalid element state`
  - : Thrown in any of the following cases:
    - The service has no characteristic with the specified `characteristicUuid`.
    - The characteristic has no descriptor with the specified `descriptorUuid`.
    - No operation of the specified `type` on the descriptor is waiting for a response in the context.
- `no such frame`
  - : No context with the given `context` ID is found.

## Description

When the page reads or writes the value of a simulated descriptor, the operation goes to the simulated device.
The page's own checks can settle the call first.
For example, the call rejects at once if the page isn't connected to the device or if the [GATT blocklist](https://bluetooth.spec.whatwg.org/#gatt-blocklist) excludes the operation.
The blocklist excludes writes to the Client Characteristic Configuration descriptor (`00002902-0000-1000-8000-00805f9b34fb`), for example.
An operation that goes to the device waits until you answer it with this command (see [GATT operations](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#gatt_operations)).

If you're [subscribed](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to `bluetooth.descriptorEventGenerated`, you receive an event that identifies the operation to answer.
The event contains the device's `address`, the `serviceUuid`, `characteristicUuid` and `descriptorUuid` of the descriptor, and the `type` of the operation.
For a write, the event's `data` field contains the bytes that the page wrote.

## Examples

### Answering a read with a value

Assume you have a [WebDriver BiDi connection](/en-US/docs/Web/WebDriver/How_to/Create_BiDi_connection), an [active session](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/new), and a [subscription](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to [`bluetooth.requestDevicePromptUpdated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/requestDevicePromptUpdated), [`bluetooth.gattConnectionAttempted`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/gattConnectionAttempted), and [`bluetooth.descriptorEventGenerated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/descriptorEventGenerated).

Assume also that a tab shows a page from `https://example.com`.
You got the tab's context ID using [`browsingContext.getTree`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/getTree).
You simulated a powered-on adapter for the tab using [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter).
Using [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral), you then added a heart rate monitor with the address `09:09:09:09:09:09` and the heart rate service in its `knownServiceUuids`.
In response to a user's action, the page called {{domxref("Bluetooth.requestDevice()", "navigator.bluetooth.requestDevice()")}} with a filter for the heart rate service.
You chose the monitor in the [device prompt](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_device_prompt) using [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt).
When the page called {{domxref("BluetoothRemoteGATTServer.connect()", "device.gatt.connect()")}}, you answered the `bluetooth.gattConnectionAttempted` event using [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse) with the code `0`, so the page is connected to the monitor.
You then gave the monitor a heart rate service using [`bluetooth.simulateService`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService).
You gave the service a heart rate measurement characteristic using [`bluetooth.simulateCharacteristic`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic).
Using [`bluetooth.simulateDescriptor`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptor), you gave the characteristic a Characteristic User Description descriptor (`00002901-0000-1000-8000-00805f9b34fb`).

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

Send the following message to answer the read with the description "Heart rate", encoded as UTF-8:

```json
{
  "id": 1,
  "method": "bluetooth.simulateDescriptorResponse",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a37-0000-1000-8000-00805f9b34fb",
    "code": 0,
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "data": [72, 101, 97, 114, 116, 32, 114, 97, 116, 101],
    "descriptorUuid": "00002901-0000-1000-8000-00805f9b34fb",
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "read"
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

`readValue()` resolves with a {{jsxref("DataView")}} that holds these bytes, which the page can decode with a {{domxref("TextDecoder")}}.

### Answering a write

Using the same connection, session, and subscriptions as in the first example, and with the page still connected to the monitor, suppose the page calls `writeValue()` on the descriptor with "Chest", encoded as UTF-8.
The browser sends the following event, with the bytes that the page wrote in `data`:

```json
{
  "type": "event",
  "method": "bluetooth.descriptorEventGenerated",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a37-0000-1000-8000-00805f9b34fb",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "data": [67, 104, 101, 115, 116],
    "descriptorUuid": "00002901-0000-1000-8000-00805f9b34fb",
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "write"
  }
}
```

Send the following message to let the write succeed:

```json
{
  "id": 2,
  "method": "bluetooth.simulateDescriptorResponse",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a37-0000-1000-8000-00805f9b34fb",
    "code": 0,
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "descriptorUuid": "00002901-0000-1000-8000-00805f9b34fb",
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "write"
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

`writeValue()` resolves.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [`bluetooth.simulateDescriptor`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptor) command
- [`bluetooth.simulateCharacteristicResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristicResponse) command
- {{domxref("BluetoothRemoteGATTDescriptor.readValue()")}} method
- {{domxref("BluetoothRemoteGATTDescriptor.writeValue()")}} method
- {{domxref("BluetoothRemoteGATTDescriptor")}} interface
