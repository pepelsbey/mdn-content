---
title: "`bluetooth` module"
short-title: bluetooth
slug: Web/WebDriver/Reference/BiDi/Modules/bluetooth
page-type: listing-page
status:
  - experimental
browser-compat: webdriver.bidi.bluetooth
spec-urls: https://bluetooth.spec.whatwg.org/#bluetooth-module
sidebar: webdriver
---

{{SeeCompatTable}}

The **`bluetooth`** module contains commands for automating Bluetooth device interactions.
It lets you test a website that uses the [Web Bluetooth API](/en-US/docs/Web/API/Web_Bluetooth_API) without Bluetooth hardware, by simulating a Bluetooth adapter and the devices around it.

## GATT operations

When the page reads the value of a simulated characteristic or descriptor, writes a descriptor, writes a characteristic with {{domxref("BluetoothRemoteGATTCharacteristic.writeValueWithResponse()", "writeValueWithResponse()")}} or {{domxref("BluetoothRemoteGATTCharacteristic.writeValueWithoutResponse()", "writeValueWithoutResponse()")}}, or starts or stops notifications, the operation goes to the simulated device.
The page's own checks can settle the call first: for example, it rejects at once if the page isn't connected to the device.
An operation that goes to the device waits until you simulate the device's response.
If you're [subscribed](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to `bluetooth.characteristicEventGenerated` or `bluetooth.descriptorEventGenerated`, you receive an event that identifies the attribute and the type of the operation, with the bytes that the page wrote for a write.
You then answer it with [`bluetooth.simulateCharacteristicResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristicResponse) or [`bluetooth.simulateDescriptorResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptorResponse):

- With the code `0`, the operation succeeds, and a read gets the bytes that you specify as the value.
- With an error code, the operation fails, and the page's call rejects.

## Commands

- [`bluetooth.simulateCharacteristicResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristicResponse)
- [`bluetooth.simulateDescriptorResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptorResponse)

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}
