# iocage-homeassistant
Artifact file(s) for [Home-Assistant](https://www.home-assistant.io/) + [App-Daemon](https://www.home-assistant.io/docs/ecosystem/appdaemon/) / [HA-Dashboard](https://www.home-assistant.io/docs/ecosystem/hadashboard/) + [Hass-Configurator](https://www.home-assistant.io/docs/ecosystem/hass-configurator/#configuration-ui-for-home-assistant)  


A somewhat complete "quick start" using this repo can be found in the Home Assistant forums:  
[My (almost) complete quick start to installing Home Assistant on FreeNAS 11.2 ](https://community.home-assistant.io/t/my-almost-complete-quick-start-to-installing-home-assistant-on-freenas-11-2-including-appdaemon-ha-dashboard-hass-configurator-mosquitto-and-tasmoadmin/71882?u=troy)


If you are using a Z-Wave or Zigbee controller such as the Aeotec Gen-5, Nortek HUSBZB-1, or similiar USB device, you will now need to add a script that creates a custom devfs_ruleset on the FreeNAS host system. This is not difficult to do and a quick how-to can be found in the [FreeNAS forums](https://forums.freenas.org/index.php?threads/usb-z-wave-device-no-longer-shows-up-in-iocage-jail-on-freenas-11-2.71301/post-501866).

The final step requries using the FreeNAS GUI in order to have the custom ruleset automatically created during FreeNAS (re)boots.

---
---
## iocage-plugin-homeassistant

 - This script will by default create a plugin-jail for Home-Assistant on FreeNAS 11.2
 - However for Home Assistant I think using this script to install "standard-jail" is my recommended choice 

**Download plugin and install**

    wget -O /mnt/iocage/.plugin_index/homeassistant.json https://raw.githubusercontent.com/tprelog/iocage-homeassistant/master/homeassistant.json
    sudo iocage fetch -P dhcp=on vnet=on bpf=yes -n /mnt/iocage/.plugin_index/homeassistant.json --branch 'master'

---
---
## iocage-jail-homeassistant

 - This scrpit can also be used to create a standard-jail for Home-Assistant
 - Includes option to install App-Daemon/HA-Dashboard and(or) Hass-Configurator
 
**Download pkg-list and create a jail using it to install requirements**

    wget -O /tmp/pkglist.json https://raw.githubusercontent.com/tprelog/iocage-homeassistant/master/pkg-list.json
    sudo iocage create -r 11.2-RELEASE boot=on dhcp=on bpf=yes vnet=on -p /tmp/pkglist.json -n homeassistant


**Optional: mount a dataset inside the jail**

    sudo iocage fstab -a homeassistant "/mnt/tank/user/hass /home/hass nullfs rw 0 0"
    
    
**Git script and begin install**

    sudo iocage exec homeassistant git clone https://github.com/tprelog/iocage-homeassistant.git /root/.iocage-homeassistant
    sudo iocage exec homeassistant bash /root/.iocage-homeassistant/post_install.sh standard
    

**Answer questions will choose what gets installed**

    Install Home-Assistant?  [Y/n]:
    Install Hass-Configurator?  [Y/n]:
    App-Daemon & HA-Dashboard?  [Y/n]:
    Use the pre-configured examples?  [Y/n]:

***Profit!***

**Keeping things updated**

    sudo iocage exec homeassistant bash update
    Password:

    1) Home Assistant  3) Configurator    5) Status
    2) App Daemon      4) FreeBSD         6) Exit
    Enter Number to Upgrade: 

---

  - Home Assistant: `http://YOUR.HOMEASSISTANT.IP.ADDRESS:8123`  
  - HADashboard   : `http://YOUR.HOMEASSISTANT.IP.ADDRESS:5050`  
  - Configurator  : `http://YOUR.HOMEASSISTANT.IP.ADDRESS:3218`  
     
---

###### To see a list of jails as well as their ip address

    sudo iocage list -l
    +-----+-----------------+------+-------+----------+-----------------+---------------------+-----+----------+
    | JID |      NAME       | BOOT | STATE |   TYPE   |     RELEASE     |         IP4         | IP6 | TEMPLATE |
    +=====+=================+======+=======+==========+=================+=====================+=====+==========+
    | 1   | homeassistant   | on   | up    | jail     | 11.2-RELEASE-p4 | epair0b|192.0.1.75  | -   | -        |
    +-----+-----------------+------+-------+----------+-----------------+---------------------+-----+----------+
    | 2   | homeassistant_2 | on   | up    | pluginv2 | 11.2-RELEASE-p4 | epair0b|192.0.1.77  | -   | -        |
    +-----+-----------------+------+-------+----------+-----------------+---------------------+-----+----------+


Last tested on FreeNAS-11.2-RELEASE  
More information about [iocage plugins](https://doc.freenas.org/11.2/plugins.html) and [iocage jails](https://doc.freenas.org/11.2/jails.html) can be found in the [FreeNAS guide](https://doc.freenas.org/11.2/intro.html#introduction)  

