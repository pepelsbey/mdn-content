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

When the page performs one of the following operations on a simulated device, the operation goes to the device:

- Reading the value of a characteristic or descriptor
- Writing a descriptor
- Writing a characteristic with {{domxref("BluetoothRemoteGATTCharacteristic.writeValueWithResponse()", "writeValueWithResponse()")}} or {{domxref("BluetoothRemoteGATTCharacteristic.writeValueWithoutResponse()", "writeValueWithoutResponse()")}}
- Starting or stopping notifications

The page's own checks can settle the call first.
For example, the call rejects at once if the page isn't connected to the device.
An operation that goes to the device waits until you simulate the device's response.

If you're [subscribed](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to [`bluetooth.characteristicEventGenerated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/characteristicEventGenerated) or [`bluetooth.descriptorEventGenerated`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/descriptorEventGenerated), you receive an event that identifies the attribute and the type of the operation.
For a write, the event also contains the bytes that the page wrote.

You answer the operation using [`bluetooth.simulateCharacteristicResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristicResponse) or [`bluetooth.simulateDescriptorResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptorResponse):

- With the code `0`, the operation succeeds, and a read gets the bytes that you specify as the value.
- With an error code, the operation fails, and the page's call rejects.

## Commands

- [`bluetooth.simulateCharacteristicResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristicResponse)
- [`bluetooth.simulateDescriptorResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptorResponse)

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}
