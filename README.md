HSMM-Pi
=======

HSMM-Pi is a set of tools designed to easily configure the Raspberry Pi to function as a High-Speed Multimedia (HSMM) wireless mesh node, compatible with the [Amateur Radio Emergency Data Network (AREDN)](https://www.arednmesh.org/). 

We aim to provide a large feature set for advanced users while having a minimal number of quick-start steps for anyone to easily access the mesh. The goal is to keep configuration time to that of a few hours (cumulative) with minimal guidance. The three use cases envisioned are:

1. an 'off-grid' network
2. adhoc emergency network
3. an uncensored 'internet' with mitigated counterparty risk

Mesh networks offer everyone the ability to operate high-speed data networks in a competitive way to the internet and independent of internet service providers. The mesh is scalable, self repairing and ***uncensored***. While the mesh runs on linux devices, it is agnostic to the device which uses its networking capability. 

At minimum a computer running an Ubuntu-based OS with a wifi card is all that is required. Although the code is designed for a Raspberry Pi and other single board computers, this code has run from a properly configured VirtualBox VM and surplus desktop computers. 

Licensed amateur radio operators can operate their mesh wifi devices at higher power and/or with larger antennas than are available to unlicensed users. These devices offer longer simplex range at the expense of compliance with the operating restrictions such as limitations on encrypted data usage over the mesh. It is suggested not to mix amateur licensed networks and Part 15 unlicensed low power networks for this reason.

The project consists of a PHP web application that is used to configure and monitor the mesh node, and an installation shell script that installs dependencies and puts things in the right spots.

This updated HSMM-Pi project is designed to run on Ubuntu LTS systems (currently 20.04) and Raspberry Pi OS Buster.  Rather than providing an OS image for HSMM-Pi, an installation script transforms a newly-imaged host into an HSMM-Pi node.  This has several benefits:

 * Greater transparency:  You can see exactly which changes are made to the base system by looking at the install shell script: install.sh
 * Easier to port to more platforms: Any platform that runs the supported Ubuntu/Debian LTS releases ought to be capable of running HSMM-Pi
 * Easier to host:  only the installation script and webapp files require hosting on Github.
 * Easier to seek support: Ubuntu/Debian is widely used and supported, no need to introduce another customization.

Hardware Requirements
=====================
HSMM-Pi has been tested to work with the following:

* Raspberry Pi running the Raspbian OS
* BeagleBone running Debian
* the BeagleBone Black running Ubuntu 12.04 from the onboard eMMC flash memory
* Ubuntu 20.04 LTS VirtualBox VM with USB wifi card
* Surplus 64-bit Intel computer (Dell) with Ubuntu 20.04 LTS server and generic PCIe wifi card

The requirements for each are listed below.

Raspberry-Pi Node:

 *  Raspberry Pi (any model, however, at this time, RPI4 and RPI0W have ***not been tested***)
 *  USB WiFi adapter (tested with the N150 adapter using the Ralink 5370 chipset, and with the Alfa AWSU036NH), or built-in WiFi adapter (on Raspberry Pi 3)
 *  SD memory card (8GB minimum, 32GB minimum recommended)

BeagleBone Node:

 *  BeagleBone or BeagleBone Black
 *  USB WiFi adapter (tested with the N150 adapter using the Ralink 5370 chipset)
 *  SD memory card (8GB minimum, , 32GB minimum recommended) (in the case of BeagleBone Black, used for initial imaging of the eMMC flash memory only)

Modes
=====
HSMM-Pi has three operating modes:

1. Internal
2. Mesh Gateway
3. Tunnel

The mesh offers a select few node-based services. These are designed to provide a minimal decentralized functionality primarily to the off-grid, emergency services user. They do not replace or prohibit the use of standard network services architecture (i.e. web servers, client software such as a cryptocurrency node or torrent, etc). Optional mesh-side built-in services allowed in any operating mode include:

***These features are notional and have not been implemented.***

1. FTP NAS
2. Cloud Storage
3. Torrent
4. BBS

A description of each is provided below.

Mesh Gateway Mode
-----------------
A node in Mesh Gateway mode routes traffic throughout the mesh, and provides the mesh with Internet access through the wired Ethernet port (on the Raspberry Pi).  The gateway *obtains* a DHCP lease on the wired interface, and advertises its Internet link to mesh nodes using OLSR.

It is highly recommended that you take precautions to prevent mesh users from accessing your local network. 

Licensed amateur radio operators should not use this mode or join a mesh with this enabled. Joining may cause a violation of license requirements. 


Internal Mode
-------------
A node in Internal mode routes traffic throughout the mesh and provides mesh access to any hosts connected to its wired Ethernet port.  The node in this mode runs a DHCP server that *issues* DHCP leases to any hosts on the wired connection.  It also runs a DNS server that can provide name resolution for both mesh nodes and Internet hosts.  The following diagram shows how the two types of nodes can be deployed:

![Diagram](http://i.imgur.com/sfR3Q12.png)

There could be any number of mesh nodes in the Ad-Hoc WiFi Network.  The route among the nodes is managed entirely with OLSR.

I've done all of my testing with N150 USB wifi adapters that use the Ralink 5370 wireless chipset.  These adapters are cheap (~$7 USD), compact, and easy to come by.  They also use drivers that are bundled with most Ubuntu distributions, making setup easy.  The N150 adapter tested included a threaded antenna connector that should make it easy to add a linear amplifier and aftermarket antenna (outside the scope of the HSMM-Pi project).

Tunnel
------

***Future Feature. Not yet enabled***

The Tunnel mode uses an internet link to connect multiple nodes in a peer-2-peer fashion. This provides a network extension when a physical mesh cannot reach. Internet access to meshed devices is blocked. 

Nodes are added by including their IP address or URL to the ... and sharing public keys. Data is encrypted between nodes.

FTP NAS
-------

***Proposed feature. TBD***

By default, HSMM-Pi uses 1 Gb of local storage (more can be used as set in the configuration file) to provide a file server to the mesh. This storage can be accessed by any mesh user as a File Transfer Protocol (FTP) service. An access password may be set to protect you from users placing illegal content in your device.

For those using this mesh under Part 15 rules, the use of [veracrypt](https://www.veracrypt.fr/en/Home.html) or other file encryption software external to mesh functionality is suggested.  

Cloud Storage
-------------

***Future Feature. TBD***

***Not for licensed amateur radio operators***

Cloud Storage is an optional feature that can be enabled through the web application and replaces the FTP feature. This feature uses a preset amount of free disk space (usually 1-20 Gb) as a cloud storage device accessible to all nodes. Uploaded files are encrypted and parted out to multiple nodes. Node users with the correct key can access this data. The mesh automatically moves parts to nodes closest to mesh users that access that data often. Files that are not accessed for a while are 'forgotten'.

This mode of sharing is inspired by the [freenet datastore](https://freenetproject.org/pages/documentation.html#understand) and will be designed to be functionally similar. 

Torrent
-------

***Proposed feature. Still on the drawing board***

For larger networks and to reduce mesh demand through bottleneck nodes, files in storage can transit the mesh in parts through multiple paths... Each node with Torrent enabled is a tracker... Files are shared as ###.mtorrent... TBD

BBS
----

***Proposed feature. Still on the drawing board***

For those not interested in running a proper email server, amateur radio operators reminiscing of the old days of packet radio, or those interested in decentralized email-like communication, the mesh software provides a Bulletin Board System (BBS) built in!

This BBS offers heirachical routing, a mail box and telnet access anywhere on the mesh. TBD...

Raspberry Pi Installation
=========================

1. Download the [Raspbian Buster](https://www.raspberrypi.org/downloads/) disk image or latest version. The Lite image is suitable for headless installations as it omits the graphical interface, web browser, etc.
1. Write the image to an SD memory card. Use of the Raspberry Pi Imager software is recommended.
2. Place a blank text file named 'ssh' in the boot directory on the newly created SD memory card.
1. Insert the card into a (non-powered) Raspberry Pi
1. Connect the wired Ethernet port on the Pi to a network with LAN access
1. Apply power to the Pi
2. Through your router's interface, discover the assigned IP to the Pi. Using a static IP address is recommended when using Mesh Gateway Mode.
1. Login to the Pi through an SSH session, the console, or the terminal application. The username is "pi" and the password is "raspberry".
1. Run the Raspberry Pi Setup program:

        sudo raspi-config
1. Expand the filesystem to fill the SD memory card
1. Change the password for the "pi" account
1. If installing over an SSH connection to the Pi, then I recommend you install "screen" (`sudo apt-get install screen`) to ensure that the installation script is not stopped prematurely if you lose connectivity with the Pi.  This is optional, but I highly recommend using screen if installing over the network.  You can find more info on screen [here](https://savannah.gnu.org/projects/screen)
1. Run the following commands to download the HSMM-Pi project and install

        sudo apt-get update
        sudo apt-get install -y git
        git clone https://github.com/alexpaths/hsmm-pi.git
        cd hsmm-pi
        sh install.sh
1. Login to the web application on the Pi:
http://(wired Ethernet IP of the node):8080/
1. Access the Admin account using the username "admin" and password "changeme".
1. Change the password for HSMM-Pi web application
1. Configure as either an Internal or Gateway node
2. Restart and enjoy


BeagleBone Black Installation
=============================

***These instructions are here for posterity from the original release from urlgrey and have not been recently tested***

1. Download the latest BeagleBone Black Ubuntu 12.04 image: http://www.armhf.com/index.php/boards/beaglebone-black/#precise
1. Write the image to an SD memory card using the steps on the page referenced in the previous step
1. Insert the SD card into a BeagleBone Black board
1. Apply power to the BeagleBone Black
1. Login to the BeagleBone Black through an SSH session or the console using the 'ubuntu' account
1. Transfer the image to the running BeagleBone Black using SCP
1. Write the image to the eMMC flash memory using the steps mentioned in the first step here.
1. Wait for all 4 LEDs to go solid (could take several minutes)
1. Shutdown the Beaglbone Black (sudo /sbin/init 0)
1. Remove the memory card from the BeagleBone Black
1. Apply power to the BeagleBone Black
1. Login to the BeagleBone Black through an SSH session or the console using the 'ubuntu' account
1. Change the password for the 'ubuntu' account
1. Install the development tools necessary to build OLSRD and retrieve the HSMM-Pi project:

        sudo apt-get upgrade
        sudo apt-get install make gcc git
1. If installing over an SSH connection, then I recommend you install 'screen' (sudo apt-get install screen) to ensure that the installation script is not stopped prematurely if you lose connectivity.  This is optional, but I highly recommend using screen if installing over the network.  You can find more info on screen here: http://linux.die.net/man/1/screen
1. Run the following commands to download the HSMM-Pi project and install

        git clone https://github.com/urlgrey/hsmm-pi.git
        cd hsmm-pi
        sh install.sh
1. Login to the web application:
http://(wired Ethernet IP of the node):8080/
1. Access the Admin account using the 'admin' username and 'changeme' password.
1. Change the password for HSMM-Pi
1. Configure as either an Internal or Gateway node

Generic Ubuntu/Debian Instructions
=====================

While a full desktop seems like a surplus of power for this mesh device, they are usually very inexpensive. At a local business surplus, I purchased an older machine completely capable of running mesh software and more for 10 $USD (without harddisk). In addition, the desktop can double as a server for on-mesh services. 

1. Install a fresh copy of [Ubuntu LTS](https://ubuntu.com/download/server) on a wiped machine or new Virtual Machine (VM). Note: VMs must be run locally and have the wifi device routed into the VM. Port ... must be set to be accessible by the host machine. 
1. Connect the wired Ethernet port on the computer to a network with LAN access if using SSH or configuring as a Mesh Gateway. 
2. Through your router's interface, discover the assigned IP to the computer. Using a static IP address is recommended when using Mesh Gateway Mode.
1. Login to the computer through an SSH session if required
2. Change username and password as required by best practices to secure your machine
1. Install the development tools necessary to build OLSRD and retrieve the HSMM-Pi project:

        sudo apt-get upgrade
        sudo apt-get install make gcc git screen

1. Run the following commands to download the HSMM-Pi project and install

        git clone https://github.com/alexpaths/hsmm-pi.git
        cd hsmm-pi
        sh install.sh
1. Login to the web application:
http://(wired Ethernet IP of the node):8080/
1. Access the Admin account using the 'admin' username and 'changeme' password.
1. Change the password for HSMM-Pi web application
1. Configure as either an Internal or Gateway node
2. Restart and enjoy



Upgrade Steps
=============
This is experimental, and you should fall back to a fresh installation if things aren't functioning as you'd expect.  This is supported only on the HEAD of the master branch.

1. Login to the host using SSH or the console
1. Run the following commands to upgrade:

        cd ~/hsmm-pi
        git pull
        sh install.sh
1. Access the web UI and check the configuration.  Save the Network and Location settings, even if no changes are needed.
1. If the save operation fails, then you might need to replace the SQLite database file due to database schema changes.  Run the following command:

        cd ~/hsmm-pi/
        sudo cp src/var/data/hsmm-pi/hsmm-pi.sqlite /var/data/hsmm-pi/
1. Repeat the step of reviewing and saving the configuration through the web UI.

Node Configuration
==================
This represents the minimum set of steps:

1. Select Admin->Network from the menubar
1. Configure the WiFi interface:
    1.  Specify an IP address that will be unique throughout the mesh network.  This will be different every mesh node.  A default will be provided based on the last 3 fragments of the WiFi adapter MAC address.
1. Configure the Wired interface:
    1. Internal Mesh Configuration: Set the Wired interface mode to LAN.
    2. Gateway Node Configuration: Set the Wired interface mode to WAN
1. Configure the Mesh settings
    1. Specify your node name. It is best practice to keep the name as short as possible to remain unique. Amateur radio operators must use their callsign in the name. Use of the [APRS suffix](https://www.jpole-antenna.com/2018/09/25/aprs-ssids-paths-and-beacons/) or [AREDN best practices](https://arednmesh.readthedocs.io/en/stable/arednGettingStarted/basic_setup.html) is encouraged.
1. Click 'Save'
1. If successful, click the 'Reboot' button in the alert and proceed.

Tunnel Node Configuration
=========================



Network Topology and Design
===========================


Domain Names and DNS
====================

FAQ
====

A list of frequently asked questions can be found on [our wiki](https://github.com/urlgrey/hsmm-pi/wiki), at the [FAQ page](https://github.com/urlgrey/hsmm-pi/wiki/FAQ).

References
==========

urlgrey's original [HSMM-Pi Blog](http://hsmmpi.wordpress.com/)

For a video tour, see urlgrey's original [YouTube video](http://www.youtube.com/watch?v=ltUAw02vfqk)