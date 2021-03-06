---
layout: page
title: "Manual Alarm Control Panel"
description: "Instructions how to integrate manual alarms into Home Assistant."
date: 2015-10-13 19:10
sidebar: true
comments: false
sharing: true
footer: true
logo: home-assistant.png
ha_category: Alarm
ha_release: 0.7.6
---


This platform enables you to set manual alarms in Home Assistant.

```yaml
# Example configuration.yaml entry
alarm_control_panel:
  - platform: manual
```

Configuration variables:

- **name** (*Optional*): The name of the alarm. Default is "HA Alarm".
- **code** (*Optional*): If defined, specifies a code to enable or disable the alarm in the frontend.
- **pending_time** (*Optional*): The time in seconds of the pending time before arming the alarm. Default is 60 seconds.
- **trigger_time** (*Optional*): The time in seconds of the trigger time in which the alarm is firing. Default is 120 seconds.
- **disarm_after_trigger** (*Optional*): If true, the alarm will automatically disarm after it has been triggered instead of returning to the previous state.
- **armed_home/armed_away/armed_night/triggered** (*Optional*): State specific settings
  - **pending_time**: State specific pending time override.

In the config example below, armed_home state will have no pending time and triggered state will have pending time of 20 second whereas armed_away state will have a default pending time of 30 seconds.

```yaml
# Example configuration.yaml entry
alarm_control_panel:
  - platform: manual
    name: Home Alarm
    code: 1234
    pending_time: 30
    armed_home:
      pending_time: 0
    triggered:
      pending_time: 20
    trigger_time: 4 
```

## {% linkable_title Examples %}

In this section, you find some real-life examples of how to use this panel.

### {% linkable_title Sensors %}

Using sensors to trigger the alarm.

```yaml
automation:
- alias: 'Trigger alarm while armed away'
  trigger:
    - platform: state
      entity_id: sensor.pir1
      to: 'active'
    - platform: state
      entity_id: sensor.pir2
      to: 'active'
    - platform: state
      entity_id: sensor.door
      to: 'open'
    - platform: state
      entity_id: sensor.window
      to: 'open'
  condition:
    - condition: state
      entity_id: alarm_control_panel.ha_alarm
      state: armed_away
  action:
    service: alarm_control_panel.alarm_trigger
    entity_id: alarm_control_panel.ha_alarm
```

Sending a notification when the alarm is triggered.

```yaml
automation:
  - alias: 'Send notification when alarm triggered'
    trigger:
      - platform: state
        entity_id: alarm_control_panel.ha_alarm
        to: 'triggered'
    action:
      - service: notify.notify
        data:
          message: "ALARM! The alarm has been triggered"
```

Disarming the alarm when the door is properly unlocked.

```yaml
automation:
  - alias: 'Disarm alarm when door unlocked by keypad'
    trigger:
      - platform: state
        entity_id: sensor.front_door_lock_alarm_type
        to: '19'
        # many z-wave locks use Alarm Type 19 for 'Unlocked by Keypad'
    action:
      - service: alarm_control_panel.alarm_disarm
        entity_id: alarm_control_panel.house_alarm
```
