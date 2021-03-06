## UNRAID DASHBOARD

Monitor and control your Unraid instance(s). This allows to run Mover, Parity Check, Power Unraid On/Off, turn Array On/Off, monitor various statistics, control and monitor Docker containers. It's possible to build automations and alarms based on all these sensors or like actionable notifications of containers with higher than normal CPU/MEM usage etc.


![Unraid Overview](/dashboards/unraid/img/unraid-dashboard-demo.gif)

## HOME ASSISTANT ADD-ONS

* MQTT Broker

&nbsp;

&nbsp;

## INTEGRATIONS

List of integrations needed to set this up:

* [Unraid API](https://github.com/ElectricBrainUK/UnraidAPI) - This will allow you to control the 

* [Glances](https://github.com/nicolargo/glances) - This is my go to integration for monitoring my servers statistics (depending on your system and OS you can get different access to different sensors, CPU usage, RAM usages, disck usasge, container stats, temps, Fan speeds etc.)

* [Monitor Docker](https://github.com/ualex73/monitor_docker) & [Docker-Socket-Proxy](https://github.com/Tecnativa/docker-socket-proxy) - Control your docker instances/containers, pay attention to the security! I am using this as my integration to my other docker instances also, but you could instead modify the dashboard to use Unraid-API only.

* [browser_mod](https://github.com/thomasloven/hass-browser_mod) - In this dashboard I used browser_mod for custom popups, to provide more details on the Docker containers.

&nbsp;

&nbsp;

### UNRAID

On Unraid side, you must have Docker enabled to setup the containers below.

**1. Unraid-API**

The setup process is really well described [here](https://github.com/ElectricBrainUK/UnraidAPI) by ElectricBrainUK

&nbsp;

**2. Glances**

(EDIT 25.4.2022: Glances v3.2.5 is not working HA 2022.4, using nicolargo/glances:3.2.4.2)
Setup Glances docker container in Unraid, this is quite straight forward.

&ensp;&ensp; APPS -> Search for Glances -> Install

Take note of the ip and port you Glances is runnign, you need these in Home Assistant when setting up the integration. You can also open the WebUI to check all the info Glances is giving (Note! all might not be available through the integration).

&nbsp;

**3. Monitor Docker & Docker-Socket-Proxy**

In order the Monitor Docker integration to work, you need run to Docker-Socket-Proxy on Unraid side. Check the QA part of the [Monitor Docker readme](https://github.com/ualex73/monitor_docker), also check the [Tecnativa Docker-Socket-Proxy](https://github.com/Tecnativa/docker-socket-proxy) security recommendations.

&nbsp;

&nbsp;

### HOME ASSISTANT

**1. Unraid-API**

The setup process is really well described [here](https://github.com/ElectricBrainUK/UnraidAPI) by ElectricBrainUK

&nbsp;

**2. Glances**

On Home Assistant side connection to to your Glances docker is easy, through default integration.

&ensp;&ensp; *Configurations -> Devices & Services -> Add Integration*

Search for Glances and fill in the setup form. You want to use unique name (e.g. Unraid-1) so that it's easier to indentify the entities when you start having multiple Glances integrations.

&nbsp;

**3. Docker Monitor**

Install Monitor Docker through HACS [Monitor Docker readme](https://github.com/ualex73/monitor_docker). To use the monitor_docker you have to add the config in to your configuration.yaml, below is my config.

```YAML
## MONITOR DOCKER ##
monitor_docker:
  - name: UnraidDocker
    url: tcp://Your-Unraid-IP:Docker-Socket-Proxy-Port
    scan_interval: 30
    monitored_conditions:
      - version
      - containers_running
      - containers_paused
      - containers_stopped
      - containers_cpu_percentage
      - containers_memory_percentage
      - cpu_percentage
      - health
      - uptime
      - memory
      - memory_percentage
      - network_speed_up
      - network_speed_down
      - allinone
```

&nbsp;

&nbsp;

## VIEW & CARDS

For this view I used I used *View Type Horizontal (layout-card)* and *vertical-stacks* to define the columns of cards. You can easily arrange yours as needed.

### FRONTEND ELEMENTS & CUSTOM CARDS

List of cards used:

* vertical-stack
* horizontal-stack
* picture-elements
* custom:layout-card
* custom:button-card
* custom:auto-entities
* custom:bar-card
* custom:apexcharts-card
* history-graph
* custom:logbook-card

&nbsp;

&nbsp;

### CARDS

| CARD | Containers Running |
|:-:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <img src="/dashboards/unraid/img/containers-running.png">  | *EDIT 29.3.2022: Added a popup-button layer*</br>Containers Running uses custom:auto-entities to automatically list all containers with state "on". Custom:layout-card fills the card in 3 columns, with custom:button-cards presenting each individual container.</br><br>Containers can be turned toggled (on/off) from the Green Indicator Circle (confirmation popup). By clicking the button-card, custom popup, using browser-mod integration is shown, with container specific details.</br><br>Icons are present in www/icons folder and the filename.png should match the container name. </br></br> [containers-running.yaml](/dashboards/unraid/containers-running.yaml) </br> |
| <img src="/dashboards/unraid/img/container-indicators.gif"> | Containers can be turned toggled (on/off) from the Green Indicator Circle (with confirmation popup). High CPU and bandwith cues for each container. |
| <img src="/dashboards/unraid/img/containers-running-popup.png"> | Container - Popup</br><br>Defined within the containers-running.yaml. Uses custom:button-card for the first "Power" element. 2nd element is history-graph, 4th and 5th element uses custom:apexcharts-card. Final element is custom:logbook-card. </br> <br> </br> |

| CARD | Picture Element |
|:-:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <img src="/dashboards/unraid/img/picture-elements.png">  | Picture of my Unraid Enclosure, Shows Unraid Version Number, provided by Unraid-Api, and some hardware info also by Unraid-Api. Uses custom:button-card for the extra info positioning. </br> </br> [picture-elements.yaml](/dashboards/unraid/picture-elements.yaml) |

| CARD | Button Row |
|:-:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <img src="/dashboards/unraid/img/button-row-0.png"></br><img src="/dashboards/unraid/img/button-row-1.gif"></br><img src="/dashboards/unraid/img/button-row-2.gif"></br><img src="/dashboards/unraid/img/button-row-3.gif">  | *EDIT 29.3.2022: Some clean-up and update done*<br>Button row to control the Unraid instance, Array, Mover, Parity. BUT Messy YAML, needs clean-up and some parts need total redo (Unnecessary lines, not so perfect positioning (web vs mobile). </br> </br> [button-row.yaml](/dashboards/unraid/button-row.yaml) |
