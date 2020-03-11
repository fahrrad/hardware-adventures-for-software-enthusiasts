# Simple MQTT Client Demo

# Prerequisites & Setup
1. Install [Docker](https://docs.docker.com/install/)
2. Download [SDK](https://github.com/espressif/ESP8266_NONOS_SDK/releases/tag/v2.2.1)
3. Install python 
4. Install SDK tooling via Docker image
```
docker pull vowstar/esp8266
```
5. Clone our MEETUP git repo into `ESP8266_NONOS_SDK` directory https://github.com/jenpaff/hardware-adventures-for-software-enthusiasts/tree/master/sven_in_c

# Build Your C Example

Run docker image and mount SDK directory
```
docker run -ti -e SDK_BASE=/build/ESP_NONOS_SDK -v /path-to-this-directory/ESP8266_NONOS_SDK-2.2.1:/build vowstar/esp8266 /bin/bash
```

## Step 0 (Optional): Familiarise yourself with the structure
/include            --> header files, e.g. mqtt_config.h
/modules           
/mqtt
/user
    /user_main.c    --> main function
gen_misc.sh         --> compile script

## Step 1: Configure Wifi and the MQTT Broker 

Go to `/include/mqtt_config.h` and configure the following parameters:
- MQTT_USER
- MQTT_PASS
- STA_SSID
- STA_PASS

## Step 2: Explore the main function

Go to `/include/mqtt_config.h` and change "feeds/door" in line 48 to your custom feed name.
All the MQTT messages will be sent to the MQTT broker. If you are sending your messages to our common broker, give 
your feed name a different name so you can distinguish your messages.

Alternatively, you can set up your own broker, e.g. https://www.cloudmqtt.com/

### Step 3: Build your demo

```
$./gen_misc.sh
```

Compile options: 2 0 3 3 2 

Choose boot version: 2 
Choose bin generate: 0
Choose spi speed: 3 
Choose spi mode: 3 
Choose spi size & map: 2 (512KB+ 512KB)

### Step 4: Check your port 

Check where in which port your ESP is connected

```
ls /dev/cu.* # on Mac
ls /dev/tty.* # on Windows
```

It should be e.g. `/dev/cu.usbserial-14140`

### Step 5: Build your demo

Exit Docker and start the flashing process by using the following commands

Go into your SDK directory 
```
cd ESP8266_NONOS_SDK
```

```
python ../esptool/esptool.py --port /dev/cu.usbserial-14140 --baud 115200 write_flash 0x00000 bin/eagle.flash.bin  0x10000 bin/eagle.irom0text.bin 0xFE000 bin/blank.bin 0xfc000 bin/esp_init_data_default_v05.bin```
```

### Run the demo and Result Shows

#### Debugging 

If you are debugging, check the main function and uncomment 130-136. When the flashing is done, you can check the serial output with the following command
```
picocom -b 115200 /dev/cu.usbserial-14140
```

The MQTT messages should send to the broker if successful (of course it will only send one status).

#### Prototype testing

Make sure you uncomment lines 138-143. When the flashing is done, plug your ESP into the prototype and check
the MQTT broker for messages. 


### Troubleshooting
- Check first via debugging if the Wifi connects correctly and MQTT messages are sending

# References: 
https://docs.espressif.com/projects/esp-idf/en/latest/api-guides/general-notes.html
https://www.espressif.com/sites/default/files/2a-esp8266-sdk_getting_started_guide_en_0.pdf
https://www.espressif.com/sites/default/files/documentation/esp8266_quick_start_guide_en.pdf



