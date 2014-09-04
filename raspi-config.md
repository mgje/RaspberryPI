### Change Computer-Name

```bash
$ sudo raspi-config
```

*  Advanced Options
*  Change Name

### Change WIFI Parameters

```bash
$ sudo nano /etc/hostapd/hostapd.conf
```

* change SSID
* change Frequncy

### Conect via wifi to raspberry Pi

#### SSH
```bash
$ ssh pi@192.168.42.1
```

#### Remote Desktop (MAC)
```bash
$ open vnc://pi@:192.168.42.1:5901
```

###  Konfigurations WBZ-course

name 		 			ssid				channel
raspi-7		               Raspi7              9
martin             			mgu                  8
raspi-8					Raspi8             7
raspi-5					Raspi5			6
raspi-9					Raspi9			5
raspi-1					Raspi1			4
raspi-10					Raspi10	          3
raspi-2					Raspi2			2
raspi-3					Raspi3			1




