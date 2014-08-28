Raspberry Pi
============

* Rasspberry Pi Image with Mathematica, AccessPoint, DHCP-Server, Apache



Installation - Setup - Configuration
====================================

### SciPy - Scientific Computing with Python

```bash
$ sudo apt-get install python-smbus ipython bluetooth bluez-utils \
python-cwiid python-scipy python-numpy python-pygame \
python-setuptools libsdl-dev
$ sudo apt-get install python-matplotlib

``` 


### Apache Webserver

#### Create user & install apache2

```bash
$ sudo useradd -m "web"
$ sudo passwd "web"
$ sudo groupadd www
$ sudo adduser web www
$ sudo apt-get install apache2
```


#### Download and publish web-content

```bash
$ wget https://raw.githubusercontent.com/mgje/webprogramming/gh-pages/raspberry/WebProgramming.zip
$ unzip WebProgramming.zip
$ sudo mv WebProgramming /var/www
```

#### Restart apache server daemon

```bash
$ sudo /etc/init.d/apache2 restart
```

#### Optional web-packages

```bash
$ sudo apt-get install php5
$ sudo apt-get install mysql-server
$ sudo apt-get install php5-mysql
$ sudo apt-get install phpmyadmin
```

### Install dhcp-server

```bash
$ sudo apt-get install isc-dhcp-server
```

#### Next step configuration of the isc-dhcp-server

ignore the first error messages!

[FAIL] Starting ISC DHCP server...

open the isc-dhcp-server startscript and change the
last line to INTERFACES="wlan0"

```bash
$ sudo nano /etc/default/isc-dhcp-server
```

the edited start script should look as follows

```bash
# Defaults for isc-dhcp-server initscript
# sourced by /etc/init.d/isc-dhcp-server
# installed at /etc/default/isc-dhcp-server by the maintainer scripts

#
# This is a POSIX shell fragment
#

# Path to dhcpd's config file (default: /etc/dhcp/dhcpd.conf).
#DHCPD_CONF=/etc/dhcp/dhcpd.conf

# Path to dhcpd's PID file (default: /var/run/dhcpd.pid).
#DHCPD_PID=/var/run/dhcpd.pid

# Additional options to start dhcpd with.
#       Don't use options -cf or -pf here; use DHCPD_CONF/ DHCPD_PID instead
#OPTIONS=""

# On what interfaces should the DHCP server (dhcpd) serve DHCP requests?
#       Separate multiple interfaces with spaces, e.g. "eth0 eth1".
INTERFACES="wlan0"
```
change the configuration file dhcp.conf

```bash
$ sudo nano /etc/dhcp/dhcpd.conf
```

uncomment the two line with option

```bash
# option domain-name "example.org":
# option domain-name-servers ns1.example.org ns2.example.org
```

and append at the end of the file the following lines

```bash
subnet 192.168.42.0 netmask 255.255.255.0 {
range 192.168.42.10 192.168.42.50;
option broadcast-address 192.168.42.255;
option routers 192.168.42.1;
default-lease-time 600;
max-lease-time 7200;
option domain-name "local";
option domain-name-servers 192.168.42.1, 131.152.1.1;}
```

the following command let the dhcp service start automatic
after reboot

```bash
$ sudo update-rc.d isc-dhcp-server enable
```


### Install  AccessPoint

```bash
$ sudo apt-get install hostapd iw 
```


#### Configuration of the accesspoint (hostapd)
##### for  Edimax Technology Co., Ltd EW-7811Un 802.11n Wireless Adapter [Realtek RTL8188CUS]

create a configuration file

```bash
$ sudo nano /etc/hostapd/hostapd.conf
```

paste the following lines in hostapd.conf

```bash
# Schnittstelle und Treiber
interface=wlan0
driver=rtl871xdrv
# WLAN-Konfiguration
ssid=Raspi2
channel=9
# ESSID sichtbar
ignore_broadcast_ssid=0
# Laendereinstellungen
country_code=DE
#ieee80211d=1
# Uebertragungsmodus
hw_mode=g
# Optionale Einstellungen
# supported_rates=10 20 55 110 60 90 120 180 240 360 480 540
# Draft-N Modus aktivieren / optional nur für entsprechende Karten
#ieee80211n=1
# wmm-Funktionalitaet (fuer draft-n)
#wmm_enabled=1
# Uebertragungsmodus / Bandbreite 40MHz / siehe iw list
# ht_capab=[HT40+][SHORT-GI-40][DSSS_CCK-40]
# Beacons
#beacon_int=100
#dtim_period=2
# MAC-Authentifizierung
macaddr_acl=0
# max. Anzahl der Clients
#max_num_sta=20
# Groesse der Datenpakete/Begrenzung
#rts_threshold=2347
#fragm_threshold=2346
# hostapd Log Einstellungen
#logger_syslog=-1
#logger_syslog_level=2
#logger_stdout=-1
#logger_stdout_level=2
# temporaere Konfigurationsdateien
#dump_file=/tmp/hostapd.dump
#ctrl_interface=/var/run/hostapd
#ctrl_interface_group=0
# Authentifizierungsoptionen
auth_algs=1
# Verschluesselung / hier rein WPA2
wpa=2
#rsn_preauth=1
#rsn_preauth_interfaces=wlan0
wpa_key_mgmt=WPA-PSK
#rsn_pairwise=CCMP
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
# Schluesselintervalle / Standardkonfiguration
wpa_group_rekey=600
wpa_ptk_rekey=600
wpa_gmk_rekey=86400
# Zugangsschluessel (PSK) / hier in Klartext (ASCII)
wpa_passphrase=raspberrypi
```

Patch for the RTL8188CUS hardware driver

```bash
$ wget http://www.adafruit.com/downloads/adafruit_hostapd.zip
$ unzip adafruit_hostapd.zip
$ sudo mv /usr/sbin/hostapd /usr/sbin/hostapd.ORIG
$ sudo mv hostapd /usr/sbin
$ sudo chmod 755 /usr/sbin/hostapd
$ sudo update-rc.d hostapd enable
```
adapt the startskript /etc/init.d/hostapd

```bash
$ sudo nano /etc/init.d/hostapd
```
update line 19 DAEMON_CONF ...
update line 28 DAEMON_OPTS ...

the start script should look like the following codeblock

```bash
#!/bin/sh

### BEGIN INIT INFO
# Provides:             hostapd
# Required-Start:       $remote_fs
# Required-Stop:        $remote_fs
# Should-Start:         $network
# Should-Stop:
# Default-Start:        2 3 4 5
# Default-Stop:         0 1 6
# Short-Description:    Advanced IEEE 802.11 management daemon
# Description:          Userspace IEEE 802.11 AP and IEEE 802.1X/WPA/WPA2/EAP
#                       Authenticator
### END INIT INFO

PATH=/sbin:/bin:/usr/sbin:/usr/bin
DAEMON_SBIN=/usr/sbin/hostapd
DAEMON_DEFS=/etc/default/hostapd
DAEMON_CONF="/etc/hostapd/hostapd.conf"
NAME=hostapd
DESC="advanced IEEE 802.11 management"
PIDFILE=/var/run/hostapd.pid

[ -x "$DAEMON_SBIN" ] || exit 0
[ -s "$DAEMON_DEFS" ] && . /etc/default/hostapd
[ -n "$DAEMON_CONF" ] || exit 0

DAEMON_OPTS="-B -P $PIDFILE $DAEMON_OPTS -d  $DAEMON_CONF"

. /lib/lsb/init-functions

case "$1" in
  start)

...

```

#### Configuration of the network

assuming eth0 get an ip from dhcp
wlan0 is set to the fix address 192.168.42.1

```bash
sudo nano /etc/network/interfaces
```

edit your config in a similar way

```bash
auto lo

iface lo inet loopback
iface eth0 inet dhcp
#stand alone static number
#iface eth0 inet static
#  address 191.168.41.1
#  netmask 255.255.255.0


allow-hotplug wlan0
# wireless config home
#iface wlan0 inet manual
#wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf
#iface default inet dhcp

# static configuratin for dhcp server
  iface wlan0 inet static
  # die folgende IP wird die Server Adresse des via Accesspoint freigegebenen Netzwerkes
   address 192.168.42.1
   netmask 255.255.255.0


 up iptables-restore < /etc/iptables.ipv4.nat
```

#### config the iptable for an accesspoint

```bash
sudo sh -c "echo 1 > /proc/sys/net/ipv4/ip_forward"
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
sudo iptables -A FORWARD -i eth0 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT
sudo iptables -A FORWARD -i wlan0 -o eth0 -j ACCEPT
sudo iptables -t nat -S
sudo iptables -S
sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"
```

the output of the following script

```bash
$ sudo cat /etc/iptables.ipv4.nat
```

should be similar to

```bash
# Generated by iptables-save v1.4.14 on Mon Aug 12 16:42:57 2013
*filter
:INPUT ACCEPT [1379:89162]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [1491:900495]
-A FORWARD -i eth0 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -i wlan0 -o eth0 -j ACCEPT
COMMIT
# Completed on Mon Aug 12 16:42:57 2013
# Generated by iptables-save v1.4.14 on Mon Aug 12 16:42:57 2013
*nat
:PREROUTING ACCEPT [80:6839]
:INPUT ACCEPT [80:6839]
:OUTPUT ACCEPT [2:144]
:POSTROUTING ACCEPT [0:0]
-A POSTROUTING -o eth0 -j MASQUERADE
COMMIT
# Completed on Mon Aug 12 16:42:57 2013
```

### Testing

```bash
$ sudo service isc-dhcp-server start
$ sudo service isc-dhcp-server status
$ sudo service hostapd start
$ sudo service hostapd status
```

Reboot the system

See if you can connect to the accesspoint with SSID=Raspi2.
The default passwort is raspberrypi.

```bash
http://192.168.42.1
```

Redirect every Request to the website on the raspberry pi

#### DNS Mask

```bash
$ sudo apt-get install -y dnsmasq
$ sudo nano /etc/dnsmasq.conf 
```

Set the line with address active and change it in
the following way

```bash
address=/#/192.168.42.1
```

Test your system

```bash
$ sudo shutdown -r now
```

Backup your image
=================

Find the disknumber of the SD-card with the mount command
Change the X in the commandline to the disknumber

Backup SD-card to packed image

```bash
$ sudo dd if=/dev/rdiskX bs=1m | gzip > /path/to/backup.img.gz 
```

Restore image to SD-Card

```bash
$ gzip -dc /path/to/backup.img.gz | sudo dd of=/dev/rdiskX bs=1m
```




