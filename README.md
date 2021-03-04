# IQRF Gateway

Docker compose script for the IQRF Gateway running on RPI board.

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
docker-compose down
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
