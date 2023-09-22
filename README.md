# Hardware Security

**Introduction**

Embedded Devices are computers small enough to be included in the structure of the equipment that can be controlled. These computers are generally microprocessors that most likely have memory and interfaces to control the equipment in which they are embedded.

The Following Are Examples of Embedded Devices:

* Credit Cards Smart Chip
* Smart TV
* Car’s Electronic Control Unit (ECU)
* Magnetic Resonance Imaging (MRI) - Operates on Windows 98
* Programmable Logic Controllers (PLCs) - In Industrial Settings
* Internet of Things

During Development, aspects such as safety, functionality, reliability, size, power consumption, time-to-market, cost, and security are subject to trade-offs.

Security is sometimes the prime function of an embedded device, such as in credit cards.

Time-to-Market could be significant consideration with a new product because a company needs to get into the market before losing dominance to competitors.

Also, Safety is the prime function when it comes to automotive.

**Hardware Attacks?**

A device comprises both software and hardware, we consider software to consist of bits, and hardware to consist of atoms. we regard firmware (Code that is embedded in the embedded device) to be the same as software (bits).

When Speaking of hardware attacks, it’s easy to conflict between an attack that uses hardware and an attack that targets hardware. (The Delivery Method, and The Target)

For Example:

* We can attack a device’s ring oscillator (Hardware Target) By glitching The Supply Voltage (Hardware Attack)
* Or We Can Inject a Voltage Glitch on a CPU (Hardware Target) that influences an executing program (Software Target) etc…
