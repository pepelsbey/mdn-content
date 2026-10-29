---
title: "`bluetooth.simulateCharacteristic` command"
short-title: simulateCharacteristic
slug: Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic
page-type: webdriver-command
status:
  - experimental
browser-compat: webdriver.bidi.bluetooth.simulateCharacteristic
sidebar: webdriver
---

{{SeeCompatTable}}

The `bluetooth.simulateCharacteristic` [command](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules#commands) of the [`bluetooth`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth) module adds a GATT characteristic to a service of a simulated Bluetooth device, or removes one.

## Syntax

```json-nolint
/* Adding a characteristic */
{
  "method": "bluetooth.simulateCharacteristic",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicProperties": {
      "notify": true
    },
    "characteristicUuid": "00002a37-0000-1000-8000-00805f9b34fb",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "add"
  }
}

/* Removing a characteristic */
{
  "method": "bluetooth.simulateCharacteristic",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a37-0000-1000-8000-00805f9b34fb",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "serviceUuid": "0000180d-0000-1000-8000-00805f9b34fb",
    "type": "remove"
  }
}
```

### Parameters

The `params` field contains:

- `address`
  - : A string that contains the Bluetooth address of the simulated device, such as one that you added with [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral).
- `characteristicProperties` {{optional_inline}}
  - : An object that specifies the properties of the characteristic to add.
    The page sees them in the characteristic's {{domxref("BluetoothRemoteGATTCharacteristic.properties", "properties")}}.
    Specify this when the `type` field value is `add`, and omit it when the value is `remove`.
    A property is set only if its field is `true`.
    It can contain the following fields:
    - `authenticatedSignedWrites` {{optional_inline}}
      - : A boolean that indicates whether signed writes to the characteristic's value are permitted.
    - `broadcast` {{optional_inline}}
      - : A boolean that indicates whether the characteristic's value can be broadcast.
    - `extendedProperties` {{optional_inline}}
      - : A boolean that indicates whether the characteristic has further properties, defined in its Characteristic Extended Properties descriptor.
        {{domxref("BluetoothCharacteristicProperties")}} has no property of this name: if it's `true`, the page's `reliableWrite` and `writableAuxiliaries` properties come from that descriptor; otherwise, they are `false`.
    - `indicate` {{optional_inline}}
      - : A boolean that indicates whether the characteristic can send indications of its value, with acknowledgement.
    - `notify` {{optional_inline}}
      - : A boolean that indicates whether the characteristic can send notifications of its value, without acknowledgement.
    - `read` {{optional_inline}}
      - : A boolean that indicates whether the characteristic's value can be read.
    - `write` {{optional_inline}}
      - : A boolean that indicates whether the characteristic's value can be written with a response.
    - `writeWithoutResponse` {{optional_inline}}
      - : A boolean that indicates whether the characteristic's value can be written without a response.
- `characteristicUuid`
  - : A string that contains the 128-bit UUID of the characteristic in lowercase, such as `00002a37-0000-1000-8000-00805f9b34fb` for the heart rate measurement characteristic.
- `context`
  - : A string that contains the ID of the [top-level context](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext#top-level_context) whose [simulated adapter](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#simulated_adapter) has the device.
    Context IDs are returned by commands such as [`browsingContext.getTree`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/getTree).
- `serviceUuid`
  - : A string that contains the UUID of the device's service to add the characteristic to or remove it from, such as one that you added with [`bluetooth.simulateService`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService).
- `type`
  - : A string that specifies whether to add or remove the characteristic.
    It can take one of the following values:
    - `add`: Adds a characteristic with the specified `characteristicUuid` and `characteristicProperties` to the service.
    - `remove`: Removes the service's characteristic with the specified `characteristicUuid`, along with its descriptors.

### Return value

The `result` field in the response is an empty object (`{}`).

### Errors

- [`invalid argument`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidArgument)
  - : Thrown in any of the following cases:
    - A parameter is missing or has an invalid type, or `type` is not `add` or `remove`.
    - `type` is `add`, `characteristicProperties` is missing, and the service has no characteristic with the specified `characteristicUuid`.
    - `type` is `remove`, and `characteristicProperties` is specified.
    - `context` is not a top-level context, or the context has no simulated adapter.
    - The simulated adapter has no device with the specified `address`.
    - The device has no service with the specified `serviceUuid`.
- `invalid element state`
  - : Thrown in any of the following cases:
    - `type` is `add`, and the service already has a characteristic with the specified `characteristicUuid`.
    - `type` is `remove`, `characteristicProperties` is not specified, and the service has no characteristic with the specified `characteristicUuid`.
- `no such frame`
  - : No context with the specified `context` ID is found.

## Description

A service has one characteristic per UUID, and each characteristic can have descriptors, which you add with [`bluetooth.simulateDescriptor`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptor) (see [The GATT hierarchy](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_gatt_hierarchy)).
To change the properties of a characteristic, remove it and add it again, then add again any descriptors it had.
The page sees the new properties in the characteristic that it gets afterward.

Once the page is connected to the device and has the service, it finds the characteristic with {{domxref("BluetoothRemoteGATTService.getCharacteristic()", "service.getCharacteristic()")}} or {{domxref("BluetoothRemoteGATTService.getCharacteristics()", "getCharacteristics()")}}, unless the [GATT blocklist](https://bluetooth.spec.whatwg.org/#gatt-blocklist) excludes its UUID.
Once you remove the characteristic, the page no longer finds it.

When the page reads or writes the characteristic's value, or starts or stops notifications, and the characteristic's properties permit it, the page's call waits until you answer with [`bluetooth.simulateCharacteristicResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristicResponse).
If you're subscribed to [`bluetooth.characteristicEventGenerated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/characteristicEventGenerated), you receive an event that says which operation the page started.

## Examples

### Adding a characteristic

Assume you have a [WebDriver BiDi connection](/en-US/docs/Web/WebDriver/How_to/Create_BiDi_connection), an [active session](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/new), and a [subscription](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to [`bluetooth.requestDevicePromptUpdated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/requestDevicePromptUpdated) and [`bluetooth.gattConnectionAttempted`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/gattConnectionAttempted).
Suppose the tab with the context ID `93ee5bd6-d256-4608-a002-9a8995cc0e5f` has a page from `https://example.com`, and you simulated a powered-on adapter for it with [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter).
With `bluetooth.simulatePreconnectedPeripheral`, you added a heart rate monitor with the address `09:09:09:09:09:09` and the heart rate service in its `knownServiceUuids`.
The page called {{domxref("Bluetooth.requestDevice()", "navigator.bluetooth.requestDevice()")}} with a filter for the heart rate service in response to a user's action, and you chose the monitor in the [device prompt](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_device_prompt) with [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt).
You then gave the monitor a heart rate service with `bluetooth.simulateService`.
When the page called {{domxref("BluetoothRemoteGATTServer.connect()", "device.gatt.connect()")}}, you received a `bluetooth.gattConnectionAttempted` event and answered it with [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse) and the code `0`, so the page is connected to the monitor.
The page has the heart rate service from `device.gatt.getPrimaryService()`, and the service has no characteristics yet.

To add a heart rate measurement characteristic that supports notifications, send the following message:

```json
{
  "id": 1,
  "method": "bluetooth.simulateCharacteristic",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicProperties": {
      "notify": true
    },
    "characteristicUuid": "00002a37-0000-1000-8000-00805f9b34fb",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
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

If the page now calls `service.getCharacteristic("00002a37-0000-1000-8000-00805f9b34fb")`, the promise resolves with the characteristic, whose `properties.notify` is `true` and whose other properties are `false`.

### Removing a characteristic

Using the same connection, session, and subscription, with the page still connected to the monitor, send the following message to remove the heart rate measurement characteristic:

```json
{
  "id": 2,
  "method": "bluetooth.simulateCharacteristic",
  "params": {
    "address": "09:09:09:09:09:09",
    "characteristicUuid": "00002a37-0000-1000-8000-00805f9b34fb",
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
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

The service has no characteristics left, so if the page now calls `service.getCharacteristics()`, the promise rejects with a `NotFoundError` {{domxref("DOMException")}}.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [`bluetooth.simulateDescriptor`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptor) command
- [`bluetooth.simulateService`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService) command
- {{domxref("BluetoothRemoteGATTService.getCharacteristic()")}} method
- {{domxref("BluetoothCharacteristicProperties")}} interface
- {{domxref("BluetoothRemoteGATTCharacteristic")}} interface
