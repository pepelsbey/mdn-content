---
title: "`bluetooth.simulateCharacteristicResponse` command"
short-title: simulateCharacteristicResponse
slug: Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristicResponse
page-type: webdriver-command
status:
  - experimental
browser-compat: webdriver.bidi.bluetooth.simulateCharacteristicResponse
sidebar: webdriver
---

{{SeeCompatTable}}

The `bluetooth.simulateCharacteristicResponse` [command](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules#commands) of the [`bluetooth`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth) module answers an operation that the page started on a GATT characteristic of a simulated Bluetooth device, such as a read of its value.

## Syntax

```json-nolint
/* With required parameters */
{
  "method": "bluetooth.simulateCharacteristicResponse",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a38-0000-1000-8000-00805f9b34fb",
    "code": 0,
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "write"
  }
}

/* With required and optional parameters */
{
  "method": "bluetooth.simulateCharacteristicResponse",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a38-0000-1000-8000-00805f9b34fb",
    "code": 0,
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "data": [1],
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "read"
  }
}
```

### Parameters

The `params` field contains:

- `address`
  - : A string that contains the Bluetooth address of the simulated device, such as one that you added with [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral).
    This is the `address` value of the [`bluetooth.characteristicEventGenerated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/characteristicEventGenerated) event for the operation.
- `characteristicUuid`
  - : A string that contains the UUID of the characteristic, such as one that you added with [`bluetooth.simulateCharacteristic`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic).
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
  - : An array of integers between `0` and `255` that specifies the bytes of the characteristic's value that the page reads.
    Specify this when the [`type`](#type) field is `read`.
- `serviceUuid`
  - : A string that contains the UUID of the device's service that has the characteristic, such as one that you added with [`bluetooth.simulateService`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService).
    This is the `serviceUuid` value of the event.
- `type`
  - : A string that specifies the type of operation to answer.
    It can take one of the following values:
    - `read`: Answers a read of the characteristic's value, from {{domxref("BluetoothRemoteGATTCharacteristic.readValue()", "readValue()")}}.
    - `subscribe-to-notifications`: Answers a request to start notifications, from {{domxref("BluetoothRemoteGATTCharacteristic.startNotifications()", "startNotifications()")}}.
    - `unsubscribe-from-notifications`: Answers a request to stop notifications, from {{domxref("BluetoothRemoteGATTCharacteristic.stopNotifications()", "stopNotifications()")}}.
    - `write`: Answers a write of the characteristic's value, from {{domxref("BluetoothRemoteGATTCharacteristic.writeValueWithResponse()", "writeValueWithResponse()")}} or {{domxref("BluetoothRemoteGATTCharacteristic.writeValueWithoutResponse()", "writeValueWithoutResponse()")}}, for which the event's `type` is `write-with-response` or `write-without-response`.

### Return value

The `result` field in the response is an empty object (`{}`).

### Errors

- [`invalid argument`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidArgument)
  - : Thrown in any of the following cases:
    - A required parameter is missing, or a parameter has an invalid type.
    - `type` is not `read`, `subscribe-to-notifications`, `unsubscribe-from-notifications`, or `write`.
    - `code` is not a non-negative integer.
    - `context` is not a top-level context.
    - The context has no simulated adapter.
    - The simulated adapter has no device with the specified `address`.
    - The device has no service with the specified `serviceUuid`.
- `invalid element state`
  - : Thrown in any of the following cases:
    - The service has no characteristic with the specified `characteristicUuid`.
    - No operation of the specified `type` on the characteristic is waiting for a response in the context.
- `no such frame`
  - : No context with the given `context` ID is found.

## Description

When the page performs one of the following operations on a simulated characteristic, the operation goes to the simulated device:

- Reading its value
- Writing its value with {{domxref("BluetoothRemoteGATTCharacteristic.writeValueWithResponse()", "writeValueWithResponse()")}} or {{domxref("BluetoothRemoteGATTCharacteristic.writeValueWithoutResponse()", "writeValueWithoutResponse()")}}
- Starting or stopping notifications

The page's own checks can settle the call first.
For example, the call rejects at once if the page isn't connected to the device or if the [GATT blocklist](https://bluetooth.spec.whatwg.org/#gatt-blocklist) excludes the operation.
Also, `startNotifications()` resolves at once if notifications are already started.
An operation that goes to the device waits until you answer it with this command (see [GATT operations](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#gatt_operations)).

If you're [subscribed](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to `bluetooth.characteristicEventGenerated`, you receive an event that identifies the operation to answer.
The event contains the device's `address`, the `serviceUuid` and `characteristicUuid` of the characteristic, and the `type` of the operation.
For a write, the event's `data` field contains the bytes that the page wrote.

## Examples

### Answering a read with a value

Assume you have a [WebDriver BiDi connection](/en-US/docs/Web/WebDriver/How_to/Create_BiDi_connection), an [active session](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/new), and a [subscription](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to [`bluetooth.requestDevicePromptUpdated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/requestDevicePromptUpdated), [`bluetooth.gattConnectionAttempted`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/gattConnectionAttempted), and [`bluetooth.characteristicEventGenerated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/characteristicEventGenerated).

Assume also that a tab shows a page from `https://example.com`.
You got the tab's context ID using [`browsingContext.getTree`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/getTree).
You simulated a powered-on adapter for the tab using [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter).
Using [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral), you then added a heart rate monitor with the address `09:09:09:09:09:09` and the heart rate service in its `knownServiceUuids`.
In response to a user's action, the page called {{domxref("Bluetooth.requestDevice()", "navigator.bluetooth.requestDevice()")}} with a filter for the heart rate service.
You chose the monitor in the [device prompt](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_device_prompt) using [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt).
When the page called {{domxref("BluetoothRemoteGATTServer.connect()", "device.gatt.connect()")}}, you answered the `bluetooth.gattConnectionAttempted` event using [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse) with the code `0`, so the page is connected to the monitor.
You then gave the monitor a heart rate service using [`bluetooth.simulateService`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService).
Using [`bluetooth.simulateCharacteristic`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic), you gave the service a body sensor location characteristic whose properties include `read` and `write`.

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

Send the following message to answer the read with the value `1`, which means Chest for this characteristic:

```json
{
  "id": 1,
  "method": "bluetooth.simulateCharacteristicResponse",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a38-0000-1000-8000-00805f9b34fb",
    "code": 0,
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "data": [1],
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

`readValue()` resolves with a {{jsxref("DataView")}} that holds the byte `1`.

### Failing a write

Using the same connection, session, and subscriptions as in the first example, and with the page still connected to the monitor, suppose the page calls `writeValueWithResponse()` with the byte `2` on the body sensor location characteristic.
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

Send the following message to fail the write with the ATT error code `0x03` (Write Not Permitted):

```json
{
  "id": 2,
  "method": "bluetooth.simulateCharacteristicResponse",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a38-0000-1000-8000-00805f9b34fb",
    "code": 3,
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
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

`writeValueWithResponse()` rejects with a {{domxref("DOMException")}}.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [`bluetooth.simulateCharacteristic`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic) command
- [`bluetooth.simulateDescriptorResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptorResponse) command
- {{domxref("BluetoothRemoteGATTCharacteristic.readValue()")}} method
- {{domxref("BluetoothRemoteGATTCharacteristic")}} interface
