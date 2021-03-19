# IQRF Gateway

Docker compose script for the IQRF Gateway running on RPi boards.

## Default configuration

 - coordinator is connected via SPI at `/dev/spidev0.0`
 - IQRF Gateway Daemon API WebSocket is available at `ws://gw-ip:1338/`
 - IQRF Gateway Daemon Monitor WebSocket is available at `ws://gw-ip:1438/`
 - IQRF Gateway Webapp is available at `http://gw-ip/` and `https://gw-ip/`
 - MQTT broker is available at `tcp://gw-ip:1883`

## Setup

### Single instance

To get started with all defaults, simply clone the repository and start the containers.

```bash
git clone https://gitlab.iqrf.org/open-source/iqrf-gateway-docker.git
docker-compose up -d
```

### Multiple instances

 1. Clone the repository to directories with unique names (e.g. use instance ID as a suffix).
 2. Change all exposed ports (e.g. `"80:80"`) to be unique for each instance (e.g. `"8080:80"`, `"8081:80"`) in `docker-compose.yml`.
 3. Change the mapped interface for coordinator on line 54 in `docker-compose.yml`.
 4. Change the coordinator interface in IQRF Gateway Daemon's configuration.
 5. Start the containers


#### Example script for 2 coordinator connected via USB CDC
```bash
for i in {0..1}; do
	git clone https://gitlab.iqrf.org/open-source/iqrf-gateway-docker.git "iqrf-gateway-${i}"
	cd "iqrf-gateway-${i}"
	sed -i "s/\"80:80\"/\"$((8080 + ${i})):80\"/g" docker-compose.yml
	sed -i "s/\"443:443\"/\"$((8443 + ${i})):443\"/g" docker-compose.yml
	sed -i "s/\"1338:1338\"/\"$((1338 + ${i})):1338\"/g" docker-compose.yml
	sed -i "s/\"1438:1438\"/\"$((1438 + ${i})):1438\"/g" docker-compose.yml
	sed -i "s/\"55000:55000\/udp\"/\"$((55000 + ${i})):55000\/udp\"/g" docker-compose.yml
	sed -i "s/\"55300:55300\/udp\"/\"$((55300 + ${i})):55300\/udp\"/g" docker-compose.yml
	sed -i "s/\"1883:1883\"/\"$((1883 + ${i})):1883\"/g" docker-compose.yml
	sed -i "s/\"\/dev\/spidev0\.0:\/dev\/spidev0\.0\"/\/dev\/ttyACM$((0 + ${i})):\/dev\/ttyACM$((0 + ${i}))\"\"/g" docker-compose.yml
	perl -i -p0e "s/\"libraryName\": \"IqrfCdc\",\n {12}\"enabled\": false/\"libraryName\": \"IqrfCdc\",\n            \"enabled\": true/g" daemon/config/config.json
	perl -i -p0e "s/\"libraryName\": \"IqrfSpi\",\n {12}\"enabled\": true/\"libraryName\": \"IqrfSpi\",\n            \"enabled\": false/g" daemon/config/config.json
	sed -i "s/\/dev\/ttyACM0/\/dev\/ttyACM$((0 + ${i}))/g" daemon/config/iqrf__IqrfCdc.json
        docker-compose up -d
	cd ..
done
```

## IQRF interfaces

### CDC or SPI or UART

- select true at either IqrfCdc or IqrfSpi or IqrfUart

```bash
nano daemon/config/config.json
```

- configure parameters for selected interface

```bash
nano daemon/config/iqrf__IqrfCdc.json
nano daemon/config/iqrf__IqrfSpi.json
nano daemon/config/iqrf__IqrfUart.json
```

- change mapping for your communication port of IQRF interface (line 57)

```bash
nano docker-compose.yml
```

## Start the gateway

```bash
docker-compose up -d
```

## Stop the gateway

```bash
docker-compose stop
```

## WebGUI of the gateway 

Point browser to http://gw-ip/ and explore.

## Basic examples

### Getting the tools for the testing

```bash
sudo apt-get install mosquitto-clients
```

```bash
wget https://github.com/vi/websocat/releases/download/v1.1.0/websocat_1.1.0_amd64.deb
sudo dpkg -i websocat_1.1.0_*.deb
sudo apt-get install -y jq
rm -f websocat_1.1.0_*.deb
```

### Getting the examples

```bash
git clone https://gitlab.iqrf.org/open-source/iqrf-gateway-daemon
cd iqrf-gateway-daemon/examples/bash
```

### Run the examples

```bash
./mqtt-generic-blink.sh
```

```bash
./websocket-generic-blink.sh
```
