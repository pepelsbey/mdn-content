---
title: "`bluetooth.requestDevicePromptUpdated` event"
short-title: requestDevicePromptUpdated
slug: Web/WebDriver/Reference/BiDi/Modules/bluetooth/requestDevicePromptUpdated
page-type: webdriver-event
spec-urls: https://bluetooth.spec.whatwg.org/#cddl-type-bluetoothrequestdevicepromptupdated
sidebar: webdriver
---

The `bluetooth.requestDevicePromptUpdated` [event](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules#events) of the [`bluetooth`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth) module fires when the browser opens a [device prompt](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#the_device_prompt) because a page called {{domxref("Bluetooth.requestDevice()", "navigator.bluetooth.requestDevice()")}}.
It can fire again for the same prompt while the prompt is open, with an updated list of devices.

## Event data

The `params` field in the event notification is an object with the following fields:

- `context`
  - : A string that contains the ID of the context in which the page called `requestDevice()`.
    Context IDs are returned by commands such as [`browsingContext.getTree`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/browsingContext/getTree).
    To close the prompt, specify this ID as the `context` of a [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt) command.
- `devices`
  - : An array of objects, each representing a device that the prompt lists.
    The devices match the options that the page passed to `requestDevice()`.
    It can be empty: the browser can open the prompt even if no device matches.
    Each object contains the following fields:
    - `id`
      - : A string that contains the ID of the device.
        To choose the device, specify this ID as the `device` of a `bluetooth.handleRequestDevicePrompt` command.
    - `name`
      - : A string that contains the name of the device.
        If the device has no name, the value is `null` instead of a string.
- `prompt`
  - : A string that contains the ID of the device prompt.
    Every event for the same prompt has the same ID.
    To close the prompt, specify this ID as the `prompt` of a `bluetooth.handleRequestDevicePrompt` command.

## Description

When a page calls `requestDevice()` in response to a user's action, the browser looks for the devices that match the options that the page passed.
It then opens a device prompt that lists them, so that the user can choose one.
If you're [subscribed](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to `bluetooth.requestDevicePromptUpdated`, you receive this event when the prompt opens.
While the prompt is open, the browser can send the event again with the same prompt ID and an updated `devices` array.
Use the devices from the latest event.
With a [simulated adapter](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth#simulated_adapter), the list includes the simulated devices that match.

To close the prompt as the user would, send `bluetooth.handleRequestDevicePrompt` with the IDs from the event.
The command either chooses one of the listed devices or cancels the prompt.
Subscribe to the event before the page calls `requestDevice()` so that you receive the IDs that the command needs.

You receive no event if `requestDevice()` rejects before the browser opens a prompt, for example in a page that isn't allowed to use Bluetooth.

## Examples

### Receiving the devices that a prompt lists

Assume you have a [WebDriver BiDi connection](/en-US/docs/Web/WebDriver/How_to/Create_BiDi_connection), an [active session](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/new), and a [subscription](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) to `bluetooth.requestDevicePromptUpdated`.

Assume also that a tab is open, showing a page from `https://example.com`.
You simulated a powered-on adapter for the tab using [`bluetooth.simulateAdapter`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulateAdapter).
You then used [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral) to add a device named `Heart Rate Monitor` with the address `09:09:09:09:09:09` and the heart rate service (`0000180d-0000-1000-8000-00805f9b34fb`).

The page asks for a heart rate monitor.
It can do so only in response to a user's action, such as a click, or to a [`script.callFunction`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/script/callFunction) command with `userActivation` set to `true`:

```js
const device = await navigator.bluetooth.requestDevice({
  filters: [{ services: ["heart_rate"] }],
});
```

The browser opens a device prompt and sends the following event, which lists the heart rate monitor:

```json
{
  "type": "event",
  "method": "bluetooth.requestDevicePromptUpdated",
  "params": {
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "devices": [
      {
        "id": "b1a93982-2255-41f6-9754-17304905d3bc",
        "name": "Heart Rate Monitor"
      }
    ],
    "prompt": "6faef7d4-09cc-419c-813d-8ce550e9783f"
  }
}
```

To choose the monitor, send a [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt) command with this `context` and `prompt`, `accept` set to `true`, and the monitor's `id` as `device`.
The prompt closes, and `requestDevice()` resolves with a {{domxref("BluetoothDevice")}} object whose {{domxref("BluetoothDevice.name", "name")}} is `Heart Rate Monitor`.

### Receiving an updated list of devices

Using the same connection, session, and subscription as in the first example, suppose you added a second device named `Chest Strap` with the address `10:10:10:10:10:10` and the heart rate service.
The page then calls `requestDevice()` again.
The browser opens a new device prompt.
The first event for a prompt can list only some of the matching devices; here it lists only the chest strap:

```json
{
  "type": "event",
  "method": "bluetooth.requestDevicePromptUpdated",
  "params": {
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "devices": [
      {
        "id": "2008afd8-9370-48b2-8f13-331ee7560b6c",
        "name": "Chest Strap"
      }
    ],
    "prompt": "48fbdba6-45c9-4cf7-8ba4-f86bc07ab8c1"
  }
}
```

The browser then sends the event again, with the same prompt ID and both devices:

```json
{
  "type": "event",
  "method": "bluetooth.requestDevicePromptUpdated",
  "params": {
    "context": "93ee5bd6-d256-4608-a002-9a8995cc0e5f",
    "devices": [
      {
        "id": "b1a93982-2255-41f6-9754-17304905d3bc",
        "name": "Heart Rate Monitor"
      },
      {
        "id": "2008afd8-9370-48b2-8f13-331ee7560b6c",
        "name": "Chest Strap"
      }
    ],
    "prompt": "48fbdba6-45c9-4cf7-8ba4-f86bc07ab8c1"
  }
}
```

The latest event has the list to choose from.
To test how the page behaves when the user closes the prompt without choosing a device, send a [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt) command with this `context` and `prompt`, and `accept` set to `false`.
The prompt closes, and `requestDevice()` rejects with a `NotFoundError` {{domxref("DOMException")}}.

## Specifications

{{Specifications}}

## Browser compatibility

{{Compat}}

## See also

- [`bluetooth.handleRequestDevicePrompt`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/handleRequestDevicePrompt) command
- [`bluetooth.simulatePreconnectedPeripheral`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/bluetooth/simulatePreconnectedPeripheral) command
- [`session.subscribe`](/en-US/docs/Web/WebDriver/Reference/BiDi/Modules/session/subscribe) command
- {{domxref("Bluetooth.requestDevice()")}} method
