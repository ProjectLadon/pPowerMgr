# 1.0 General Requirements {#SRDO001 }

## 1.1 SRDO002 {#SRDO002 }

This shall be implemented as a MOOS-IvP AppCasting app.

## 1.2 SRDO003 {#SRDO003 }

All failures **shall** result in an error message that accurately describes the error and makes the source of the failure clear.

## 1.3 SRDO004 {#SRDO004 }

This software **shall** pass valgrind memcheck with no memory leaks or corrupted memory.

# 2.0 Analog Variables {#SRDO005 }

## 2.1 SRDO006 {#SRDO006 }

Each analog variable **shall** specify a MOOS variable of type ```DOUBLE``` as the input or output variable and subscribe to or publish that variable, as appropriate..

## 2.2 SRDO007 {#SRDO007 }

Each analog variable **shall** specify appropriate engineering units.

## 2.3 SRDO008 {#SRDO008 }

The given units **shall** be used to scale the subscribed or published value to a common internal representation.

## 2.4 SRDO009 {#SRDO009 }

Each analog variable **shall** have an optional ```offset``` value which is added to the incoming value before further processing.

## 2.5 SRDO010 {#SRDO010 }

Each analog variable **shall** have an optional ```gain``` value which is multiplied by the incoming value after the application of the offset (if any) and before unit scaling.

## 2.6 Analog Variable Types {#SRDO011 }

### 2.6.1 Voltage {#SRDO012 }

#### 2.6.1.1 SRDO013 {#SRDO013 }

Voltage variables **shall** name a MOOS community variable of type ```DOUBLE``` to subscribe to.

#### 2.6.1.2 SRDO014 {#SRDO014 }

The offset, if present, **shall** be added to the incoming variable.

#### 2.6.1.3 SRDO015 {#SRDO015 }

The gain, if present, **shall** be multiplied by the incoming variable after the addition of the offset.

#### 2.6.1.4 SRDO016 {#SRDO016 }

The resulting value **shall** be converted to an internal representation based on the supplied units.

### 2.6.2 Current {#SRDO017 }

#### 2.6.2.1 SRDO018 {#SRDO018 }

Current variables **shall** name a MOOS community variable of type ```DOUBLE``` to subscribe to.

#### 2.6.2.2 SRDO019 {#SRDO019 }

The offset, if present, **shall** be added to the incoming variable.

#### 2.6.2.3 SRDO020 {#SRDO020 }

The gain, if present, **shall** be multiplied by the incoming variable after the addition of the offset.

#### 2.6.2.4 SRDO021 {#SRDO021 }

The resulting value **shall** be converted to an internal representation based on the supplied units.

### 2.6.3 Energy Output {#SRDO022 }

#### 2.6.3.1 SRDO023 {#SRDO023 }

Offset and gain, if present, **shall** be applied to the internal variable prior to publication.

#### 2.6.3.2 SRDO024 {#SRDO024 }

Energy ouput variables **shall** name a MOOS community variable of type ```DOUBLE``` and publish to that variable.

#### 2.6.3.3 SRDO025 {#SRDO025 }

The software **shall** maintain a list of all energy output variables for the use of other components of this software.

### 2.6.4 Energy Input {#SRDO026 }

#### 2.6.4.1 SRDO027 {#SRDO027 }

An energy input **may** only connect to an internal energy output.

#### 2.6.4.2 SRDO028 {#SRDO028 }

Energy inputs **shall** ignore offset and gain.

#### 2.6.4.3 SRDO029 {#SRDO029 }

Unit conversions **shall** be performed.

# 3.0 Battery Configuration {#SRDO030 }

## 3.1 SRDO031 {#SRDO031 }

Each battery managed by this power manager **shall** be configured by a single ```Battery``` entry.

## 3.2 SRDO032 {#SRDO032 }

Each battery object **shall** subscribe to a variable that reads the battery voltage.

## 3.3 SRDO033 {#SRDO033 }

Each battery object **may** subscribe to a variable that reads the battery charge current.

## 3.4 SRDO034 {#SRDO034 }

Each battery object **may** subscribe to a variable that reads the battery load current.

## 3.5 SRDO035 {#SRDO035 }

The nominal capacity of the battery **may** be defined in the configuration. This information is required in order to calculate the percentage charge state. If it is not present, the battery object **shall** throw an error if the percentage charge is requested.

## 3.6 SRDO036 {#SRDO036 }

The initial charge state of the battery **may** be defined in the configuration. Failure to define it **may** result in negative energy values being published.

## 3.7 SRDO037 {#SRDO037 }

If battery voltage, charge, and load currents are available, the battery object **shall** track the energy content of the battery and publish it to an energy output variable, if defined.

## 3.8 SRDO038 {#SRDO038 }

If the battery object has the information required to calculate total energy, it **may** define an energy output variable. If the output is defined but the required input information is not, the battery object **shall** print an error and force this module to exit.

# 4.0 Load Configuration {#SRDO039 }

## 4.1 SRDO040 {#SRDO040 }

Each load managed by this power manager **shall** be configured by a single ```Load``` entry.

## 4.2 SRDO041 {#SRDO041 }

Each load **shall** define one or more sources, which **must** be the names of previously configured battery objects.

## 4.3 SRDO042 {#SRDO042 }

Each load **may** track the voltage and current consumed by the load.

## 4.4 SRDO043 {#SRDO043 }

Each load **may** define a ```Switch``` element which is the name of a BINARY variable published when the shedding conditions are met.

## 4.5 SRDO044 {#SRDO044 }

Each load **may** define ```SwitchSense```, if it also defines ```Switch```. If present, this **shall** define the value published to ```Switch``` when the shedding conditions are met.

## 4.6 SRDO045 {#SRDO045 }

Each load that defines a switch variable **must** define at least one ```SheddingCondition```.

## 4.7 Shedding Conditions {#SRDO046 }

### 4.7.1 SRDO047 {#SRDO047 }

The ```SheddingCondition``` element **must** consist of one or more arrays of conditions.

### 4.7.2 SRDO048 {#SRDO048 }

Each condition **shall** produce a boolean value.

### 4.7.3 SRDO049 {#SRDO049 }

The results from the conditions in each member array **shall** be AND'd together.

### 4.7.4 SRDO050 {#SRDO050 }

The results from each member array **shall** be OR'd together to produce the final result.

### 4.7.5 SRDO051 {#SRDO051 }

Each condition **shall** name a source variable of any of the analog input types (voltage, current, or energy).

### 4.7.6 SRDO052 {#SRDO052 }

Each condition **shall** have a threshold value, which is a floating point number in the same units as the variable.

### 4.7.7 SRDO053 {#SRDO053 }

The condition **may** have a null element of either ```GreaterThan``` or ```LessThan```. If present, the software **shall** judge the condition as true if the variable is greater than or less than the threshold, respectively.

### 4.7.8 SRDO054 {#SRDO054 }

If

# 5.0 Startup Behavior {#SRDO055 }

## 5.1 SRDO056 {#SRDO056 }

This module **shall** read in the contents of the .moos file key conf as a JSON object, if present.

## 5.2 SRDO057 {#SRDO057 }

This module **shall** read in the contents of the file specified by the .moos file key ```confFile``` as JSON, if present.

## 5.3 SRDO058 {#SRDO058 }

If both ```conf``` and ```confFile``` are present, this module **shall** exit with an error.

## 5.4 SRDO059 {#SRDO059 }

The configuration JSON **must** conform to the schema defined in schema/powermgr_conf_schema.json.

## 5.5 SRDO060 {#SRDO060 }

If the configuration JSON is invalid, this module **shall** exit with an error.

## 5.6 SRDO061 {#SRDO061 }

The software **shall** build maps of ```Battery``` and ```Load``` objects from the the configuration JSON.

## 5.7 SRDO062 {#SRDO062 }

The software **shall** initialize all battery objects to their starting energy level (0 if not otherwise specified).

## 5.8 SRDO063 {#SRDO063 }

The software **shall** build a list of all analog input variables to subscribe to.

# 6.0 Connection Behavior {#SRDO064 }

## 6.1 SRDO065 {#SRDO065 }

On connection, the shoftware **shall** register for all subscribed variables.

# 7.0 New Mail Behavior {#SRDO066 }

## 7.1 SRDO067 {#SRDO067 }

On receipt of new mail, all analog variables that receive new data **shall** be updated.

## 7.2 SRDO068 {#SRDO068 }

Upon receipt of new mail, any battery object with updated input data **shall** update its internal estimate of energy remaining.

## 7.3 SRDO069 {#SRDO069 }

The incoming data for each battery **shall** be integrated via the trapezoidal rule to estimate the energy remaining. The trapezoidal rule is used because it is simple to implement and test, and is robust in the presence of varying time steps.

# 8.0 Iteration Behavior {#SRDO070 }

## 8.1 SRDO071 {#SRDO071 }

On each iteration, this software **shall** calculate the shedding condition for each load object.

## 8.2 SRDO072 {#SRDO072 }

The software **shall** publish the current energy levels for all battery objects that have them defined.

## 8.3 SRDO073 {#SRDO073 }

The software **shall** publish the results for eache shedding condition calculation.

