[Velux](https://www.velux.com/) integration for Home Assistant allows you to connect to a Velux KLF 200 interface, to control [io-homecontrol](http://www.io-homecontrol.com) devices like windows and blinds. The module allows you to start scenes configured within KLF 200.

At least firmware version > 2.0.0.0 is required on the KLF 200 device. The firmware images may be obtained [here](https://www.velux.com/klf200) and may be imported via the webinterface of your KLF 200.

## This is a Fork from hass velux component which extend rain sensor functionality!

There is currently support for the following device types within Home Assistant:

- Cover
- Light
- Scene

## Configuration

A `velux` section must be present in the `configuration.yaml` file and contain the following options as required:

```yaml
# Example configuration.yaml entry
velux2:
  host: IP_ADDRESS
  password: VELUX_PASSWORD
```

```yaml
host:
  description: The IP address or hostname of the KLF 200 to use.
  required: true
  type: string
password:
  description: The password of the KLF 200 interface. Note that this is the same as the Wi-Fi password (in the upper box on the back), *not* the password for the web login.
  required: true
  type: string
```
## Services

### Service `velux.reboot_gateway`

Reboots the configured KLF 200 Gateway.

There is a problem with the KLF 200 gateway where the connection cannot be established after a restart of Home Assistant, only a manual power off and on fixes this.
As a workaround, you can use an automation to force a restart of the KLF 200 before exiting Home Assistant, like this:

```yaml
automation:
  alias: KLF reboot on hass stop event
  description: Reboots the KLF200 in order to avoid SSL Handshake issue
  trigger:
    - platform: homeassistant
      event: shutdown
  action:
    - service: velux.reboot_gateway
```

## Attributes

Cover entity with integrated rain sensor has extra attributes to represent the state of the limitation sensor.

| Name | Description |
| ---- | ----------- |
| `limitation_min` | Min limitation of window. `limitation_min` => 0 no rain; `limitation_min` => 93 the window can only open for 7 % , so it means, its raining
| `limitation_max` | Max limitation of window.

The value can be used as a `binary_sensor` for rain detection `True` or no rain `False`

```yaml
binary_sensor:
  - platform: template
    sensors:
      rainsensor_kitchen:
        device_class: moisture
        value_template: >
          {{ state_attr('cover.kitchen', 'limitation_min') >= 93 }}
```

## Velux Active (KIX 300)

The Velux Active (KIX 300) set is not supported by this integration. To integrate Velux Active (KIX 300) with Home Assistant, you can use the [HomeKit Controller](/integrations/homekit_controller) integration and get full control over your windows, curtains, covers, the air quality sensor KLA 300, etc.

Add the Velux Active gateway using HomeKit pairing (with the pairing code on the sticker at the bottom of the Velux Active gateway) and the devices connected to the gateway - including sensors - will be automatically discovered and added to Home Assistant.
