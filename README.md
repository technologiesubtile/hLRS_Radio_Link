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

For further information, feel free to PM me at heinrich DOT diesinger AT gmx DOT net.
