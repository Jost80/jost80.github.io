---
title: 433Mhz Sensors in Home Assistant
date: 2023-05-21 14:30:00 +/-TTTT
categories: [Home Assistant]
tags: [rtl-433]     # TAG names should always be lowercase
---
I have been using Telldus at home for automation for a few years. The free tier was enough for my needs but lately Telldus announced that they would remove features from it and I would need to pay for premium to keep everything working as before. Since the Telldus hardware I have is the older 433Mhz version with its limitations and that Telldus haven't really developed the service for a long time as far as I can see I felt it was time to move to something else.

Since I like to tinker i went with [Home Assistant](https://www.home-assistant.io/). 

I did want to keep some of the sensors from Telldus that I use to keep track of temperatures. My solution was to get a [NESDR Mini 2 USB RTL-SDR](https://www.amazon.se/dp/B00P2UOU72?psc=1&ref=ppx_yo2ov_dt_b_product_details) and use that with the [rtl-433](https://github.com/merbanan/rtl_433) software. Since I use a Debian based distribution I could install it with ```sudo apt install rtl-433```.

When running ```sudo /usr/bin/rtl_433``` from the command line I could monitor any signals sent on 433Mhz and that way find the id numbers for my sensors. 

Example output can look like this

```
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _
time      : 2023-05-21 13:46:02
model     : Fineoffset-TelldusProove               ID        : 135
Temperature: 24.6 C      Humidity  : 26 %          Integrity : CRC
_ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ 
```

After documenting all ID numbers and figuring out what physical sensor that matched each ID it was time to go ahead and send this to Home Assistant. The solution I found was to use Mosquitto as a middle man.

To setup Mosquitto using docker compose you can use the following (network settings in the example are a bit lazy so you probably want to modify that).

Create a folder called mosquitto.
In that folder create a file called docker-compose.yml with the following content.
```yaml
  mosquitto:
    container_name: mosquitto
    image: eclipse-mosquitto
    volumes:
      - /docker/configs/mosquitto:/mosquitto
      - /docker/configs/mosquitto/data:/mosquitto/data
      - /docker/configs/mosquitto/log:/mosquitto/log
    restart: unless-stopped
    privileged: false
    network_mode: host
```
Continue by creating the file mosquitto.conf in /docker/configs/mosquitto/config with the following content

```
persistence true
persistence_location /mosquitto/data/
log_dest file /mosquitto/log/mosquitto.log
listener 1883

## Authentication ##
allow_anonymous true
```

Start the container by running ```docker-compose up -d``` in the folder with docker-compose.yml. The container should now be running (you can confirm that with ```sudo docker ps```).

Next we need to enter the container and set up a password.
```bash
# Enter the container
sudo docker exec -it mosquitto sh
# Set password for user hass
mosquitto_passwd -c /mosquitto/config/password.txt hass
```

Then change mosquitto.conf to include the following
```
allow_anonymous false
password_file /mosquitto/config/password.txt
```

Next is to configure Home Assistant.

Add this to configuration.yaml
```
mqtt: !include mqtt.yaml
```

And create the file mqtt.yaml with the config for your sensors. Here is an example for the same sensor as the example output earlier (ID=135). This sensor reports temperature in celcius and the humidity.

```
sensor:
    - name: "Kitchen (temperature)"
      state_topic: "rtl_433/135/temperature_C"
      qos: 0
      unit_of_measurement: "°C"
    - name: "Kitchen (humidity)"
      state_topic: "rtl_433/135/humidity"
      qos: 0
```

In Home Assistant you then add the MQTT integration and configure it with the IP, port, username and password required to connect to mosquitto.

Then to test if its working
```bash
sudo /usr/bin/rtl_433 -F "mqtt://127.0.0.1:1883,user=hass,pass=supersecret,devices=rtl_433[/id]" -T 5m
```
rtl_433 should run for 5 minutes and send any data it captures to mosquitto. 

If all is ok sensor data should show up in Home Assistant and you can schedule rtl_433 with crontab. I read that it could be a good idea to schedule it for a 5 minute capture every 10 minutes to give the device time to cool down so these examples does just that. With temperature sensors this works fine but if you want to read other sensors like a door or remote control that would probably not be any good. 

In my case I had to add ```-R 18``` to only listen to one specific device type. One of my neighbors have a device with the same ID as me but sending temperature in fahrenheit and the values (and graph) I got was interesting to say the least.
```
*/10 * * * * /usr/bin/rtl_433  -R 18 -F "mqtt://127.0.0.1:1883,user=hass,pass=supersecret,devices=rtl_433[/id]" -T 5m
```


References used:

[https://cohost.org/Elixir-Of-Progress/post/463783-probing-weather-in-h](https://cohost.org/Elixir-Of-Progress/post/463783-probing-weather-in-h)

[https://www.homeautomationguy.io/blog/docker-tips/configuring-the-mosquitto-mqtt-docker-container-for-use-with-home-assistant](https://www.homeautomationguy.io/blog/docker-tips/configuring-the-mosquitto-mqtt-docker-container-for-use-with-home-assistant)