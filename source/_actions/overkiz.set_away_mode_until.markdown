---
title: "Set away mode until"
action: overkiz.set_away_mode_until
domain: overkiz
description: "Puts a supported water heater in away mode until a chosen date and time."
since: "2026.12"
related_actions:
  - water_heater.set_away_mode
---

Use this action to put a supported water heater in away mode until a date and time you choose, instead of the fixed one-year end date used by [Set water heater away mode](/actions/water_heater.set_away_mode/).

This is useful when you only want to heat water during certain times of day, for example overnight and in the afternoon. At the end of each heating window, an automation can call this action to pause heating until the next window starts.

This action is available only for water heaters that store away mode dates on the device itself. If your water heater does not support it, the action returns an error.

{% include actions/ui_header.md %}

To use this action in an automation or script:

1. Go to {% my automations title="**Settings** > **Automations & scenes**" %}.
2. Open an existing automation or script, or select **Create automation** > **Create new automation**.
3. If you're creating an automation, add a trigger in the **When** section.
4. In the **Then do** section, select **Add action**.
5. Select what you want to control. Under **By target** (see [Targets](#targets)), pick the water heater you want to control.
6. From the actions shown for that target, under **Overkiz**, select **Set away mode until**.
7. Under **End**, set the date and time away mode should end. Optionally, under **Start**, set when away mode should begin.
8. Select **Save**.

### Options in the UI

{% options_ui %}
End:
  description: The date and time away mode ends. Must be in the future and no more than 365 days from now.
  required: true
Start:
  description: The date and time away mode begins. Defaults to now.
  required: false
{% endoptions_ui %}

{% include actions/yaml_header.md %}

In YAML, refer to this action as `overkiz.set_away_mode_until`. A basic example looks like this:

{% example %}
action: |
  action: overkiz.set_away_mode_until
  target:
    entity_id: water_heater.patio_water_heating
  data:
    end: "2026-12-15 06:00:00"
{% endexample %}

This puts `water_heater.patio_water_heating` in away mode now, until December 15, 2026 at 6:00 AM.

### Options in YAML

{% options_yaml %}
end:
  description: The date and time away mode ends. Must be in the future and no more than 365 days from now.
  required: true
  type: datetime
start:
  description: The date and time away mode begins. Defaults to now.
  required: false
  type: datetime
{% endoptions_yaml %}

{% include actions/targets.md entity_type="supported Overkiz water heater" example="water_heater.patio_water_heating" %}

## Good to know

- The end date and time replace the fixed one-year end date that [Set water heater away mode](/actions/water_heater.set_away_mode/) always uses.
- The **Start** and **End** date and time are stored on the device itself.

{% include actions/try_it.md %}

{% include actions/more_examples.md %}

### Automation: pause heating between two daily windows

If your water heater only needs to heat water overnight and in the afternoon, you can use this action to pause heating right after each window and resume it at the start of the next one.

- **Trigger**: Time
  - **At**: `08:00:00`
- **Action**: Overkiz: Set away mode until
  - **Target**: Patio water heating
  - **End**: `{% raw %}{{ today_at('14:00:00').isoformat() }}{% endraw %}`

{% details "YAML example for pausing heating until the next window" %}

{% example %}
automation: |
  alias: "Water heater: pause until the afternoon window"
  triggers:
    - trigger: time
      at: "08:00:00"
  actions:
    - action: overkiz.set_away_mode_until
      target:
        entity_id: water_heater.patio_water_heating
      data:
        end: "{% raw %}{{ today_at('14:00:00').isoformat() }}{% endraw %}"
{% endexample %}

{% enddetails %}

### Automation: resume the following morning

At the end of the afternoon heating window, pause heating until the next morning's window starts.

- **Trigger**: Time
  - **At**: `20:00:00`
- **Action**: Overkiz: Set away mode until
  - **Target**: Patio water heating
  - **End**: `{% raw %}{{ (today_at('06:00:00') + timedelta(days=1)).isoformat() }}{% endraw %}`

{% details "YAML example for resuming the following morning" %}

{% example %}
automation: |
  alias: "Water heater: pause until the next morning window"
  triggers:
    - trigger: time
      at: "20:00:00"
  actions:
    - action: overkiz.set_away_mode_until
      target:
        entity_id: water_heater.patio_water_heating
      data:
        end: "{% raw %}{{ (today_at('06:00:00') + timedelta(days=1)).isoformat() }}{% endraw %}"
{% endexample %}

{% enddetails %}

{% include actions/stuck.md %}

{% include actions/related.md %}
