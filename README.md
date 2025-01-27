# hLRS_Radio_Link
Heuristic OTA protocol for ELRS modules and CRSF protocol

The ELRS transceiver modules used in RC models happen to be almost identical to the hardware of my previous project, SX1281 BLE transceiver. With the exception that at the place of the ESP8266 and its external 4MB flash memory there is a ESP8285 with 1 MB internal flash. If the 4 MB are not needed, the internal flash is an advantage because the ESP8285 is protected against readout. 

It was straightforward to have the previously written firmware run on this much more compact module by very few minor portations. After this, it is challenging to write own firmware for the module acting as RC transceiver.

![dronedraw](https://github.com/user-attachments/assets/a74cffe3-0760-4201-8d72-562839fc0606)

The objective is that it remains compatible to the CRSF standard on the wired side. Furthermore, autodetection of its roll as "transmitter" or "receiver" shall be implemented. On both sides of the radio link, there is actually a transceiver because the link is bidirectional for transmitting telemetry data over the downlink and to be able to monitor the link quality and the battery status at all times, in compliance to regulations. In existing protocols, the so called "receiver" modules can also be used as "transmitter" but need to be flashed with specifically built version of the firmware for doing so.

Another functionality shall be the synchronization of the timers on both sides such that a FHSS can be implemented in the future.

The aim here is by no means to compete with the performance of the existing ELRS. The update rate shall be much slower, channel hopping is not yet implemented and at least for now, there is less protection against jamming or interference with other users. However, once an operational prototype is available, it gives some flexibility regarding future add-ons. Also, the software is entirely written in Arduino IDE and does not rely on radio libraries, but uses the register access method from the previous project, relying only on the SPI library. Therefore, shifting between LoRa, GFSK, FLRC and BLE should be possible.

A similar project exists under the name of mLRS. However, this is also a protocol that is more advanced and aims at increased performance. In this context, it is often claimed that in principle the radio link should just be a plain transparent serial connection. Since basically, a quadrocopter can "fly by wire" simply by connecting a serial cable from the RC to the flight controller. The comparison holds only partially, because the radio link, unless if it has two RF modules on both sides, is only half-duplex. Consequently, inbound packets must be dropped or otherwise would have to be queued (i opt for dropping them..). Also it should be mentioned that besides sending packets to the opposite side, the transceivers must generate some packets locally and inject them into the stream: when using a serial cable, there would be no "link statistics" and the respective packet, although formally a telemetry frame, cannot originate from the flight controller. To obtain such a packet, the two transceivers must send each other their link data, and at the RC side, the packet must then be composed from this joint data. Likewise, in the case of connection loss, the receiver must generate a channel control frame with the failsafe flag set and send it to the flight controller.
