---
title: "`bluetooth.simulateDescriptor` command"
short-title: simulateDescriptor
slug: Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptor
page-type: webdriver-command
status:
  - experimental
browser-compat: webdriver.bidi.bluetooth.simulateDescriptor
sidebar: webdriver
---

{{SeeCompatTable}}

The `bluetooth.simulateDescriptor` [command](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules#commands) of the [`bluetooth`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth) module adds a GATT descriptor to a characteristic of a simulated Bluetooth device, or removes one.

## Syntax

```json-nolint
/* Adding a descriptor */
{
  "method": "bluetooth.simulateDescriptor",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a37-0000-1000-8000-00805f9b34fb",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "descriptorUuid": "00002902-0000-1000-8000-00805f9b34fb",
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "add"
  }
}

/* Removing a descriptor */
{
  "method": "bluetooth.simulateDescriptor",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a37-0000-1000-8000-00805f9b34fb",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "descriptorUuid": "00002902-0000-1000-8000-00805f9b34fb",
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "remove"
  }
}
```

### Parameters

The `params` field contains:

- `address`
  - : A string that contains the Bluetooth address of the simulated device, such as one that you added with [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral).
- `characteristicUuid`
  - : A string that contains the UUID of the characteristic that has the descriptor, such as one that you added with [`bluetooth.simulateCharacteristic`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic).
- `context`
  - : A string that contains the ID of the [top-level context](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext#top-level_context) whose [simulated adapter](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#simulated_adapter) has the device.
    Context IDs are returned by commands such as [`browsingContext.getTree`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/getTree).
- `descriptorUuid`
  - : A string that contains the 128-bit UUID of the descriptor in lowercase, such as `00002902-0000-1000-8000-00805f9b34fb` for the Client Characteristic Configuration descriptor.
- `serviceUuid`
  - : A string that contains the UUID of the device's service that has the characteristic, such as one that you added with [`bluetooth.simulateService`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService).
- `type`
  - : A string that specifies whether to add or remove the descriptor.
    It can take one of the following values:
    - `add`: Adds a descriptor with the specified [`descriptorUuid`](#descriptoruuid) to the characteristic.
    - `remove`: Removes the characteristic's descriptor with the specified `descriptorUuid`.

### Return value

The `result` field in the response is an empty object (`{}`).

### Errors

- [`invalid argument`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidArgument)
  - : Thrown in any of the following cases:
    - A required parameter is missing or has an invalid type.
    - `type` is not `add` or `remove`.
    - `context` is not a top-level context.
    - The context has no simulated adapter.
    - The simulated adapter has no device with the specified `address`.
    - The device has no service with the specified `serviceUuid`.
    - The service has no characteristic with the specified `characteristicUuid`.
- `invalid element state`
  - : Thrown in any of the following cases:
    - `type` is `add`, and the characteristic already has a descriptor with the specified `descriptorUuid`.
    - `type` is `remove`, and the characteristic has no descriptor with the specified `descriptorUuid`.
- `no such frame`
  - : No context with the given `context` ID is found.

## Description

A characteristic has one descriptor per UUID, and a descriptor contains no other attributes.
For more information, see [The GATT hierarchy](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_gatt_hierarchy).
Removing the characteristic or its service also removes the descriptor.
Once the page is connected to the device and has the characteristic, it finds the descriptor with {{domxref("BluetoothRemoteGATTCharacteristic.getDescriptor()", "characteristic.getDescriptor()")}} or {{domxref("BluetoothRemoteGATTCharacteristic.getDescriptors()", "getDescriptors()")}}.
It doesn't find the descriptor if the [GATT blocklist](https://bluetooth.spec.whatwg.org/#gatt-blocklist) excludes its UUID.
Once you remove the descriptor, the page no longer finds it.

When the page reads or writes the descriptor's value, the operation goes to the simulated device.
The page's own checks can settle the call first, for example when the page isn't connected to the device or the GATT blocklist excludes the operation.
An operation that goes to the device waits until you answer it with [`bluetooth.simulateDescriptorResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptorResponse).
If you're subscribed to [`bluetooth.descriptorEventGenerated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/descriptorEventGenerated), you receive an event that says which operation the page started.

## Examples

### Adding a descriptor

Assume you have a [WebDriver BiDi connection](/en-US/docs/Web/WebDriver/How_to/Create_BiDi_connection), an [active session](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/new), and a [subscription](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to [`bluetooth.requestDevicePromptUpdated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/requestDevicePromptUpdated) and [`bluetooth.gattConnectionAttempted`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/gattConnectionAttempted).

Assume also that a tab is open, showing a page from `https://example.com`.
You opened the tab using [`browsingContext.create`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/create), which returned its context ID.
You then loaded the page in the tab using [`browsingContext.navigate`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/navigate).
You simulated a powered-on adapter for the tab using [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter).
You then added a heart rate monitor with the address `09:09:09:09:09:09` and the heart rate service in `knownServiceUuids` using [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral).
In response to a user's action, the page called {{domxref("Bluetooth.requestDevice()", "navigator.bluetooth.requestDevice()")}} with a filter for the heart rate service.
You chose the monitor in the [device prompt](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_device_prompt) using [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt).
When the page called {{domxref("BluetoothRemoteGATTServer.connect()", "device.gatt.connect()")}}, you received a `bluetooth.gattConnectionAttempted` event.
You answered it using [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse) with the code `0`, so the page is connected to the monitor.
You then added a heart rate service to the monitor using [`bluetooth.simulateService`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService).
You added a heart rate measurement characteristic that supports notifications to the service using [`bluetooth.simulateCharacteristic`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic).
The page got the characteristic using `service.getCharacteristic()`.
The characteristic has no descriptors yet.

Send the following message to add a Client Characteristic Configuration descriptor to the characteristic:

```json
{
  "id": 1,
  "method": "bluetooth.simulateDescriptor",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a37-0000-1000-8000-00805f9b34fb",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "descriptorUuid": "00002902-0000-1000-8000-00805f9b34fb",
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "add"
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

If the page now calls `characteristic.getDescriptors()`, the promise resolves with an array that contains only the descriptor.

### Removing a descriptor

Using the same connection, session, subscriptions, and tab as in the first example, with the page still connected to the monitor, send the following message to remove the Client Characteristic Configuration descriptor:

```json
{
  "id": 2,
  "method": "bluetooth.simulateDescriptor",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a37-0000-1000-8000-00805f9b34fb",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "descriptorUuid": "00002902-0000-1000-8000-00805f9b34fb",
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "remove"
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

The characteristic has no descriptors left.
If the page now calls `characteristic.getDescriptors()`, the promise rejects with a `NotFoundError` {{domxref("DOMException")}}.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [`bluetooth.simulateCharacteristic`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic) command
- [`bluetooth.simulateService`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService) command
- {{domxref("BluetoothRemoteGATTCharacteristic.getDescriptor()")}} method
- {{domxref("BluetoothRemoteGATTDescriptor")}} interface
