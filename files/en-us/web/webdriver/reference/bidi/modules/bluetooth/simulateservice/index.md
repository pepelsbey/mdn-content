---
title: "`bluetooth.simulateService` command"
short-title: simulateService
slug: Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService
page-type: webdriver-command
status:
  - experimental
browser-compat: webdriver.bidi.bluetooth.simulateService
sidebar: webdriver
---

{{SeeCompatTable}}

The `bluetooth.simulateService` [command](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules#commands) of the [`bluetooth`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth) module adds a GATT service to a simulated Bluetooth device, or removes one.

## Syntax

```json-nolint
/* Adding a service */
{
  "method": "bluetooth.simulateService",
  "params": {
    "address": "09:09:09:09:09:09",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "type": "add",
    "uuid": "0000180d-0000-1000-8000-00805f9b34fb"
  }
}

/* Removing a service */
{
  "method": "bluetooth.simulateService",
  "params": {
    "address": "09:09:09:09:09:09",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "type": "remove",
    "uuid": "0000180d-0000-1000-8000-00805f9b34fb"
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
- `type`
  - : A string that specifies whether to add or remove the service.
    It can take one of the following values:
    - `add`: Adds a service with the specified `uuid` to the device.
    - `remove`: Removes the device's service with the specified `uuid`, along with its characteristics and their descriptors.
- `uuid`
  - : A string that contains the 128-bit UUID of the service in lowercase, such as `0000180d-0000-1000-8000-00805f9b34fb` for the heart rate service.

### Return value

The `result` field in the response is an empty object (`{}`).

### Errors

- [`invalid argument`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidArgument)
  - : Thrown in any of the following cases:
    - A parameter is missing or has an invalid type, or `type` is not `add` or `remove`.
    - `context` is not a top-level context, or the context has no simulated adapter.
    - The simulated adapter has no device with the specified `address`.
- `invalid element state`
  - : Thrown in any of the following cases:
    - `type` is `add`, and the device already has a service with the specified `uuid`.
    - `type` is `remove`, and the device has no service with the specified `uuid`.
- `no such frame`
  - : No context with the specified `context` ID is found.

## Description

A simulated device has one service per UUID, and each service can have characteristics, which you add with [`bluetooth.simulateCharacteristic`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic) (see [The GATT hierarchy](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_gatt_hierarchy)).
Once the page has the device from {{domxref("Bluetooth.requestDevice()", "navigator.bluetooth.requestDevice()")}}, you can add a service before or after the page connects to it.
The service stays on the device until you remove it, the context is closed, or you remove the simulated adapter with [`bluetooth.disableSimulation`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/disableSimulation).

Once the page is connected to the device with {{domxref("BluetoothRemoteGATTServer.connect()", "device.gatt.connect()")}}, it finds the service with {{domxref("BluetoothRemoteGATTServer.getPrimaryService()", "device.gatt.getPrimaryService()")}} or {{domxref("BluetoothRemoteGATTServer.getPrimaryServices()", "getPrimaryServices()")}}, if the page is allowed to access it: for example, if the service is in the `filters` or `optionalServices` that the page passed to `requestDevice()`, and the [GATT blocklist](https://bluetooth.spec.whatwg.org/#gatt-blocklist) doesn't exclude its UUID.
Once you remove the service, the page no longer finds it.

## Examples

### Adding a service

Assume you have a [WebDriver BiDi connection](/en-US/docs/Web/WebDriver/How_to/Create_BiDi_connection), an [active session](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/new), and a [subscription](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to [`bluetooth.requestDevicePromptUpdated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/requestDevicePromptUpdated) and [`bluetooth.gattConnectionAttempted`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/gattConnectionAttempted).
Suppose the tab with the context ID `93ee5bd6-d256-4608-a002-9a8995cc0e5f` has a page from `https://example.com`, and you simulated a powered-on adapter for it with [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter).
With `bluetooth.simulatePreconnectedPeripheral`, you added a heart rate monitor with the address `09:09:09:09:09:09` and the heart rate service in its `knownServiceUuids`.
The page called `navigator.bluetooth.requestDevice()` with a filter for the heart rate service in response to a user's action, and you chose the monitor in the [device prompt](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_device_prompt) with [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt).
When the page called `device.gatt.connect()`, you received a `bluetooth.gattConnectionAttempted` event and answered it with [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse) and the code `0`, so the page is connected to the monitor.
The monitor has no services yet.

To give the monitor a heart rate service, send the following message:

```json
{
  "id": 1,
  "method": "bluetooth.simulateService",
  "params": {
    "address": "09:09:09:09:09:09",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "type": "add",
    "uuid": "0000180d-0000-1000-8000-00805f9b34fb"
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

If the page now calls `device.gatt.getPrimaryService("heart_rate")`, the promise resolves with a {{domxref("BluetoothRemoteGATTService")}} object for the service.

### Removing a service

Using the same connection, session, and subscription, with the page still connected to the monitor, send the following message to remove the heart rate service:

```json
{
  "id": 2,
  "method": "bluetooth.simulateService",
  "params": {
    "address": "09:09:09:09:09:09",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "type": "remove",
    "uuid": "0000180d-0000-1000-8000-00805f9b34fb"
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

If the page now calls `device.gatt.getPrimaryService("heart_rate")`, the promise rejects with a `NotFoundError` {{domxref("DOMException")}}.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [`bluetooth.simulateCharacteristic`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic) command
- [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral) command
- {{domxref("BluetoothRemoteGATTServer.getPrimaryService()")}} method
- {{domxref("BluetoothRemoteGATTService")}} interface
