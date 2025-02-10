# hLRS_Radio_Link
Heuristic OTA protocol for ELRS modules and CRSF protocol

The ELRS transceiver modules used in RC models happen to be almost identical to the hardware of my previous project, SX1281 BLE transceiver. With the exception that at the place of the ESP8266 and its external 4MB flash memory there is a ESP8285 with 1 MB internal flash. If the 4 MB are not needed, the internal flash is an advantage because the ESP8285 is readout protected. 

It was straightforward to have the previously written firmware run on this much more compact module by very few minor portations. After this, it was challenging to write a proprietary firmware for the module acting as RC transceiver.

![dronedraw](https://github.com/user-attachments/assets/a74cffe3-0760-4201-8d72-562839fc0606)

A main feature is drop-in compatibility to the CRSF standard on the wired side. Furthermore, autodetection of its roll as "transmitter" or "receiver" has been implemented. On both sides of the radio link, there is actually a transceiver because the link is bidirectional for transmitting telemetry data over the downlink and to be able to monitor the link quality and the battery status at all times, in compliance with air traffic regulations. In existing ELRS protocol, the so called "receiver" modules can also be used as "transmitter" but need to be flashed with specifically built version of the firmware for doing so.

Another functionality is the synchronization of the timers on both sides such that FHSS can be enabled.

The next distinctive feature is TDMA telemetry: in a 1 transmitter N receiver setup, each receiver can transmit its telemetry data in a dedicated time slot.

The aim here was not to compete with the performance of the existing ELRS. The refresh rate is lower. However, since the protocol was entirely developped in-house, it gives some flexibility regarding future add-ons. Also, the software is entirely written in Arduino IDE and does not rely on radio libraries, but uses the register access method from the previous project, relying only on the SPI library. Therefore, shifting between LoRa, GFSK, FLRC and BLE should also be possible.

A similar project exists under the name of mLRS. However, this is also a protocol that is more oriented at increased performance. In this context, it is often claimed that in principle the radio link should just be a plain transparent serial connection. Since basically, a quadrocopter can "fly by wire" simply by connecting a serial cable from the RC to the flight controller. The comparison holds only partially, because the radio link is only half-duplex. Consequently, inbound packets must be dropped or otherwise would have to be queued (i opt for dropping them..). Also it should be mentioned that besides sending packets to the opposite side, the transceivers must generate some packets locally and insert them into the stream: when using a serial cable, there would be no "link statistics" and the respective packet, although formally a telemetry frame, cannot originate from the flight controller. To obtain such a packet, the two transceivers must send each other their link data, and at the RC side, the packet must then be composed from this joint data. Likewise, in the case of connection loss, the receiver must generate a channel control frame with the failsafe flag set and send it to the flight controller.


Watch on youtube a demonstration of the time slots (TDMA) for telemetry downlink from multiple receivers :

https://www.youtube.com/watch?v=aI1-UF5SBdM


![P1093242](https://github.com/user-attachments/assets/f0c80746-878c-47fe-a100-388d69e4cc0a)  ![P1093253](https://github.com/user-attachments/assets/d1eb3136-74f8-4958-a969-149b606cd933)

ELRS module, front with the SX1281 and a PA/LNA, and back with the ESP8385


For further information, feel free to PM me at heinrich DOT diesinger AT gmx DOT net.





Links:

Sbus protocol sniffing and reverse engineering of the FrSky16 protocol, using Realterm :

https://www.youtube.com/watch?v=CULas2y_sXI


Sniffing/parsing, speedybee f405 r2, t2, CRSF; types of frames, Python script for translating/displaying telemetry frames :

https://medium.com/@mike_polo/parsing-crsf-protocol-from-a-flight-controller-with-a-raspberry-pi-for-telemetry-data-79e9426ff943


CRSF wire protocol, the wire side of the protocol is the same for elrs; duplex, half duplex, autobaud, packet types :

https://github.com/crsf-wg/crsf/wiki/Physical-Layer


Vulnerabilities of ELRS :

https://www.nccgroup.com/us/research-blog/technical-advisory-expresslrs-vulnerabilities-allow-for-hijack-of-control-link/

https://securityaffairs.com/133146/hacking/expresslrs-protocol-flaws-drones-takeover.html


Vulnerabilities of TBS Crossfire, OTA sniffing using ESP32 Heltec board, wire/SPI sniffing using FPGA board :

https://www.g3gg0.de/fpv/fpv-analysis-of-tbs-crossfire/


Frequencies, parameters of ELRS :

https://www.rcgroups.com/forums/showthread.php?4144003-New-2-4G-LORA-protocol/page19

p. 19 #279 waterfall diagrams; above he says he choose bind frequency 2452 MHz; the channel bandwidth is 812 Khz(set in SX1280 register). He set up a range of 68 hopping frequencies of 1MHZ apart; this is a group trying to write own software


https://www.expresslrs.org/info/signal-health/#rf-mode-indexes-rfmd

see table below RF mode Indexes with sensitivity minima in dBm


https://culturefpv.fr/expresslrs-un-nouveau-lien-radio-20210418/

ranges in km


Wilhelm Meier, Funktionsmodellbau und Fernbedienungen :

https://github.com/wimalopaan/Electronics

https://www.youtube.com/@wilhelmmeier6124




