# Waveshare UPS HAT Monitoring Tool (CLI edition)
CLI-friendly version of `INA219.py` script for [Waveshare UPS HAT](https://www.waveshare.com/ups-hat.htm) for Raspberry Pi. It also includes Zabbix agent userparam definition and the template if you want to monitor it remotely with Zabbix.

This script is forked from Waveshare's original `INA219.py` script published at their [WIKI](https://www.waveshare.com/wiki/UPS_HAT).

## Installation
* Grab the code:
```
git clone https://github.com/rpavlyuk/UPS_HAT-cli.git
cd UPS_HAT-cli
```
* Install packages which script requires to work (assuming that you already have Python installed):
```
pip install -r requirements.txt
```
* Copy the script to local programs folder:
```
sudo cp INA219-cli.py /usr/local/bin
```
* Set correct permissions:
```
sudo chmod a+x /usr/local/bin/INA219-cli.py
```
* Give it a try:
```
/usr/local/bin/INA219-cli.py --help
```

## Usage
* Script has a built in help:
```
[rpavlyuk@ganymede ~]$ /usr/local/bin/INA219-cli.py --help
usage: INA219-cli.py [-h] [-f FORMAT] [-p PARAMETER]

options:
  -h, --help            show this help message and exit
  -f, --format FORMAT   Output format: json, text, plain
  -p, --parameter PARAMETER
                        (Only for JSON or plain format) Output only specific parameter: psu_voltage, shunt_voltage, load_voltage, current, power, battery_soc
```
**NOTE**: By default script needs to be run via `sudo` in order to access I2C
### Some command examples:
* Text output:
```
[rpavlyuk@ganymede ~]$ sudo /usr/local/bin/INA219-cli.py
PSU Voltage:    8.288 V
Shunt Voltage: -0.000400 V
Load Voltage:   8.288 V
Current:       -0.004000 A
Power:          0.032 W
Percent:       95.3%
```
* JSON output:
```
[rpavlyuk@ganymede ~]$ sudo /usr/local/bin/INA219-cli.py -f json
{
  "psu_voltage": {
    "value": 8.29585,
    "unit": "V",
    "label": "PSU Voltage"
  },
  "shunt_voltage": {
    "value": -0.00015,
    "unit": "V",
    "label": "Shunt Voltage"
  },
  "load_voltage": {
    "value": 8.296,
    "unit": "V",
    "label": "Load Voltage"
  },
  "current": {
    "value": -0.0015,
    "unit": "A",
    "label": "Current"
  },
  "power": {
    "value": 0.016,
    "unit": "W",
    "label": "Power"
  },
  "battery_soc": {
    "value": 95.67,
    "unit": "%",
    "label": "Percent"
  }
}
```
* Requesting specific parameter in plain and JSON format:
```
[rpavlyuk@ganymede ~]$ sudo /usr/local/bin/INA219-cli.py -f json -p battery_soc
{
  "value": 95.67,
  "unit": "%",
  "label": "Percent"
}
[rpavlyuk@ganymede ~]$ sudo /usr/local/bin/INA219-cli.py -f plain -p battery_soc
95.83
```

## Zabbix Integration
The template and the installation was tested with Zabbix 7.2.x
### Zabbix Agent on Raspberry Pi host
* It is assumed that you already have [Zabbix Agent](https://www.zabbix.com/download_agents) installed on the box where UPS HAT is installed, and Agent is already connected to [Zabbix Server](https://www.zabbix.com/server_monitoring)
* Copy agent configuration file to your `.conf.d` folder (assuming `/etc/zabbix_agentd.conf.d/`):
```
sudo cp zabbix/ups_hat_waveshare.conf /etc/zabbix_agentd.conf.d/
```
* Give `zabbix` user access to run the script as root:
```
sudo cp zabbix/zabbix.sudoers /etc/sudoers.d/zabbix
```
**NOTE:** You may just append script to allowed list if you already have `/etc/sudoers.d/zabbix` sudoers file
* Restart the agent:
```
sudo systemctl restart zabbix-agent.service
```
### Zabbix Server
* Import template [Waveshare_UPS_HAT_for_RaspberryPi.yaml](https://github.com/rpavlyuk/UPS_HAT-cli/blob/main/zabbix/Waveshare_UPS_HAT_for_RaspberryPi.yaml) to your template repository
* Link the template to the host and use the collected data for any dashboards and charts

## License
The code is provided as-is, use it at your own risk.

