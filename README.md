Raspberry pi Peopoly Moai Web Control


Hardware needed:

Raspberry pi 3B+ with power supply: https://www.amazon.com/CanaKit-Raspberry-Power-Supply-Listed/dp/B07BC6WH7V/ref=sr_1_5?ie=UTF8&qid=1548353240&sr=8-5&keywords=raspberry+pi+3+b%2B+power+supply

Raspberry pi camera: https://www.amazon.com/gp/product/B012V1HEP4/ref=ppx_yo_dt_b_asin_title_o01__o00_s00?ie=UTF8&psc=1

Sandisk micro sd 16gb: https://www.amazon.com/gp/product/B013TMN4GW/ref=oh_aui_search_asin_title?ie=UTF8&psc=1

4 Relay board: https://www.amazon.com/gp/product/B00KTEN3TM/ref=ppx_yo_dt_b_asin_title_o00__o00_s00?ie=UTF8&psc=1

Jumper wires female to female: https://www.amazon.com/EDGELEC-Breadboard-Optional-Assorted-Multicolored/dp/B07GD2BWPY/ref=sr_1_6?s=amazon-devices&ie=UTF8&qid=1548353441&sr=8-6&keywords=female+to+female+jumper

JST XH Connectors: https://www.amazon.com/gp/product/B01IZW0IKG/ref=ppx_yo_dt_b_asin_title_o02__o00_s00?ie=UTF8&psc=1

USB keyboard may be wireless

USB mouse may be wireless

HDMI cable to connect to tv or monitor


Softare needed:

SD card formatter: https://www.sdcard.org/downloads/formatter_4/

Win32diskimager: https://sourceforge.net/projects/win32diskimager/

Raspbian Stretch Lite: https://www.raspberrypi.org/downloads/raspbian/

putty or putty 64 package installer: https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html

Filezilla: https://filezilla-project.org/


Misc items:
solder iron w/ solder
wire cutters
wire strippers or knife
hobby knife
heat shrink or liquid tape or electrical tape



Commands as per video:

sudo apt-get install rpi-update
sudo rpi-update
sudo reboot
sudo apt-get update
sudo apt-get upgrade
sudo reboot
sudo apt install git

To Install WiringPi:

sudo git clone git://git.drogon.net/wiringPi
cd wiringPi
sudo chown -R pi:pi /home/pi/wiringPi/
./build


setup pins:

sudo nano /etc/rc.local

add at bottom between fi and exit 0

gpio -g mode 5 out
gpio -g mode 6 out
gpio -g mode 13 out
gpio -g mode 19 out
gpio -g mode 20 in


sudo nano clockwise.cgi

#!/bin/bash

gpio -g write 5 1
sleep .1
gpio -g write 6 1
sleep .1
gpio -g write 5 0
sleep .1
gpio -g write 6 0


sudo nano counter.cgi

#!/bin/bash

gpio -g write 6 1
sleep .1
gpio -g write 5 1
sleep .1
gpio -g write 6 0
sleep .1
gpio -g write 5 0


sudo nano singlecl.cgi

#!/bin/bash

gpio -g write 13 1
sleep .1
gpio -g write 13 0


sudo nano power.cgi

#!/bin/bash

if [ $(gpio -g read 20) = 0 ]
then
gpio -g write 19 1
else
gpio -g write 19 0
fi


sudo chmod 755 clockwise.cgi
sudo chmod 755 counter.cgi
sudo chmod 755 singlecl.cgi
sudo chmod 755 power.cgi


sudo mkdir /var/www
sudo mkdir /var/www/cgi-bin
sudo chown -R pi:pi /var/
sudo chown -R pi:pi /var/www/
sudo chown -R pi:pi /var/www/cgi-bin/

sudo reboot?

sudo mv clockwise.cgi /var/www/cgi-bin
sudo mv counter.cgi /var/www/cgi-bin
sudo mv singlecl.cgi /var/www/cgi-bin
sudo mv power.cgi /var/www/cgi-bin

sudo reboot

sudo apt-get -y install lighttpd
sudo lighttpd-enable-mod cgi
sudo lighttpd-enable-mod fastcgi

sudo nano /etc/lighttpd/lighttpd.conf

change:
Y

server.document-root =“/var/www/html”

by:

server.document-root =“/var/www”

sudo /etc/init.d/lighttpd stop
sudo /etc/init.d/lighttpd start

cd /var/www

sudo nano index.html

<html>
<head>
</head>
		
<style>
body {background-color: lightyellow}
h1 {color:blue}
</style>

<body>
<div style="text-align:center">
<h1>Peopoly Moai Web Control</h1>
<br><br>
</body>
</html>

sudo chmod 755 index.html

go to your ip address and check


Adding Camera and streaming:

sudo apt-get install libjpeg62-turbo-dev
sudo apt-get install cmake
git clone https://github.com/jacksonliam/mjpg-streamer.git ~/mjpg-streamer
cd ~/mjpg-streamer/mjpg-streamer-experimental
make clean all
sudo rm -rf /opt/mjpg-streamer
sudo mv ~/mjpg-streamer/mjpg-streamer-experimental /opt/mjpg-streamer
sudo rm -rf ~/mjpg-streamer

sudo nano /etc/rc.local


LD_LIBRARY_PATH=/opt/mjpg-streamer/ /opt/mjpg-streamer/mjpg_streamer -i "input_raspicam.so -fps 15 -q 50 -x 320 -y 240" -o "output_http.so -p 9000 -w /opt/mjpg-streamer/www" &


With pc download:

https://github.com/Smitty505000/peopoly-moai-web-control.git

unzip and use filezilla to put 5 files in /var/www

Back in putty:

saudo nano /var/www/index.html

replace yourip with the ip of you raspberry pi

sudo reboot

open web browser and type in your raspberry pi ip address in the address bar



