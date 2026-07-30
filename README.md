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
  - trigger: state
    entity_id:
      - binary_sensor.mailbox_motion_occupancy
    from:
      - "off"
    to:
      - "on"
    id: "1"
  - trigger: state
    entity_id:
      - binary_sensor.mailbox_door_contact
    from:
      - "off"
    to:
      - "on"
    id: "2"
conditions: []
actions:
  - choose:
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
            entity_id: input_boolean.mailbox_door_opened
          - action: input_boolean.turn_on
            metadata: {}
            target:
              entity_id: input_boolean.mailbox_full
            data: {}
      - conditions:
          - condition: trigger
            id:
              - "2"
        sequence:
          - action: input_boolean.turn_off
            metadata: {}
            target:
              entity_id: input_boolean.mailbox_full
            data: {}
          - action: input_boolean.turn_on
            metadata: {}
            target:
              entity_id: input_boolean.mailbox_door_opened
            data: {}
          - delay:
              hours: 0
              minutes: 0
              seconds: 15
              milliseconds: 0
          - action: input_boolean.turn_off
            metadata: {}
            target:
              entity_id: input_boolean.mailbox_door_opened
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
