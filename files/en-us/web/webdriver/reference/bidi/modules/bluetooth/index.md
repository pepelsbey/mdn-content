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

## The GATT hierarchy

As on a real device, a simulated device has GATT services, a service has characteristics, and a characteristic has descriptors.
Each of them is identified by its 128-bit UUID in lowercase, such as `0000180d-0000-1000-8000-00805f9b34fb` for the heart rate service.
Unlike on a real device, siblings can't share a UUID: a device can't have two services with the same UUID, a service two such characteristics, or a characteristic two such descriptors.
Once the page has the device from {{domxref("Bluetooth.requestDevice()", "navigator.bluetooth.requestDevice()")}}, you build the hierarchy one level at a time, before or after the page connects to the device:

- [`bluetooth.simulateService`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService) adds a service to a device, or removes one.
- [`bluetooth.simulateCharacteristic`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic) adds a characteristic to a service, with its properties, such as whether its value can be read, or removes one.
- [`bluetooth.simulateDescriptor`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptor) adds a descriptor to a characteristic, or removes one.

Removing a service or a characteristic also removes everything it contains.
Once the page is connected to the device, it finds the simulated services with {{domxref("BluetoothRemoteGATTServer.getPrimaryService()", "device.gatt.getPrimaryService()")}}, their characteristics with {{domxref("BluetoothRemoteGATTService.getCharacteristic()", "service.getCharacteristic()")}}, and their descriptors with {{domxref("BluetoothRemoteGATTCharacteristic.getDescriptor()", "characteristic.getDescriptor()")}}.
It only finds the services that it's allowed to access, such as those in the `filters` or `optionalServices` that it passed to `requestDevice()`, and none whose UUID the [GATT blocklist](https://bluetooth.spec.whatwg.org/#gatt-blocklist) excludes.

## Commands

- [`bluetooth.simulateCharacteristic`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateCharacteristic)
- [`bluetooth.simulateDescriptor`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateDescriptor)
- [`bluetooth.simulateService`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateService)

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}
