#Beagle Sense API Reference
*DRAFT Version v0.1*

# Versions and URLs

The BeagleSense base URL structure for API requests will always look like:

	https://api.beaglesense.com/{version}/{API_request}?{API_params}
	
# Responses

All responses to API requests will be formatted as JSON.

Successful API requests will have an **HTTP 200** response code and JSON data corresponding to the API request.

Unsuccessful API requests will have an appropriate **HTTP ERROR** (e.g. 400) response code and a JSON object with an error message and code. Eg.

	{
		"error_code" : “400”,
		"error_message" : “Invalid API_KEY” 
	}

# Request Throttling

In order to ensure consistent performance of the BeagleSense API service, API requests will be throttled if the service determines that your account is making too many calls. The actual rate limit is not officially set but 1 request per second is a good guideline to adhere to.

# Device API Reference

The Device API is used for communicating with and getting information from the Beagle Devices set up in your account.

## getAllDevices

**Since: v1**

Return a list of all Beagle Devices in your account.

	https://api.beaglesense.com/v1/device/getAllDevices?api_key=API_KEY

|Parameter|Type|Value|
|---------|----|-----|
|API_KEY|String|\<required>Your API Key|

Response Example:

	{ "devices":[
		{
			"device_id":”65bc12a”,
			"device_type”:1,
			"device_name”:”Home Base Station”,
			"device_location”:”Home”
		},
		{
			"device_id":”65bc12b”,
			"device_type”:2,
			"device_name”:”Temperature”,
			"device_location”:”Living Room”
		},
		{
			"device_id":”65bc12c”,
			"device_type”:3,
			"device_name”:”Air Quality”,
			"device_location”:”Living Room”
		},
		{
			"device_id":”65bc12d”,
			"device_type”:2,
			"device_name”:”Temperature”,
			"device_location”:”Bedroom”
		}
	]}

## getDeviceInfo

**Since: v1**

Return a detailed description and status for a specified device.

	https://api.beaglesense.com/v1/device/getDeviceInfo?api_key=API_KEY&device_id=DEVICE_ID

|Parameter|Type|Value|
|---|---|---|
|API_KEY|String|\<required> Your API key|
| DEVICE_ID | String |\<required> The unique device ID for the info request|

Response Example:

	{ 
		"device_id":”65bc12b”,
		"device_type”:2,
		"device_name”:”Temperature”,
		"device_location”:”Living Room”,
		"serial_number”=”7765zc65”,
		"firmware_version”=”1.2.101”,
		"battery_power”=75,
		"sample_rate”=12,
		"transfer_rate”=4
	}

## setDeviceConfig

**Since: v1**

Make a configuration change to a device. The particular settings or values that can be changed on a device may be dependent on the type of device.

	https://api.beaglesense.com/v1/device/setDeviceConfig?api_key=API_KEY&device_id=DEVICE_ID&CONFIG_ID=CONFIG_VALUE&CONFIG_ID=CONFIG_VALUE&...

|Parameter|Type|Value|
|---|---|---|
|API_KEY|String|\<required> Your API key|
| DEVICE_ID | String |\<required> The unique device ID for the request|
| CONFIG_ID ||\<required> Configuration variables to set. See the table below for possible values|

| CONFIG_ID | Type | CONFIG_VALUE |
|---|---|---|
| device_name | String |A user defined name for the device|
| device_location | String |A user defined location for the device|
| power_mode | Enum |Switches a number of variables in each device which trade off power consumption/battery life with data collection and communication rates. Accepted values are: ECONOMY, NORMAL, HIGH|
| sample_rate | Integer |The number of detection cycles per hour to collect data. Higher frequencies will impact battery life.|
| transfer_rate | Integer |The number of times the sensor should communicate collected data. Sensors will send to the base station. Base stations will upload to the Beagle Cloud.|

# Data API Reference

The Data API is used to make requests against the data collected by your Beagle Sensors.

## getBeagleScore

**Since: v1**

Get BeagleScore data

	https://api.beaglesense.com/v1/data/getBeagleScore?api_key=API_KEY&score_type=SCORE_TYPE&start_date=START_DATE&end_date=END_DATE&locations=LOCATIONS

|Parameter|Type|Value|
|---|---|---|
|API_KEY|String|\<required> Your API key|
| SCORE_TYPE | Enum |\<required> One of SERENITY, PRODUCTIVITY|
| START_DATE | String |\<required> "yyyy-mm-dd" The start date for the report period|
| END_DATE | String |\<required> “yyyy-mm-dd” The end date for the report period.|
| LOCATIONS | String |\<optional> A comma separated list of location names. Not providing any location will cause the report to return a score for each location defined in your Beagle network.|

Response Example:

	{ "beagle_scores":[
		{
			"device_location":”Living Room”,
			"start_date”=”2015-10-01”,
			"end_date”=”2015-10-31”,
			"day”:[
				{"date":”2015-10-01”,”serenity”:”76”},
				{"date":”2015-10-01”,”serenity”:”82”},
				…
			]
		}
	]}

## getRawData

**Since: v1**

Get a report of raw data collected from some or all sensors over a period of time.

The standard data request will look like:

	https://api.beaglesense.com/v1/data/getRawData?api_key=API_KEY&start_date=START_DATE&end_date=END_DATE&base_station_ids=BASE_STATION_IDS&sensor_ids=SENSOR_IDS

|Parameter|Type|Value|
|---|---|---|
|API_KEY|String|\<required> Your API key|
| SCORE_TYPE | Enum |\<required> One of SERENITY, PRODUCTIVITY|
| START_DATE | String |\<required> "yyyy-mm-dd" The start date for the report period|
| END_DATE | String |\<required> “yyyy-mm-dd” The end date for the report period.|
| LOCATIONS | String |\<optional> A comma separated list of location names. Not providing any location will cause the report to return a score for each location defined in your Beagle network.|
| SENSOR_IDS | String |\<optional> A comma separated list of sensor device IDs to filter results by. |

Response Example:

	{ “raw_data”:[
		{
			“device_location”:”Living Room”,
			”start_date”=”2015-10-01”,
			”end_date”=”2015-10-31”,
			”day”:[
				{“date”:”2015-10-01”,”device_id”:”65bc12b”,
					”value”:”23”,”units”:”degrees C”},
				{“date”:”2015-10-01”,”device_id”:”65bc12c”,
					”value”:”425”,”units”:”CO2 ppm”},
				{“date”:”2015-10-02”,”device_id”:”65bc12b”,
					”value”:”22”,”units”:”degrees C”},
				{“date”:”2015-10-02”,”device_id”:”65bc12c”,
					”value”:”397”,”units”:”CO2 ppm”},
				…
			]
		},
		{
			“device_location”:”Bedroom”,
			”start_date”=”2015-10-01”,
			”end_date”=”2015-10-31”,
			”day”:[
				{“date”:”2015-10-01”,”device_id”:”65bc12d”,
					”value”:”21”,”units”:”degrees C”},
				{“date”:”2015-10-02”,”device_id”:”65bc12d”,
					”value”:”21”,”units”:”degrees C”},
				…
			]
		}
	]}
