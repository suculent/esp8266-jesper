# Jesper IoT server for the ESP8266 w/NodeMCU

Implements simple JSON-based HTTP server with Jesper protocol to control GPIO and fixtures connected to the ESP board.

### Prerequisites

- ESP8266 with FTDI/USB serial interface
- ESPlorer or any other tool capable of injecting LUA file to ESP8266

### Installation

1. Edit `config.lua` with your WiFi's SSID/password and upload to your ESP8266.
2. Upload `init.lua` to your ESP8266 and Reboot.
3. Test with curl (see commands below) or Platform or Plugin for Homebridge.
4. The console log will show WiFi connection progress and received commands/results.

### Test

The console log will give you aquired IP address for the homebridge-plugin-jesper or homebridge-platform-jesper configuration file (usually ~/homebridge/config.json).

Perform a HTTP POST request with valid JSON payload:

    curl <ESP8266_IP_ADDRESS> -d '{"write":{"port":1,"state":1}}'
    
Reply should look like:

    {"port":1, "value":1}    


## The Jesper protocol

Jesper ESP is a HTTP-based JSON protocol GPIO server. At the current moment,
supports only minimized JSONs (not pretty-printed).

Encodes values to GPIO PINs as gpio.LOW for 1 and gpio.HIGH for 0 (todo: fact-check)
Values read from ADC are in range 0 .. 1024 (todo: fact-check)
System reboots after any critical failure.


### Connect to WiFi [connect]

Requires existing connection. If you need to connect to specific WiFi network on ESP reset, this will save arguments inside a file named config.lua on your ESP for future use after reset.

The file should contain following globals:

    wifi_ssid = 'ENTER_YOUR_WIFI_SSID'
    wifi_password = 'ENTER_YOUR_WIFI_PASSWORD'
    
Usage:

**HTTP POST** `{"connect":{"ssid":"mywifi","password":"password"}}`


### Write to GPIO [write]

GPIO ports are numbered 0-n, ADC is read-only (write not supported).

**HTTP POST**

`{"write":{"port":4,"state":1}}`

**RESPONSE**

`{"success":true}`


### Read from GPIO/ADC [read]

__Read value from GPIO or ADC.__

GPIO ports are numbered 0-n, ADC uses reserved value of 255.

**HTTP POST**

`{"read":{"port":1}}`

**RESPONSE**

`{"port":1, "value":1}`


### Write to LED [led]

__Write RGB triplet to LED (GPIO PINs 7, 8, 9).__

**HTTP POST**

`{"led":{"red":1,"green":1,"blue":1}}`

**RESPONSE**

`{"success":true}`


### Read from LED [led-status]

Reads RGB triplet from LED (GPIO PINs 7, 8, 9).

**HTTP POST**

`{"led-status":"please"}`

**RESPONSE**

`{"led-status":{"red":1, "green":1, "blue":1}}`

