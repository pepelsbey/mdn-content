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
    Specify this when the [`type`](#type) field is `add`, and omit it when the field is `remove`.
    A property is set only if its field is `true`.
    It can contain the following fields:
    - `authenticatedSignedWrites` {{optional_inline}}
      - : A boolean that indicates whether signed writes to the characteristic's value are permitted.
    - `broadcast` {{optional_inline}}
      - : A boolean that indicates whether the characteristic's value can be broadcast.
    - `extendedProperties` {{optional_inline}}
      - : A boolean that indicates whether the characteristic has further properties, defined in its Characteristic Extended Properties descriptor.
        {{domxref("BluetoothCharacteristicProperties")}} has no property of this name.
        If this field is `true`, the page's `reliableWrite` and `writableAuxiliaries` properties come from that descriptor.
        Otherwise, they are `false`.
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
  - : A string that contains the UUID of the device's service in which to add or remove the characteristic, such as one that you added with [`bluetooth.simulateService`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService).
- `type`
  - : A string that specifies whether to add or remove the characteristic.
    It can take one of the following values:
    - `add`: Adds a characteristic with the specified [`characteristicUuid`](#characteristicuuid) and [`characteristicProperties`](#characteristicproperties) to the service.
    - `remove`: Removes the service's characteristic with the specified `characteristicUuid`, along with its descriptors.

### Return value

The `result` field in the response is an empty object (`{}`).

### Errors

- [`invalid argument`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidArgument)
  - : Thrown in any of the following cases:
    - A required parameter is missing, or a parameter has an invalid type.
    - `type` is not `add` or `remove`.
    - `type` is `add`, `characteristicProperties` is missing, and the service has no characteristic with the specified `characteristicUuid`.
    - `type` is `remove`, and `characteristicProperties` is specified.
    - `context` is not a top-level context.
    - The context has no simulated adapter.
    - The simulated adapter has no device with the specified `address`.
    - The device has no service with the specified `serviceUuid`.
- `invalid element state`
  - : Thrown in any of the following cases:
    - `type` is `add`, and the service already has a characteristic with the specified `characteristicUuid`.
    - `type` is `remove`, `characteristicProperties` is not specified, and the service has no characteristic with the specified `characteristicUuid`.
- `no such frame`
  - : No context with the given `context` ID is found.

## Description

A service has one characteristic per UUID.
Each characteristic can have descriptors, which you add using [`bluetooth.simulateDescriptor`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptor).
For more information, see [The GATT hierarchy](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_gatt_hierarchy).
To change the properties of a characteristic, remove it and then add it again with the new properties.
Removing the characteristic also removes its descriptors, so add again any descriptors that it had.
The page sees the new properties in the characteristic that it gets afterward.

Once the page is connected to the device and has the service, it finds the characteristic with {{domxref("BluetoothRemoteGATTService.getCharacteristic()", "service.getCharacteristic()")}} or {{domxref("BluetoothRemoteGATTService.getCharacteristics()", "getCharacteristics()")}}.
It doesn't find the characteristic if the [GATT blocklist](https://bluetooth.spec.whatwg.org/#gatt-blocklist) excludes its UUID.
Once you remove the characteristic, the page no longer finds it.

When the page reads or writes the characteristic's value, or starts or stops notifications, the operation goes to the simulated device.
The page's own checks can settle the call first, for example when the page isn't connected to the device.
An operation that goes to the device waits until you answer it with [`bluetooth.simulateCharacteristicResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristicResponse).
If you're subscribed to [`bluetooth.characteristicEventGenerated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/characteristicEventGenerated), you receive an event that says which operation the page started.

## Examples

### Adding a characteristic

Assume you have a [WebDriver BiDi connection](/en-US/docs/Web/WebDriver/How_to/Create_BiDi_connection), an [active session](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/new), and a [subscription](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to [`bluetooth.requestDevicePromptUpdated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/requestDevicePromptUpdated) and [`bluetooth.gattConnectionAttempted`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/gattConnectionAttempted).

Assume also that a tab is open, showing a page from `https://example.com`.
You opened the tab using [`browsingContext.create`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/create), which returned its context ID.
You then loaded the page in the tab using [`browsingContext.navigate`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/navigate).
You simulated a powered-on adapter for the tab using [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter).
You then added a heart rate monitor with the address `09:09:09:09:09:09` and the heart rate service in `knownServiceUuids` using [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral).
In response to a user's action, the page called {{domxref("Bluetooth.requestDevice()", "navigator.bluetooth.requestDevice()")}} with a filter for the heart rate service.
You chose the monitor in the [device prompt](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_device_prompt) using [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt).
You then added a heart rate service to the monitor using [`bluetooth.simulateService`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService).
When the page called {{domxref("BluetoothRemoteGATTServer.connect()", "device.gatt.connect()")}}, you received a `bluetooth.gattConnectionAttempted` event.
You answered it using [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse) with the code `0`, so the page is connected to the monitor.
The page got the heart rate service using `device.gatt.getPrimaryService()`.
The service has no characteristics yet.

Send the following message to add a heart rate measurement characteristic that supports notifications:

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

If the page now calls `service.getCharacteristic("00002a37-0000-1000-8000-00805f9b34fb")`, the promise resolves with the characteristic.
In the characteristic's `properties` object, `notify` is `true` and the other properties are `false`.

### Removing a characteristic

Using the same connection, session, subscriptions, and tab as in the first example, with the page still connected to the monitor, send the following message to remove the heart rate measurement characteristic:

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

The service has no characteristics left.
If the page now calls `service.getCharacteristics()`, the promise rejects with a `NotFoundError` {{domxref("DOMException")}}.

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
