---
title: "`bluetooth.simulateAdvertisement` command"
short-title: simulateAdvertisement
slug: Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdvertisement
page-type: webdriver-command
status:
  - experimental
browser-compat: webdriver.bidi.bluetooth.simulateAdvertisement
sidebar: webdriver
---

{{SeeCompatTable}}

The `bluetooth.simulateAdvertisement` [command](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules#commands) of the [`bluetooth`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth) module simulates an advertisement packet from a Bluetooth device.
The [simulated adapter](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#simulated_adapter) of the specified [top-level context](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext#top-level_context) receives the packet.

## Syntax

```json-nolint
/* With required parameters */
{
  "method": "bluetooth.simulateAdvertisement",
  "params": {
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "scanEntry": {
      "deviceAddress": "09:09:09:09:09:09",
      "rssi": -50,
      "scanRecord": {}
    }
  }
}

/* With required and optional parameters */
{
  "method": "bluetooth.simulateAdvertisement",
  "params": {
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "scanEntry": {
      "deviceAddress": "09:09:09:09:09:09",
      "rssi": -50,
      "scanRecord": {
        "appearance": 832,
        "manufacturerData": [{ "data": "AP8BAX8=", "key": 17 }],
        "name": "Heart Rate Monitor",
        "uuids": ["0000180d-0000-1000-8000-00805f9b34fb"]
      }
    }
  }
}
```

### Parameters

The `params` field contains:

- `context`
  - : A string that contains the ID of the [top-level context](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext#top-level_context) whose simulated adapter receives the advertisement.
    Context IDs are returned by commands such as [`browsingContext.getTree`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/getTree).
- `scanEntry`
  - : An object that describes the advertisement.
    It contains the following fields:
    - `deviceAddress`
      - : A string that contains the Bluetooth address of the device that sends the advertisement, such as `09:09:09:09:09:09`.
    - `rssi`
      - : A number that specifies the power at which the advertisement is received, in dBm.
    - `scanRecord`
      - : An object that contains the data of the advertisement packet.
        It can be empty, or contain the following fields:
        - `appearance` {{optional_inline}}
          - : A number that specifies the device's appearance, one of the values defined for the GAP Appearance characteristic, such as `832` for a heart rate sensor.
        - `manufacturerData` {{optional_inline}}
          - : An array of objects, each containing manufacturer-specific data that the device advertises.
            Each object contains the following fields:
            - `data`
              - : A string that contains the data, [base64](/en-US/docs/Glossary/Base64)-encoded.
            - `key`
              - : A non-negative integer that specifies the company identifier code of the manufacturer, as assigned by the Bluetooth SIG.
        - `name` {{optional_inline}}
          - : A string that contains the device's local name, or the beginning of it.
        - `uuids` {{optional_inline}}
          - : An array of strings that contains the UUIDs of the services that the device's GATT server supports, according to the advertisement.

### Return value

The `result` field in the response is an empty object (`{}`).

### Errors

- [`invalid argument`](/en-US/docs/Web/WebDriver/Reference/Errors/InvalidArgument)
  - : Thrown in any of the following cases:
    - A required parameter is missing, or a parameter has an invalid type.
    - `context` is not a top-level context.
    - The context has no simulated adapter.
- `no such frame`
  - : No context with the given `context` ID is found.

## Description

Before you simulate an advertisement, create a simulated adapter for the context with [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter).

If the simulated adapter has no device with the specified `deviceAddress`, the command adds a [simulated device](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#simulated_devices) with that address, as if the adapter had discovered it.

A page receives an `advertisementreceived` event for the advertisement if it was given the device, for example by {{domxref("Bluetooth.requestDevice()", "navigator.bluetooth.requestDevice()")}}, and is watching the device's advertisements.
The page watches advertisements from the time it calls `watchAdvertisements()` on its {{domxref("BluetoothDevice")}} object until it aborts the watch, or until it's hidden or no longer fully active.
This applies to the pages in the top-level context and in its child contexts.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter) command
- [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral) command
- [Web Bluetooth API](/en-US/docs/Web/API/Web_Bluetooth_API)
