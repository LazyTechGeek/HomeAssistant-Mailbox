# Home Assistant Smart Mailbox

Automate your external mailbox with Home Assistant using Zigbee contact sensors or a PIR motion sensor. This repository contains the Home Assistant automations, ESPHome code, wiring diagram, and optional 3D printable files featured in the tutorial.

## Watch the video here:
▶️ [How to Make You Mailbox Smart with Home Assistant](https://youtu.be/gtcDuPuRiF4)

## 🖨️ 3D Print Files
Zigbee Sensor Mounts
- [Zigbee Sensor Mount Tight](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/zigbee_sensor_mount_tight.3mf)
- [Zigbee Sensor Mount Mirrored Tight](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/zigbee_sensor_mount_mirrored_tight.3mf)
- [Zigbee Sensor Mount Loose](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/zigbee_sensor_mount_loose.3mf)
- [Zigbee Sensor Mount Mirrored Loose](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/zigbee_sensor_mount_mirrored_loose.3mf)

Magnet Mounts (Loose)
- [Magnet Mount Side Loose](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/magnet_mount_side_loose.3mf)
- [Magnet Mount Side Mirrored Loose](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/magnet_mount_side_mirrored_loose.3mf)
- [Magnet Mount Top Loose](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/magnet_mount_top_loose.3mf)
- [Magnet Mount Top Mirrored Loose](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/magnet_mount_top_mirrored_loose.3mf)

Magnet Mounts (Tight)
- [Magnet Mount Side Tight](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/magnet_mount_side_tight.3mf)
- [Magnet Mount Side Mirrored tight](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/magnet_mount_side_mirrored_tight.3mf)
- [Magnet Mount Top Tight](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/magnet_mount_top_tight.3mf)
- [Magnet Mount Top Mirrored Tight](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/magnet_mount_top_mirrored_tight.3mf)

Desk Mailbox Model
- [mailbox Body](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/mailbox_body.3mf)
- [mailbox Lid](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/mailbox_lid.3mf)
- [mailbox Pole](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/mailbox_pole.3mf)
- [Flag](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/flag.3mf)
- [Washer](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/washer.3mf)
- [Mailbox Base](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/mailbox_base.3mf)
- [mailbox Base Lid](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/mailbox_base_lid.3mf)
- [Button Body](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/button_body.3mf)
- [Button Cap](https://raw.githubusercontent.com/LazyTechGeek/HomeAssistant-Mailbox/main/3d_prints/button_cap.3mf)

## Helpers Required

### For Solutions 1, 2 & 3

### Mailbox Full

- **Type:** Toggle
- **Icon:** `mdi:email-fast`
- **Entity ID:** `input_boolean.mailbox_full`

### Mailbox Status

- **Type:** Template → Sensor
- **Icon:** `mdi:email-search-outline`
- **Entity ID:** `sensor.mailbox_status`

**Template:**

```jinja
{% if is_state('input_boolean.mailbox_full', 'on') %}
Full
{% else %}
Empty
{% endif %}
```

---

### Additional Helper for Solution 3 (PIR Motion Sensor + Door Sensor)

### Mailbox Door Opened

- **Type:** Toggle
- **Icon:** `mdi:mailbox-open-outline`
- **Entity ID:** `input_boolean.mailbox_door_opened`

## Automations

### Solution 1 (Flap) + (Door)
```yaml
alias: Mailbox - Post Detected (Flap) + (Door)
description: Notify when mail arrives using a mailbox flap sensor and reset mailbox status when the collection door is opened.
mode: single
triggers:

  # Trigger 1 - Mail has been delivered
  # Replace the entity below with your mailbox flap contact sensor.

  - trigger: state
    entity_id:
      - binary_sensor.ENTER_YOUR_MAILBOX_FLAP_SENSOR
    id: "1"
    from:
      - "off"
    to:
      - "on"

  # Trigger 2 - Mail has been collected
  # Replace the entity below with your mailbox door contact sensor.

  - trigger: state
    entity_id:
      - binary_sensor.ENTER_YOUR_MAILBOX_DOOR_SENSOR
    id: "2"
    from:
      - "off"
    to:
      - "on"
conditions: []
actions:
  - choose:

  # Mail has been collected
  # Turn ON the mailbox full input boolean.

      - conditions:
          - condition: trigger
            id:
              - "1"
        sequence:
          - action: input_boolean.turn_on
            metadata: {}
            target:
              entity_id: input_boolean.ENTER_YOUR_MAILBOX_FULL_BOOLEAN
            data: {}
            
  # Mail has been collected
  # Turn OFF the mailbox full input boolean.
      
      - conditions:
          - condition: trigger
            id:
              - "2"
        sequence:
          - action: input_boolean.turn_off
            metadata: {}
            target:
              entity_id: input_boolean.ENTER_YOUR_MAILBOX_FULL_BOOLEAN
            data: {}
```

### Solution 2 (PIR)
```yaml
alias: Mailbox - Post Detected (PIR)
description: "Notify when mail arrives using a PIR sensor. Manual mailbox reset required after collecting mail"
mode: single
triggers:

  # Trigger - Mail has been delivered
  # Replace the entity below with your mailbox PIR sensor.

  - trigger: state
    entity_id:
      - binary_sensor.ENTER_YOUR_MAILBOX_PIR_SENSOR
    from:
      - "off"
    to:
      - "on"
    id: "1"
conditions: []
actions:

  # Mail has been delivered
  # Turn ON the mailbox full input boolean.

  - action: input_boolean.turn_on
    metadata: {}
    target:
      entity_id: input_boolean.ENTER_YOUR_MAILBOX_FULL_BOOLEAN
    data: {}
```

### Solution 3 (PIR + Door)
```yaml
alias: Mailbox - Post Detected (PIR) + (Door)
description: Notify when mail arrives using a PIR sensor and reset mailbox status when the collection door is opened.
mode: parallel
triggers:

  # Trigger 1 - Motion detected inside the mailbox
  # Replace the entity below with your mailbox PIR sensor.

  - trigger: state
    entity_id:
      - binary_sensor.ENTER_YOUR_MAILBOX_PIR_SENSOR
    from:
      - "off"
    to:
      - "on"
    id: "1"

  # Trigger 2 - Mailbox collection door opened
  # Replace the entity below with your mailbox door contact sensor.

  - trigger: state
    entity_id:
      - binary_sensor.ENTER_YOUR_MAILBOX_DOOR_SENSOR
    from:
      - "off"
    to:
      - "on"
    id: "2"
conditions: []
actions:
  - choose:

      # Motion detected inside the mailbox
      #
      # Wait 5 seconds before checking whether the mailbox door
      # has recently been opened. This helps prevent a false
      # notification when collecting your mail.
    
      - conditions:
          - condition: trigger
            id:
              - "1"
        sequence:
          - delay:
              hours: 0
              minutes: 0
              seconds: 5
              milliseconds: 0
          - condition: state
            state:
              - "off"            
            entity_id: input_boolean.ENTER_YOUR_MAILBOX_DOOR_OPENED_BOOLEAN

      # Turn ON the mailbox full input boolean.
          
          - action: input_boolean.turn_on
            metadata: {}
            target:
              entity_id: input_boolean.ENTER_YOUR_MAILBOX_FULL_BOOLEAN
            data: {}

      # Mailbox collection door opened
      #
      # Reset the mailbox and temporarily enable the door helper
      # so the PIR sensor ignores any movement while you are
      # collecting your post.      
      
      - conditions:
          - condition: trigger
            id:
              - "2"
        sequence:

          # Turn OFF the mailbox full input boolean.        
          - action: input_boolean.turn_off
            metadata: {}
            target:
              entity_id: input_boolean.ENTER_YOUR_MAILBOX_FULL_BOOLEAN
            data: {}
          - action: input_boolean.turn_on
            metadata: {}
            target:
              entity_id: input_boolean.ENTER_YOUR_MAILBOX_DOOR_OPENED_BOOLEAN
            data: {}

          # Keep the helper enabled for 15 seconds while the
          # mailbox is being emptied.
          
          - delay:
              hours: 0
              minutes: 0
              seconds: 15
              milliseconds: 0

          # Turn OFF the mailbox door helper ready for the next delivery.

          - action: input_boolean.turn_off
            metadata: {}
            target:
              entity_id: input_boolean.ENTER_YOUR_MAILBOX_DOOR_OPENED_BOOLEAN
            data: {}
```

---

## Bonus Project – Smart Mailbox Desk Display

### Tutorial Video:
▶️ [Coming Soon]()

### ESPHome Configuration

```yaml
substitutions:
  ############################################################
  # 1. DEVICE SETTINGS - Change these to match your device
  ############################################################

  name: YOUR_DEVICE_NAME
  friendly_name: YOUR FRIENDLY DEVICE NAME
  api_encryption_key: "YOUR_API_ENCRYPTION_KEY"
  ota_password: "YOUR_OTA_PASSWORD"
  ap_ssid: "YOUR_FALLBACK_HOTSPOT"
  ap_password: "YOUR_FALLBACK_PASSWORD"

  ############################################################
  # 2. HARDWARE SETTINGS - Change these to match your wiring
  ############################################################

  dfplayer_tx_pin: D6
  dfplayer_rx_pin: D7
  servo_pin: D1
  mailbox_reset_pin: D5

  ############################################################
  # End of substitutions
  ############################################################

esphome:
  name: ${name}
  friendly_name: ${friendly_name}

esp8266:
  board: d1_mini

wifi:
  networks:
  - ssid: !secret wifi_ssid
    password: !secret wifi_password

  ap:
    ssid: ${ap_ssid}
    password: ${ap_password}
captive_portal: # AP in case WiFi connection fails

# Enable logging
logger:

# Enable OTA firmware update
ota:
  - platform: esphome
    password: ${ota_password}

# Configure UART bus
uart:
  id: uart_bus
  tx_pin: ${dfplayer_tx_pin}
  rx_pin: ${dfplayer_rx_pin}
  baud_rate: 9600

# Configure DFPlayer Mini module
dfplayer:
  uart_id: uart_bus
  on_finished_playback:
    then:
      - logger.log: 'Playback finished event'

# Enable Home Assistant API
api:
  encryption:
    key: ${api_encryption_key}

# Define PWM output for servo
output:
  - platform: esp8266_pwm
    id: pwm_output_mailbox_flag
    pin: ${servo_pin}
    frequency: 50 Hz

# Define the servo component
servo:
  - id: servo_mailbox_flag
    output: pwm_output_mailbox_flag

# Optionally, add a number entity to control the servo
number:
  - platform: template
    name: Servo Mailbox Flag
    min_value: -100
    initial_value: 0
    max_value: 100
    step: 1
    optimistic: true
    set_action:
      then:
        - servo.write:
            id: servo_mailbox_flag
            level: !lambda 'return x / 100.0;'

  - platform: template
    name: "Audio Repeats"
    id: audio_repeats
    min_value: 1
    max_value: 3
    step: 1
    initial_value: 2
    restore_value: true
    optimistic: true

  - platform: template
    name: "Flag Up Pos"
    id: mailbox_flag_up_position
    icon: mdi:flag
    min_value: -100
    max_value: 100
    step: 1
    initial_value: 70
    restore_value: true
    optimistic: true
    mode: slider

    set_action:
      - servo.write:
          id: servo_mailbox_flag
          level: !lambda 'return x / 100.0;'

  - platform: template
    name: "Flag Down Pos"
    id: mailbox_flag_down_position
    icon: mdi:flag-outline
    min_value: -100
    max_value: 100
    step: 1
    initial_value: -40
    restore_value: true
    optimistic: true
    mode: slider

    set_action:
      - servo.write:
          id: servo_mailbox_flag
          level: !lambda 'return x / 100.0;'

  - platform: template
    name: "Volume"
    id: dfplayer_volume
    min_value: 0
    max_value: 30
    step: 5
    initial_value: 30
    optimistic: true
    mode: slider
    set_action:
      - logger.log:
          format: "Setting volume to %d"
          args: ['(int)x']
      - dfplayer.set_volume: !lambda 'return (int)x;'

button:
  - platform: template
    name: "Play You've Got Mail"
    icon: mdi:play
    on_press:
      - dfplayer.play: 1

  - platform: template
    name: "Move Flag Up"
    icon: mdi:flag
    on_press:
      - servo.write:
          id: servo_mailbox_flag
          level: !lambda |-
            return id(mailbox_flag_up_position).state / 100.0;

  - platform: template
    name: "Move Flag Down"
    icon: mdi:flag-outline
    on_press:
      - servo.write:
          id: servo_mailbox_flag
          level: !lambda |-
            return id(mailbox_flag_down_position).state / 100.0;

switch:
  - platform: template
    name: "Mail Presence"
    id: mail_presence
    icon: mdi:mailbox-up
    optimistic: true
    restore_mode: RESTORE_DEFAULT_OFF

    turn_on_action:
      - servo.write:
          id: servo_mailbox_flag
          level: !lambda |-
            return id(mailbox_flag_up_position).state / 100.0;
      - dfplayer.play: 1
      - logger.log: "Mail received: flag raised"

      - repeat:
          count: !lambda 'return (int) id(audio_repeats).state;'
          then:
            - dfplayer.play: 1
            - delay: 4s
      - logger.log: "Mail received: flag raised"

    turn_off_action:
      - dfplayer.stop
      - servo.write:
          id: servo_mailbox_flag
          level: !lambda |-
            return id(mailbox_flag_down_position).state / 100.0;
      - logger.log: "Mail collected: flag lowered"

binary_sensor:
  - platform: gpio
    name: "Reset Mailbox Status"
    id: mailbox_mailbox_status
    pin:
      number: ${mailbox_reset_pin}
      mode:
        input: true
        pullup: true
      inverted: true
    filters:
      - delayed_on_off: 20ms

    on_press:
      - switch.turn_off: mail_presence

web_server:
```


### Home Assistant Automation
```yaml
alias: Mailbox - Desk Display Integration

description: >-
  Synchronises the Home Assistant Mailbox Full helper with the desk display mailbox,
  keeping both states in sync.

mode: single

triggers:

  # Mail has been detected by the main mailbox automation
  - trigger: state
    entity_id:
      - input_boolean.mailbox_full
    from:
      - "off"
    to:
      - "on"
    id: "1"

  # Mailbox has been cleared / emptied
  - trigger: state
    entity_id:
      - input_boolean.mailbox_full
    from:
      - "on"
    to:
      - "off"
    id: "2"

  # Desk display has been manually set to show mail present
  - trigger: state
    entity_id:
      - switch.ENTER_YOUR_MAILBOX_NOTIFICATION_MAIL_PRESENCE_SWITCH
    from:
      - "off"
    to:
      - "on"
    id: "3"

  # Desk display has been manually reset
  - trigger: state
    entity_id:
      - switch.ENTER_YOUR_MAILBOX_NOTIFICATION_MAIL_PRESENCE_SWITCH
    from:
      - "on"
    to:
      - "off"
    id: "4"

conditions: []

actions:
  - choose:

      # If Home Assistant detects mail, activate the desk display
      - conditions:
          - condition: trigger
            id:
              - "1"
        sequence:
          - action: switch.turn_on
            metadata: {}
            target:
              entity_id: switch.ENTER_YOUR_MAILBOX_NOTIFICATION_MAIL_PRESENCE_SWITCH
            data: {}

      # If the mailbox is cleared, reset the desk display
      - conditions:
          - condition: trigger
            id:
              - "2"
        sequence:
          - action: switch.turn_off
            metadata: {}
            target:
              entity_id: switch.ENTER_YOUR_MAILBOX_NOTIFICATION_MAIL_PRESENCE_SWITCH
            data: {}

      # If the desk display is activated manually, update Home Assistant
      - conditions:
          - condition: trigger
            id:
              - "3"
        sequence:
          - action: input_boolean.turn_on
            metadata: {}
            target:
              entity_id: input_boolean.mailbox_full
            data: {}

      # If the desk display is manually reset, clear the Home Assistant helper
      - conditions:
          - condition: trigger
            id:
              - "4"
        sequence:
          - action: input_boolean.turn_off
            metadata: {}
            target:
              entity_id: input_boolean.mailbox_full
            data: {}
```

## License

This project is licensed under the Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC BY-NC-SA 4.0).

See the [LICENSE.md](LICENSE.md) file for full details.
