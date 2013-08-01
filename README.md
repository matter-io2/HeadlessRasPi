HeadlessRasPi
=============


headless operation of the Raspberry Pi. Scripts, sample configurations, and Readme's.

H.A.R.P.S: HEADLESS ASSISTED RASPBERRY PI SETUP

Overview:
  Shell scripts and configurations for running the Raspberry Pi as "Headless." Without
	needing a screen for setup, the Raspberry Pi will broadcast a local network of its 
	own, allowing the user to SSH in and configure a WiFi Connection.

Developed by Matter Labs at MatterIO, Cambridge, Massachusetts, in summer 2013

TABLE OF CONTENTS:

1. Git Repository and Contents, and Definitions
	
2. Installation and Configuration
	
3. Operation
	
4. Troubleshooting
	
5. Resources



1: GIT REPOSITORY, Contents, and Definitions:

	Appropriate files can be found on the MatterIO Git Repository.

	This includes all files necessary for operating the Pi as headless, with a few
	exceptions.

	-Files included:
		This README.txt
		goWiFi
			a script that prompts the user for a network to connect to. Requires only
			an SSID and a password. Script rewrites interfaces files, and restarts the 
			Raspberry Pi
		goAdHoc
			A script that causes the Raspberry Pi to begin broadcasting its own network. 
			If dnsmasq is configured (see below), the Pi will also serve IP addresses
			to anyone connecting to the network.
		connectionTest
			Checks if the Raspberry Pi is connected to the internet, and also returns if
			the Raspberry Pi is broadcasting its own network, or interfacing with WiFi.
			If there is no internet connection, the script tells the Raspberry Pi to 
			broadcast its own network.

	-Packages needed to be installed and/or configured by the user:
		dnsmasq
		rc.local

2: INSTALLATION:

	-dnsmasq
	If dnsmasq is not installed, you will need to connect the raspberry pi to the internet
	and install it, and configure it. To configure it:

		sudo apt-get update
		sudo apt-get install dnsmasq
		sudo nano /etc/dnsmasq.conf
		uncomment and edit the line that says "dhcp-range=[some example]"
		Set it to:
		dhcp-range=192.168.1.5,192.168.1.50

	This tells dnsmasq to assign anyone connecting to the Raspberry Pi's Ad-Hoc network an
	IP Address. You can in fact skip this step, and assign yourself an IP from your
	computer each time you connect, but it is slightly inconvenient.

	-Shell Scripts
	You will need to put the scripts goWiFi, goAdHoc, and connectionTest into the /bin 
	folder so that they are excecutable.

	-Permissions:
	You will need to change the permissions of the /etc/network folder so that changes 
	can be made by the scripts

		sudo chmod 777 /etc/network
		sudo chmod 777 /bin/goAdHoc
		sudo chmod 777 /bin/goWiFi
		sudo chmod 777 /bin/connectionTest

	-rc.local
	To make connectionTest run on startup, it needs to be added to /etc/rc.local

		sudo nano /etc/rc.local
		Add the following line before the "exit 0" prompt:
			connectionTest


3: OPERATION:

	Under normal operation, the Raspberry Pi will broadcast a network when it is not 
	connected to the internet. By default, this network is named RasPi_AdHoc.

	Connect to it as you would any other network. By default, there is no password.

	SSH into the Pi. By default, the Pi has the static IP address of 192.168.1.1

		sudo ssh pi@192.168.1.1

	Enter in the passwords at the prompts.

	Now you should be in!

	Type:
		goWiFi

	The Raspberry Pi will ask you for an SSID of an actual network and a password. Enter
	them in here.

	Once you enter them in, the Raspberry Pi will let you know that an interfaces file was
	generated, and that the Pi will shutdown and try to connect to the internet.

	In the event that the SSID that was entered is invalid, connectionTest which runs on
	startup will know that the Pi is not connected to the internet and begin broadcasting
	the Ad-Hoc network again so that the user can try again.

4: Troubleshooting

	The only error I encountered when developing this was that my computer did not connect
	due to the ssh keys being incorrect. The error may look like this:

	@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
	@ 	WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED! 	  @
	@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
	IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
	Someone could be eavesdropping on you right now (man-in-the-middle attack)!
	It is also possible that a host key has just been changed.
	The fingerprint for the RSA key sent by the remote host is
	b4:73:67:a5:fe:38:be:49:94:a0:82:d5:45:18:7f:dc.
	Please contact your system administrator.
	Add correct host key in /Users/swobo04/.ssh/known_hosts to get rid of this message.
	Offending RSA key in /Users/swobo04/.ssh/known_hosts:2
	RSA host key for 192.168.1.1 has changed and you have requested strict checking.
	Host key verification failed.

	To fix this, run this from the command line on your own computer:
		sudo ssh-keygen -R 192.168.1.1

5: Resources: 

	To read more about Ad-Hoc networking on the Raspberry Pi and automating this process,
	see the following links:

	http://sirlagz.net/2013/01/22/script-starting-hostapd-when-wifi-goes-down/
		Author runs a script to check internet connectivity.

	http://www.raspberrypi.org/phpBB3/viewtopic.php?f=36&t=15130
		Forum topic about Ad-Hoc networking. Can find /etc/network/interfaces
		lower in the conversation.

	http://blog.sip2serve.com/post/38010690418/raspberry-pi-access-point-using-rtl8192cu
		Blog post on configuring the Pi as an access point and drivers for hostapd

	http://spin.atomicobject.com/2013/04/22/raspberry-pi-wireless-communication/
		blog detailing basic ad-hoc configuration

	http://www.pi-point.co.uk/
		Raspberry Pi foundation endorsed method for making Raspberry Pi a wireless access
		point. From a while ago (early 2012)

	http://wiki.buglabs.net/index.php/Ad-hoc_Networking
		Documentation from another company's way of making their embedded electronics
		broadcast and connect to Ad-Hoc networks. Not sure what OS they run, but it 
		is almost undoubtedly unix/debian based.

	https://wiki.debian.org/WiFi/AdHoc
		Documentation from Debian wiki about networking and Ad-Hoc networks. Very useful,
		as Raspbian (the OS we currently run) is a version of Debian.

	http://my-music.mine.nu/raspberrypi.htm
		An immensely thorough resource which outlines setting up Ad-Hoc networks, LAN, and
		web-server functions on the Pi. The webpage itself if being served from a 
		Raspberry Pi.

	http://linuxcommand.org/writing_shell_scripts.php
		Want to learn bash scripting? This is the best online guide I could find.
