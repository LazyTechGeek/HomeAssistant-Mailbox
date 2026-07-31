# HomeAssistant-Mailbox
Home Assistant Mailbox

## Automations

### Option A (Flap) + (Door)
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

### Option B (PIR)
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

### Option C (PIR + Door)
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

### Mailbox - Desk Display Integration
```yaml
alias: Mailbox - Desk Display Integration
description: >-
  Synchronises the Home Assistant Mailbox Full helper with the desk display mailbox, keeping both states in sync
  opened
mode: single
triggers:
  - trigger: state
    entity_id:
      - input_boolean.mailbox_full
    from:
      - "off"
    to:
      - "on"
    id: "1"
  - trigger: state
    entity_id:
      - input_boolean.mailbox_full
    from:
      - "on"
    id: "2"
    to:
      - "off"
  - trigger: state
    entity_id:
      - switch.bedroom_mailbox_notification_mail_presence
    from:
      - "off"
    id: "3"
    to:
      - "on"
  - trigger: state
    entity_id:
      - switch.bedroom_mailbox_notification_mail_presence
    from:
      - "on"
    id: "4"
    to:
      - "off"
conditions: []
actions:
  - choose:
      - conditions:
          - condition: trigger
            id:
              - "1"
        sequence:
          - action: switch.turn_on
            metadata: {}
            target:
              entity_id: switch.bedroom_mailbox_notification_mail_presence
            data: {}
      - conditions:
          - condition: trigger
            id:
              - "2"
        sequence:
          - action: switch.turn_off
            metadata: {}
            target:
              entity_id: switch.bedroom_mailbox_notification_mail_presence
            data: {}
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

## BONUS CONTENT (MODEL MAILBOX)

### Code for mailbox model - ESPHome
```yaml
substitutions:
  name: mailbox-notification
  friendly_name: mailbox_notification

esphome:
  name: ${name}
  friendly_name: ${friendly_name}


esp8266:
  board: nodemcuv2
  framework:
    version: recommended

wifi:
  networks:
  - ssid: !secret wifi_ssid
    password: !secret wifi_password



  ap:
    ssid: "Mailbox-Notification"
    password: "7xfZNY3cPar3"
captive_portal: # AP in case WiFi connection fails

# Enable logging
logger:

# Enable OTA firmware update
ota:
  - platform: esphome
    password: "d2f26db7e3e2f529b59a143c9deea43d"


# Configure UART bus
uart:
  id: uart_bus
  tx_pin: D6  # D5
  rx_pin: D7  # D6
  baud_rate: 9600

# Configure DFPlayer Mini module
dfplayer:
  uart_id: uart_bus
  on_finished_playback:
    then:
      - logger.log: 'Playback finished event'

# Set initial volume to maximum
#interval:
#  - interval: 1s
#    then:
#      - dfplayer.set_volume: 30

# Enable Home Assistant API
api:
  services:
    - service: dfplayer_next
      then:
        - dfplayer.play_next
    - service: dfplayer_previous
      then:
        - dfplayer.play_previous
    - service: dfplayer_play
      variables:
        file: int
      then:
        - dfplayer.play: !lambda 'return file;'
    - service: dfplayer_play_loop
      variables:
        file: int
        loop_: bool
      then:
        - dfplayer.play:
            file: !lambda 'return file;'
            loop: !lambda 'return loop_;'
    - service: dfplayer_play_folder
      variables:
        folder: int
        file: int
      then:
        - dfplayer.play_folder:
            folder: !lambda 'return folder;'
            file: !lambda 'return file;'
    - service: dfplayer_play_loop_folder
      variables:
        folder: int
      then:
        - dfplayer.play_folder:
            folder: !lambda 'return folder;'
            loop: true
    - service: dfplayer_set_device_tf
      then:
        - dfplayer.set_device: TF_CARD
    - service: dfplayer_set_device_usb
      then:
        - dfplayer.set_device: USB
    - service: dfplayer_set_volume
      variables:
        volume: int
      then:
        - dfplayer.set_volume: !lambda 'return volume;'
    - service: dfplayer_set_eq
      variables:
        preset: int
      then:
        - dfplayer.set_eq: !lambda 'return static_cast<dfplayer::EqPreset>(preset);'
    - service: dfplayer_sleep
      then:
        - dfplayer.sleep
    - service: dfplayer_reset
      then:
        - dfplayer.reset
    - service: dfplayer_start
      then:
        - dfplayer.start
    - service: dfplayer_pause
      then:
        - dfplayer.pause
    - service: dfplayer_stop
      then:
        - dfplayer.stop
    - service: dfplayer_random
      then:
        - dfplayer.random
    - service: dfplayer_volume_up
      then:
        - dfplayer.volume_up
    - service: dfplayer_volume_down
      then:
        - dfplayer.volume_down


# Define PWM output for servo
output:
  - platform: esp8266_pwm
    id: pwm_output_mailbox_flag
    pin: D1  # D3 pin on the D1 Mini
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

  #- platform: template
  #  name: "Stop Audio"
  #  icon: mdi:stop
  #  on_press:
  #    - dfplayer.stop

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
    name: "Mailbox Reset Button"
    id: mailbox_reset_button
    pin:
      number: D5
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
