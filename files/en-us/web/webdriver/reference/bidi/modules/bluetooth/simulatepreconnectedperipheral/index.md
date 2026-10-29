---
title: "`bluetooth.simulatePreconnectedPeripheral` command"
short-title: simulatePreconnectedPeripheral
slug: Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral
page-type: webdriver-command
status:
  - experimental
browser-compat: webdriver.bidi.bluetooth.simulatePreconnectedPeripheral
sidebar: webdriver
---

{{SeeCompatTable}}

The `bluetooth.simulatePreconnectedPeripheral` [command](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules#commands) of the [`bluetooth`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth) module adds a simulated Bluetooth device to the [simulated adapter](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#simulated_adapter) of a [top-level context](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext#top-level_context).

## Syntax

```json-nolint
{
  "method": "bluetooth.simulatePreconnectedPeripheral",
  "params": {
    "address": "09:09:09:09:09:09",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "knownServiceUuids": ["0000180d-0000-1000-8000-00805f9b34fb"],
    "manufacturerData": [{ "data": "AP8BAX8=", "key": 17 }],
    "name": "Heart Rate Monitor"
  }
}
```

### Parameters

The `params` field contains:

- `address`
  - : A string that contains the Bluetooth address of the device, such as `09:09:09:09:09:09`.
    It identifies the device in later commands, such as [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse).
- `context`
  - : A string that contains the ID of the [top-level context](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext#top-level_context) whose simulated adapter gets the device.
    Context IDs are returned by commands such as [`browsingContext.getTree`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/getTree).
- `knownServiceUuids`
  - : An array of strings that contains the UUIDs of the [GATT services](/en-US/docs/Web/API/BluetoothRemoteGATTService) that the device advertises, such as `0000180d-0000-1000-8000-00805f9b34fb` for the heart rate service.
    The array can be empty.
- `manufacturerData`
  - : An array of objects, each containing manufacturer-specific data that the device advertises.
    The array can be empty.
    Each object contains the following fields:
    - `data`
      - : A string that contains the data, [base64](/en-US/docs/Glossary/Base64)-encoded.
    - `key`
      - : A non-negative integer that specifies the company identifier code of the manufacturer, as assigned by the Bluetooth SIG.
- `name`
  - : A string that specifies the name of the device, which the page sees as the {{domxref("BluetoothDevice.name", "name")}} of the {{domxref("BluetoothDevice")}} object.

### Return value

The `result` field in the response is an empty object (`{}`).

### Errors

- [`invalid argument`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidArgument)
  - : Thrown in any of the following cases:
    - A parameter is missing or has an invalid type, or a `key` is not a non-negative integer.
    - `context` is not a top-level context.
    - The context has no simulated adapter.
    - The simulated adapter already has a device with the specified `address`.
- `no such frame`
  - : No context with the specified `context` ID is found.

## Description

Before you add a device, create a simulated adapter for the context with [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter).
The adapter can have one device per address.
The device stays until the context is closed or you remove the adapter, along with its devices, with [`bluetooth.disableSimulation`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/disableSimulation).

When a page in the context calls {{domxref("Bluetooth.requestDevice()", "navigator.bluetooth.requestDevice()")}} in response to a user's action, the [device prompt](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_device_prompt) lists the simulated device only if it matches the options that the page passed, for example if its `knownServiceUuids` include every service in one of the filters.
To choose it, use [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt).
Once the page has the device, it can call {{domxref("BluetoothRemoteGATTServer.connect()", "device.gatt.connect()")}}, which waits until you answer with [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse) or [`bluetooth.simulateGattDisconnection`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattDisconnection).

## Examples

### Adding a heart rate monitor

Assume you have a [WebDriver BiDi connection](/en-US/docs/Web/WebDriver/How_to/Create_BiDi_connection), an [active session](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/new), and a [subscription](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to [`bluetooth.requestDevicePromptUpdated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/requestDevicePromptUpdated).
Suppose the tab with the context ID `93ee5bd6-d256-4608-a002-9a8995cc0e5f` has a page from `https://example.com` that looks for heart rate monitors, and you used `bluetooth.simulateAdapter` to simulate a powered-on adapter for it.

Send the following message to add a heart rate monitor:

```json
{
  "id": 1,
  "method": "bluetooth.simulatePreconnectedPeripheral",
  "params": {
    "address": "09:09:09:09:09:09",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "knownServiceUuids": ["0000180d-0000-1000-8000-00805f9b34fb"],
    "manufacturerData": [{ "data": "AP8BAX8=", "key": 17 }],
    "name": "Heart Rate Monitor"
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

When the page calls `requestDevice()` with the filter `{ services: ["heart_rate"] }` in response to a user's action, such as a `script.callFunction` command with `userActivation` set to `true`, you receive a `bluetooth.requestDevicePromptUpdated` event whose `devices` list includes the heart rate monitor.
If you choose it with `bluetooth.handleRequestDevicePrompt`, using the prompt ID from the latest event, `requestDevice()` resolves with a `BluetoothDevice` object whose `name` is `Heart Rate Monitor`.

### Adding a device with an address in use

Using the same connection and session, send the following message to add another device with the same address:

```json
{
  "id": 2,
  "method": "bluetooth.simulatePreconnectedPeripheral",
  "params": {
    "address": "09:09:09:09:09:09",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "knownServiceUuids": ["0000180d-0000-1000-8000-00805f9b34fb"],
    "manufacturerData": [{ "data": "AP8BAX8=", "key": 17 }],
    "name": "Another Monitor"
  }
}
```

The adapter already has a device with this address, so the browser responds with an error:

```json
{
  "type": "error",
  "id": 2,
  "error": "invalid argument",
  "message": "Bluetooth device with address 09:09:09:09:09:09 already exists"
}
```

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter) command
- [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt) command
- [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse) command
- {{domxref("Bluetooth.requestDevice()")}} method
