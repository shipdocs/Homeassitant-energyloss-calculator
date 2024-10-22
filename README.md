# Homeassitant-energyloss-calculator
My first try at calculating the energy loss for my home


U need to create some helpers/sensors as there is no other way:
U can create them in configuration.yaml or via helper see the bottom of this readme for instructions (im a noob so normally use that)

Steps for GUI Users to Import a Blueprint:
Go to the Blueprints Section:

In Home Assistant, open the Settings menu from the sidebar.
Click on Blueprints.
Import Blueprint:

In the Blueprints menu, click on Import Blueprint in the top-right corner.
Paste the RAW Link:

(https://raw.githubusercontent.com/shipdocs/Homeassitant-energyloss-calculator/refs/heads/main/blueprints/automation/heat_loss_blueprint/blueprint.yaml)
Click Preview to verify the blueprint.
Click Import:

Once the blueprint is successfully previewed, click Import to add it to your Home Assistant instance.

Create Automations Using the Blueprint:

After importing, you can create new automations using the blueprint by going to Settings > Automations & Scenes > + Create Automation and selecting Start with Blueprint.
Then select the imported Heat Loss Calculation Blueprint.

Fill in Your Sensor Details:

In the automation configuration, fill in the details like indoor temperature, outdoor temperature, and power usage sensors, as well as your home’s floor area and average temperature difference.

Add sensors via configuration.yaml
???????????????????????????????????????????????????????????????????????
For it to work u need at least one sensor template:

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

?????????????????????????????????????????????????????????????
GUI users like me :)

Step 1: Create the Heat Loss Rate Sensor
Go to Settings > Devices & Services > Helpers.

Click on + Add Helper > Template > Sensor.

Fill in the Template Sensor:

Name: Heat Loss Rate (W/°C)

{% set outdoor_temp = states('sensor.outdoor_temperature') | float %}   /// change the sensors to your own
{% set indoor_temp = states('sensor.indoor_temperature') | float %}     /// change the sensors to your own
{% set power_usage = states('sensor.power_usage') | float %}            /// change the sensors to your own
{% set temp_diff = indoor_temp - outdoor_temp %}
{% if temp_diff > 0 and power_usage > 0 %}
  {{ (power_usage / temp_diff) }}
{% else %}
  0
{% endif %}
Unit of Measurement: W/°C
Save the Template Sensor.

Step 2: Create Utility Meters for Daily, Weekly, Monthly, and Yearly Tracking
Go to Settings > Devices & Services > Helpers.

Click on + Add Helper > Utility Meter.

Create Utility Meters:

Daily Utility Meter:
Source Sensor: sensor.heat_loss_rate
Cycle: Daily
Name: Daily Heat Loss
Weekly Utility Meter:
Source Sensor: sensor.heat_loss_rate
Cycle: Weekly
Name: Weekly Heat Loss
Monthly Utility Meter:
Source Sensor: sensor.heat_loss_rate
Cycle: Monthly
Name: Monthly Heat Loss
Yearly Utility Meter:
Source Sensor: sensor.heat_loss_rate
Cycle: Yearly
Name: Yearly Heat Loss
Save the Utility Meters.

Step 3: Create the Annual Heat Loss Sensor
Go to Settings > Devices & Services > Entities.

Click on + Add Entity > Template Sensor.

Fill in the Template Sensor:

Name: Annual Heat Loss (kWh/m²)
State:
jinja
Code kopiëren
{% set avg_temp_diff = 15 %}
{% set area = 100 %}  # Replace with your home's floor area in m²
{% set total_energy_loss = (states('sensor.heat_loss_rate') | float * avg_temp_diff * 2000) / 1000 %}
{{ total_energy_loss / area }}
Unit of Measurement: kWh/m²/year
Save the Template Sensor.

Visualizing the Data in the Dashboard
Add Sensors to Lovelace:
After creating these template sensors and utility meters, go to your Lovelace dashboard.
Add an Entities Card to display your new heat loss data (Daily Heat Loss, Weekly Heat Loss, etc.).
Optionally, use a Gauge Card to visualize the current heat loss rate in W/°C or the annual heat loss in kWh/m²/year.
Summary
You can create everything via the GUI in Settings > Helpers and Entities.
The Template Sensors and Utility Meters help you track heat loss over time.
These sensors can be added to the dashboard for real-time monitoring.
