# Usage scenario #

---


The information below is thought for the situation when you operate a Raspberry Pi board as a standalone headless (no screen or keypad or mouse is attached to it) server device. It assumes that it has some kind of network connection (wired or wireless TCP/IP) up and running and, if DHCP is used, you have a decent DHCP server in your LAN (hosted on your router, NAS, firewall, ...)

# Setup #

How can I setup a virgin Raspberry Pi without having it connected to display and keyboard for initial installation?

  * Prepare a SD-Card with the latest Raspbian Image
  * Put this SD-Card into the virgin Raspberry Pi and reboot
  * SSH login to the new Raspberry Pi using the default hostname or the new IP address (can be found by scanners like Fing)
  * Change the hostname in /etc/hostname and /etc/hosts and reboot
  * SSH login using the new hostname (or IP address). All other headless server related setups (memory split, turbo mode, OS and packages  updates) can be done over SSH as well.


How can I clone the setup of an existing Raspberry Pi to another?

  * Assumed is you have already another Raspberry Pi running in your LAN
  * Prepare a SD-Card in the other Raspberry Pi that has SSH enabled and network ready to run (either via wired LAN and/or automatic WLAN connection, preferrably using DHCP)
  * Save and clone this SD-Card to another one
  * Put this SD-Card into the virgin Raspberry Pi and reboot
  * SSH login to the new Raspberry Pi using the old hostname or the new IP address (can be found by scanners like Fing)
  * Change the hostname in /etc/hostname and /etc/hosts and reboot
  * SSH login using the new hostname (or IP address). All other headless server related setups (memory split, turbo mode, OS and packages  updates) can be done over SSH now.

Alternatively to DHCP there is an option to set a fixed IP address as kernel boot parameter in the boot partition's cmdline.txt (ip=x.x.x.x) and use that IP for the first SSH login.

# Hardware #

---


## Powering the board ##

From my experience the Raspberry Pi devices are somewhat sensible to a good power supply. I even had the situation that when running a board with the 1.2 Amp PSU officially supplied by RS Components it suddenly stopped working after some minutes without doing anything with it. Running the same board with absolutely identical software and hardware attached to it with a (less potential) 0.7 Amp Samsung Smartphone PSU did work well for hours. So this no advertisement for Samsung Smartphone PSUs as the situation could also have been opposite. It's just a hint that any weak kind of PSU may cause problems like unmotivated shutdowns.

### Tip 1 ###

If you face server apps stopping responding after some time without producing any error messages check if there may be a problem with the PSU. In my case above, I had a SSL console open and luckily saw a broadcase message that the system is going to halt now but no other error messages before that. As no one did enter any halt command I'm pretty sure that some mechanism within Linux/Raspbian triggers this behaviour when problems with the power are monitored by the BCM processor chip. I also could see the same effect when having the Raspberry Pi connected to a breadboard and moving it around on the table.

### Tip 2 ###

Running server software like WebIOPi can create hardware problems in an indirect way. Indirect means that the server may stop responding but it does this not because of an error within the software itself. It stops responding because some call to the server activated some hardware devices in a way that may cause PSU problems and the Raspberry Pi may shutdown automatically as mentioned in tip 1.

Typical candidates for such things are:
  * taking more than ca. 50 mA from the 3.3V header pin
  * taking more than ca. 500 mA from the 5V header pin
  * direct connecting to/from 5V to/from native GPIO pins

More subtle candidates for such things are:
  * doing the above overloads not permanent but as a short peak load in transisitons
  * connecting long stripes/leads (e.g. to a breadboard or breakout board) to input pins without proper termination (e.g. direct to GND or 3.3V or pullup/pulldown) even if the are unused
  * using long stripes/leads for connections that do not support this when connected direct to the relevant header pins (e.g. SPI or I2C lines)
  * using long to very long stripes to GPIO ports (or GPIO4 used for bit-bang 1-wire) as this will introduce EMV noise to the Raspberry Pi even if they are properly terminated

So it's always worth while to have a SSL console open to at least notice unwanted shutdowns if they occur.