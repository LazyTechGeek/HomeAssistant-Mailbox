# HomeAssistant-Mailbox
Home Assistant Mailbox

## Automations

### Option A (Flap)
```yaml
alias: Mailbox - Post Detected (Flap)
description: Notify mail arrived when mailbox flap opened
mode: single
triggers:
  - trigger: state
    entity_id:
      - binary_sensor.mailbox_flap_contact
    id: "1"
    from:
      - "off"
    to:
      - "on"
  - trigger: state
    entity_id:
      - binary_sensor.mailbox_door_contact
    id: "2"
    from:
      - "off"
    to:
      - "on"
conditions: []
actions:
  - choose:
      - conditions:
          - condition: trigger
            id:
              - "1"
        sequence:
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
```

### Option B (PIR)
```yaml
alias: Mailbox - Post Detected (PIR)
description: "Notify mail arrived when PIR detects motion"
mode: single
triggers:
  - trigger: state
    entity_id:
      - binary_sensor.mailbox_motion_occupancy
    from:
      - "off"
    to:
      - "on"
    id: "1"
conditions: []
actions:
  - action: input_boolean.turn_on
    metadata: {}
    target:
      entity_id: input_boolean.mailbox_full
    data: {}
```

### Option C (PIR + Door)
```yaml
alias: Mailbox - Post Detected (PIR) + (Door)
description: Notify mail arrived when PIR detects motion and reset mailbox status when door opened
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
