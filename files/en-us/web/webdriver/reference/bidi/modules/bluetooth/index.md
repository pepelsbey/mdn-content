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

## Simulated devices

A [simulated adapter](#simulated_adapter) has a set of simulated devices, each identified by its Bluetooth address, such as `09:09:09:09:09:09`.
To add a device, with its name, the services it advertises, and its manufacturer data, use [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral).
Once the device is chosen in the [device prompt](#the_device_prompt), the page can connect to it.
An advertisement from a new address, which you simulate with [`bluetooth.simulateAdvertisement`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdvertisement), also adds a device.

## GATT connections

With a simulated adapter, when a page calls {{domxref("BluetoothRemoteGATTServer.connect()", "device.gatt.connect()")}} for a device that isn't connected, the browser sends a `bluetooth.gattConnectionAttempted` event, with the context ID and the device address, to the sessions that are [subscribed](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to it.
The call then waits until you simulate the device's response.
You then decide the outcome:

- To let the connection succeed, or to make it fail with an error code so that `connect()` rejects with a `NetworkError` {{domxref("DOMException")}}, send [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse).
- To make the attempt fail as if the device couldn't respond, send [`bluetooth.simulateGattDisconnection`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattDisconnection).

Once the page is connected, `bluetooth.simulateGattDisconnection` disconnects the device, as if it went out of range: the page receives a `gattserverdisconnected` event.

## Commands

- [`bluetooth.simulateAdvertisement`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdvertisement)
- [`bluetooth.simulateGattConnectionResponse`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattConnectionResponse)
- [`bluetooth.simulateGattDisconnection`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateGattDisconnection)
- [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral)

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}
