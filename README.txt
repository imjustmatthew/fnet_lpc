fnet port for LPC176x
-------------------------------------------

This is a port of the FNET network stack (http://fnet.sourceforge.net) for 32-bit MCUs to
the NXP LPC176x ARM Cortex-M3 MCUs.

Currently only the base stack plus the HTTP demo application have been tested, other applications
have not been ported or tested.

General notes:
* The port has been compiled with the Code Red LPCXpresso IDE v5.0.10 (using the inbuilt GCC)
  and tested on the LPCXpresso (LPC1769) and mbed (LPC1768) boards.
* Link status is based on the standard link status registers on the PHY, so it should
  work with any Ethernet RMII PHY attached to the controller. i.e the LPCXpresso
  uses the LAN8720 and the mbed DP83848C.
* The code for the LPC port resides in cpu/lpc17xx with minimal changes to the compiler
  and CPU header files to include the LPC and GCC compiler.
* A heap of 9216 bytes (6 ethernet frames) has been allocated from the general RAM space,
  as well as AHB Block 2 (16kB) for Ethernet  DMA. From testing, a minimum of 4kB heap is 
  required to make HTTP work. In the future I may attempt to optimize the memory configuration.
  The heap size can be configured in fnet_user_config.h
* A copy of the CMSIS library configured to 100MHz CPU speed has been included. The LPC
  driver uses CMSIS constructs to communicate with the hardware - this is different to the
  existing FNET architectures which do not have such a dependency.
* Debugging information is available from UART0 at 9600bps. On mbed this is the
  serial port connected to USB, on LPCXpresso this is pin 21 and 22 (TX/RX)
* IP and IPv6, plus DHCP and HTTP have been enabled by default in this port.
* You may see the IPv4 address obtained from DHCP and the IPv6 address from router 
  advertisements from the serial output.
* In theory, this port should work with minimal modification on other LPC micros,
  such as the LPC1788, but I do not have any such hardware to test with.  
* The MAC address is generated at compile time as a function of time,
  with the manufacturer OUI set to 'Freescale' (00:04:9f:) as per FNET defaults
  This can be overriden in fnet_user_config.h
  In the future MAC should be set from hardware (i.e from the mbed ROM) as
  appropriate.
 
Known issues:
* A latency issue develops after using TCP connections (i.e browsing HTTP server or
  trying to port scan your micro via nmap) after some time, causing
  return latency to blow out to 1000ms. I have not been able to find any cause of
  delay anywhere in the stack - a Red Trace or other instrumentation probe would
  be helpful in this instance.
* The HTTP server does not work with IPv6 enabled on -Os or -O2 compiler setting.
  This appears to be due to general optimizations applied at this levels, not
  any specific optimization (-f) option.
  The 'Release' configuration has been set to compile as -O1 with the
  individual -Os features turned on

Please contact me (<matt@bionicmessage.net>) with any questions, queries
or comments.

- Mathew McBride
January 2013
