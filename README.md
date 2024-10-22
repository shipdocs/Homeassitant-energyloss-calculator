# Homeassitant-energyloss-calculator
My first try at calculating the energy loss for my home


U need to create some helpers/sensors as there is no other way:
U can create them in configuration.yaml or via helper (im a noob so normally use that)

# Template sensor for calculating heat loss rate in W/°C
template:
  - sensor:
      - name: "Heat Loss Rate (W/°C)"
        state: >
          {% set outdoor_temp = states('sensor.outdoor_temperature') | float %}
          {% set indoor_temp = states('sensor.indoor_temperature') | float %}
          {% set power_usage = states('sensor.power_usage') | float %}
          {% set temp_diff = indoor_temp - outdoor_temp %}
          {% if temp_diff > 0 and power_usage > 0 %}
            {{ (power_usage / temp_diff) }}
          {% else %}
            0
          {% endif %}
        unit_of_measurement: "W/°C"

# Utility meters to track heat loss over time
utility_meter:
  daily_heat_loss:
    source: sensor.heat_loss_rate
    cycle: daily

  weekly_heat_loss:
    source: sensor.heat_loss_rate
    cycle: weekly

  monthly_heat_loss:
    source: sensor.heat_loss_rate
    cycle: monthly

  yearly_heat_loss:
    source: sensor.heat_loss_rate
    cycle: yearly

# Example of a template sensor for annual heat loss in kWh/m²
template:
  - sensor:
      - name: "Annual Heat Loss (kWh/m²)"
        state: >
          {% set avg_temp_diff = 15 %}
          {% set area = 100 %}  # Replace with your home's floor area in m²
          {% set total_energy_loss = (states('sensor.heat_loss_rate') | float * avg_temp_diff * 2000) / 1000 %}
          {{ total_energy_loss / area }}
        unit_of_measurement: "kWh/m²/year"
