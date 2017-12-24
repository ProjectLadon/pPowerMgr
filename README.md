# pPowerMgr
Electrical load manager for a MOOS community. This module tracks available energy in several batteries and sheds load to reduce battery demand if the situation becomes critical.

## Dependencies
* MOOS-IvP
* https://github.com/Tencent/rapidjson/ -- provides JSON parse/deparse

## Configuration
This module is configured with a JSON object conforming to ```schema/powermgr_conf_schema.json```. It can either be embedded in the configuration block as the value of ```conf``` or in a separate file specified with ```confFile```.

This JSON contains two arrays -- a ```Batteries``` array and a ```Loads``` array. The first contains an entry for each battery/charger combination and the latter an entry for each distinct load placed on those batteries.

### Battery Objects
Each ```Battery``` object has the following properties:

* **Name** -- the name of the battery, used elsewhere to specify which one we're talking about. This entry is mandatory.
* **BatteryVoltage** -- contains entries for the name of the source variable of the battery voltage, gain and offset, and the units that the output (after application of offset and gain) should be interpreted as. Legal unit values are ```V``` and ```mV```. This entry is mandatory.
* **ChargeCurrent** -- contains entries for the name of the source variable of the charging current, gain and offset, and the units that the output (after application of offset and gain) should be interpreted as. Legal unit values are ```A``` and ```mA```. This value, multiplied by the battery voltage, is the energy flowing into the battery. This entry is optional, but no battery energy calculations can be done without it.
* **LoadCurrent** -- contains entries for the name of the source variable of the load current, gain and offset, and the units that the output (after application of offset and gain) should be interpreted as. Legal unit values are ```A``` and ```mA```. This value, multiplied by the battery voltage, is the energy flowing out of the battery.  This entry is optional, but no battery energy calculations can be done without it.
* **NotionalCapacity** -- contains the notional full capacity of the battery, in energy units. Legal unit values are ```W-hr``` and ```kJ```. This is optional, but is required for calculations involving battery capacity as a percentage of full capacity.
* **InitialEnergy** -- contains the initial energy content of the battery, in energy units. Legal unit values are ```W-hr```, ```kJ```, and ```%```. This is optional, but if it is missing and the battery starts at or near full charge, legitimate energy calculations may produce negative numbers.
* **TotalEnergy** -- contains entries for the name of the total battery energy output variable, gain and offset, and the units of the output. Legal unit values are ```W-hr```, ```kJ```, and ```%```. This entry is optional.

### Load Objects
Each ```Load``` object has the following properties:

* **Name** -- contains the name of this load. This entry is mandatory.
* **Sources** -- contains a single string or array of strings naming the battery or batteries that this load draws from.
* **LoadVoltage** --
* **LoadCurrent** --
* **SheddingCondition** -- contains one or an array of conditions under which this load will be switched off (shed). A variable must be defined for the ```Switch``` element for this to have any effect. This is an array of arrays of JSON shedding condition objects. For each sub-array, the conditions are ANDed together, and then the results from each sub-array are OR'd together to produce the ultimate shedding decision.  
  * **Condition** -- contains entries for the source variable, gain and offset, and the units in which the resulting value is measured. Legal values for units are ```V```, ```mV```, ```A```, ```mA```, ```W-hr```, ```kJ```, and ```%```.
  * **Threshold** -- contains the numerical threshold for the shedding decision.
  * **GreaterThan** -- If this key is present (with a null value), the shedding condition is true if the incoming variable value is high than the numerical threshold.
  * **LessThan** --If this key is present (with a null value), the shedding condition is true if the incoming variable value is less than the numerical threshold.
* **Switch** -- contains the name of the BINARY variable to publish with the shedding decision. It is optional, but without it, there is no way for the load to be shed.  
* **SwitchSense** -- is an optional boolean setting the value of the switch variable when the load is to be set. It defaults to true, i.e. the value published to ```Switch``` is true when the load is turned off. 
