# Electronics and Serial Ports

Embedded devices commonly employ standardized communication interfaces to interact with other chips, users, and the outside world. These interfaces are typically low level, not externally accessible, and require interoperability among different manufacturers. As a result, they generally lack any protections, obfuscation, or encryption measures applied to them.

## Electricity Basics

#### **Voltage:**

The Volt (V, Expressed in units of V) is the electrical unit of voltage. it refers to electrical potential, or how hard the electrons are pushing to get from Point A to Point B (How hard the water is pushing from A to B).

It’s always measured between two points (Commonly: Negative and Positive). When People Measure voltage only with one point, the second point is actually called ground; Ground is a common reference for a system with a definition of 0V

#### **Current:**

The Amper (I, Expressed in units of A) is a measure of electrical **flow** or current, which refers to the number of electrons moving past a certain point in a given time. The difference between Voltage and Current is that voltage refers to the speed of the electrons, on the other hand, **current** refers to the quantity of electrons.

#### **Resistance:**

The Ohm (R, Expressed in units of Ω) is the measure of electrical **resistance**, or how difficult it is for the electrons to pass between two points. Resistance is comparable to how wide or narrow a hose is.

#### **Ohm’s Law:**

Ohm’s Law summarizes the relationship between Volts, amps, and ohms as (V = I \* R), Which States that knowing any two parameters allows you to calculate the third.

#### **AC/DC:**

Direct Current (DC) and Alternating Current (AC) refer to constant and varying currents, respectively. Modern electronics are powered from DC sources, such as batteries and DC power supplies. On the other hand, AC is a sinusoidally varying voltage, generally seen on the 240V or 110V power grid, but AC is also used in electronic equipment, such as switched power supplies.

#### **Impedance, Inductance, Reactance, and Capacitance**

Impedance in AC is equivalent to resistance in DC. In AC, **Impedance:** is a complex number made up of resistance and reactance and it depends on the AC signal’s frequency.

**Reactance:** is a function of inductance and capacitance.

**Inductance:** is the resistance by the circuit to a change in current. (if the water is flowing in one direction, it’ll take some energy to push it in the opposite direction due to the flowing water’s kinetic energy).

**Capacitance:** is the resistance to a change in voltage, as shown in the figure below.

<figure><img src="../../.gitbook/assets/C2F1.png" alt=""><figcaption><p><em>Figure2.1: Visualizing a capacitor as a water tank</em></p></figcaption></figure>

While there is high input pressure on the pipe, water is constantly flowing into the tank, until it is full (Charging). If the pressure drops at the input, the tank starts to drain until it is empty (Discharging). In other meanings, When the voltage across the capacitor is sufficient to raise the water level, it will store the charge. Conversely, if the voltage is too low, the capacitor will discharge the water and release the charge.

#### **Power:**

**Power** is the amount of **energy** consumed per second (W, Expressed in units of W). In an electronic circuit, this energy is exclusively turned into heat.

This is called **Power Dissipation,** the power rule for a given load can be measured by \
(P = I^2 \* R) or (P = I \* V), or (P = C \* V^2 \* _f_).

## Interfacing with Electricity

The art of using electricity to build a communications channel. The interfaces we encounter will use different electrical properties to be able to communicate in different ways, and each way has its own pros and cons.

### **Logic Levels**

In modern signaling techniques, bits (ones and zeros) serve as symbols. To create a complete communication scheme, additional special symbols may be utilized, such as parity bits for error detection or markers to indicate the start and end of a transmission. In representing these symbols, a "1" bit is equivalent to a logic high (e.g., 5V), while a "0" bit is equivalent to a logic low (e.g., 0V). However, due to the resistance in the wire, the receiving end may not receive the full 5V, instead receiving only 4.5V, for example. To counter this, agreed-upon switching thresholds are used, with anything less than 0.8V being considered a zero, and anything greater than 2V being considered a one. The most frequently employed set of thresholds is the transistor-transistor logic (TTL) set. As with any analog system, there is noise present, leading to signal fluctuation even if the sender sends a perfect 5V. If the switching threshold is set at 2V, the noise is not problematic and communication can be sustained with error-correcting codes. However, adversarial noise can corrupt communication by introducing noise that confuses the receiving end into seeing an attacker-controlled message. Fault injection can also be viewed as adversarial noise, and cryptographic signatures are necessary to prevent it.

<figure><img src="../../.gitbook/assets/C2F2.png" alt=""><figcaption><p><em>Figure 2.2: illustrates and encounters many logic thresholds.</em></p></figcaption></figure>

When checking device datasheets, you are likely to encounter LVCMOS devices, where LV stands for low voltage. This is to cater to the lowering of the original TTL and CMOS 5 V specs to 3.3 V or lower.

### **High Impedance, Pullups, and Pulldowns**

When devices enter a high impedance state, they become silent, which is distinct from being at 0V. If you were to connect 0V and 5V together, current would travel from the 5V end to the 0V end. However, if high impedance were connected to 5V, minimal or no current would flow. High impedance is analogous to high resistance in AC, like shutting off the tap on a hose. It also implies that a signal is incredibly susceptible to fluctuating between high and low voltages due to even the slightest interference, such as crosstalk or radio signals

In order to prevent devices from incorrect signals for legitimate data, pullups and pulldowns can be employed.

A pull-up resistor connects a signal to a high voltage, while a pulldown resistor connects it to ground (0V). Strong pull-ups (typically between 50Ω to 470Ω) generate a potent signal that requires a significant interference signal to override. Weak pull-ups (typically between 10KΩ to 100KΩ), on the other hand, maintain the signal high as long as there are no more powerful signals driving it to high or low voltages.

### **Push-Pull vs Tristate**

To enable bidirectional communication or multiple senders and receivers on a single wire, additional measures are required. Suppose two parties, referred to as "I" and "you," want to communicate. If I want to send data exclusively to you, the simple method of using 0 V to 5 V would suffice. This approach is called _push-pull output_ because I either push your input to 5 V or pull it to 0 V, with no involvement from you or anyone else.

However, if you now wish to send data to me over the same interconnected wires, I need to go into a quiet mode and switch to a high-impedance state. This allows you to respond to me.

For communication to occur, I can be in the one/zero state (talking) or in the high-impedance state (listening), which is also referred to as _Hi-Z_ or _tristate_ (a third state).

If we coordinate when we enter the tristate mode, we can allow several other devices to communicate on our wires. These groups of interconnected wires are referred to as **buses**, where everyone takes turns using shared wires.

<figure><img src="../../.gitbook/assets/C2F3.png" alt=""><figcaption><p><em>Figure2.3:Tristate communication between two devices</em></p></figcaption></figure>

In the circuit shown at the top, Device 2 takes control of the wire as it has set EN2 = 1 and EN1 = 0 (Hi-Z). It then sets the value B on the wire, which is then received by Device 1. On the other hand, in the circuit at the bottom, Device 1 is sending A as it has set EN1 = 1 and EN2 = 0 (Hi-Z).

### **Asynchronous vs. Synchronous vs. Embedded Clock**

One aspect that we overlooked in our TTL communication example is the concept of clocking. When we transmit a sequence of ones and zeros by alternately sending out 0 V and 5 V on the line, how can we differentiate between them without a clock signal?

#### **Asynchronous communication**

When two parties engage in asynchronous communication, they must first agree upon the signaling data rate.

The duration of my signal being high or low to represent one bit is determined by the data rate. If we agree that you will receive one bit every second, then you can interpret a 0 V signal for one second as 0, but a 0 V signal for three seconds as 000.

#### **Synchronous Communication**

Synchronous communication involves sharing a clock to synchronize the transmission of bits, but there are various ways to share a clock.

**Common Clock** refers to a situation where a universal clock synchronizes the different devices in the system. This clock is transmitted through electrical signals in the form of a high-voltage (_tick_) and a low-voltage (_tock_).

During the tick, I transmit data by setting the communication line to 5V. During the tock, you read the 5V and decode it as a 1. When the next tick occurs, I can leave the line at 5V, and on the second tock, you know that I have sent 11. However, it can be challenging to synchronize devices with different clock speeds, which can lead to complications.

**Source synchronous** communication looks similar to common clock communication from the receiver's perspective, but the difference lies in the fact that the sender sets the clock. As the sender, I send a tick before transmitting a value and then send a tock when I am finished. You, as the receiver, monitor the value every time you receive a tock.

Instead of using a separate clock signal, **embedded clock or self-clocking** signals include both data and clock information in the same signal. Instead of simply equating 5 V to one and 0 V to zero, these signals use more complex patterns that incorporate clock information.

<figure><img src="../../.gitbook/assets/C2F4.png" alt=""><figcaption><p><em>Figure2.4: Manchester Encoding</em></p></figcaption></figure>

#### **Differential Signaling**

All the concepts discussed so far relate to single-ended signaling, which indicates that a single wire is utilized to convey a sequence of ones and zeros. This technique is straightforward to implement and performs sufficiently with basic devices at lower speeds. However, if single-ended signals are sent to you at the MHz range, you will perceive high and low levels with curved edges, rather than the square waves.

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption><p>Figure2.5: High Frequency Square Pulses</p></figcaption></figure>

The rounded edges that you observe when I transmit single-ended signals to you into the MHz range are known as the ringing effect. This phenomenon arises due to the impedance and capacitance of the transmission wire, and it causes the signal to become less purely digital and introduces an analog variation.

To embrace the analog nature of signals and mitigate noise and interference, differential signaling employs two wires that carry inverted voltage levels. When one wire goes high, the other goes low and vice versa. By running the two wires in close proximity, they encounter similar interference. Hence, subtracting one signal from the other eliminates the analog part, leaving only the original digital signal.

## Low-Speed Serial Interfaces

### **Universal Asynchronous Rx/Tx Serial (UART)**

The term UART stands for universal asynchronous receiver/transmitter, which is also referred to by various names such as serial, RS-232, TTL serial, and UART. In case it supports synchronous operations, it is known as USART.

The reason it is called "Universal" is that it is widely used as a serial interface and can be easily identified using oscilloscopes.

When both wires (RX and TX) in the serial cable are connected, UART is used for receiving and transmitting.

<figure><img src="../../.gitbook/assets/C2F6.png" alt=""><figcaption><p><em>Figure2.6: UART Wiring (Pinout)</em></p></figcaption></figure>

RS-232 is a widely used UART standard for connecting devices that are several meters apart. It defines logic one (known as Mark) as anything between -3 V and -15 V, and logic zero (known as Space) as anything between +3 V and +15 V.

TTL serial, which uses 0 V/5 V logic levels, is similar to RS-232 in format. This means that you can use UART communication without requiring any additional voltage converters. However, some people may specify that they are using 3.3V TTL serial instead of the classic 0 V/5 V logic level.

The UART protocol is simple to understand. When idle, the transmitter continuously sends a logic one (mark). When ready to send data, the transmitter sends a logic zero (start bit) to indicate the beginning of the transmission, followed by the data and optional parity bits for error detection. Finally, the transmitter sends one or more logic one (stop bits) to indicate the end of the transmission. To properly interpret the transmission, the receiver and transmitter must agree on various parameters.

**Baud Rate**: The number of bits per second being transmitted between sender and receiver.

**Byte Length**: The number of bits in a byte (Universally 8, But UART supports different lengths).

**Parity**: N for no parity, E for Even, and O for Odd, Used for error detection measuring whether the total number of ones in the byte is even or odd.

**Stop Bits:** The length of the stop signal bit (often 1, 1.5, or 2).

<figure><img src="../../.gitbook/assets/C2F7 (1).png" alt=""><figcaption><p><em>Figure2.7: 0x71 byte being transmitted over UART @9600/8N1</em></p></figcaption></figure>

For instance, Specifying 9600/8N1 Means that you expect to see 9600 bits per second, 8-bit byte, no parity bit, and one stop bit.

The UART protocol is not only limited to traditional applications, but it is also used in modern communication technologies. For instance, it is employed in some mobile phones and embedded systems that communicate with cellular radios through the Hayes command set. Additionally, various GPS modules use NMEA 0183, which is a text protocol that relies on UART for the data link layer.

### **Serial Peripheral Interface (SPI)**

SPI is a type of serial interface that operates using a low pin count and is typically used between a controller and one or more peripheral devices. Unlike a UART, where both the sender and receiver can transmit and receive data, SPI is a Controller-Peripheral interface where the peripheral devices act as slaves and can only respond to the controller's requests (master).

One of the distinguishing features of SPI is its source-synchronous nature, which means that the controller sends the clock signal to the peripheral devices. Additionally, SPI is generally faster than UART, with SPI clock speeds ranging from 1-100MHz, while UART typically operates at 115.2kHz.

The SPI interface typically uses five pins for communication: SCK (Serial Clock), COPI (Controller Out Peripheral In), CIPO (Controller In Peripheral Out), \*CS (Chip Select), and GND (Ground).

<figure><img src="../../.gitbook/assets/C2F8.png" alt=""><figcaption><p><em>Figure2.8: SPI Wiring (Pinout)</em></p></figcaption></figure>

The Chip Select pin is active-low, meaning that a high voltage is false and 0V is true.

SPI is commonly used to interface with EEPROMs that store firmware for devices such as network routers or USB devices, as well as BIOS and EFI code. It is also well-suited for devices that do not require high-speed or frequent interaction, including environmental sensors, cryptographic modules, and wireless radios.

Some devices may use different names for the pins, such as Serial Data Out (SDO) and Serial Data In (SDI), or MOSI, MISO, and SS, instead of COPI, CIPO, and CS, respectively. However, the protocol typically remains the same.

### **Inter-IC Interface (I2C)**

I2C, also known as IIC, I^2C, Two-Wire (TWI), and SMBus, is a synchronous bus with a low pin-count and the ability to support multiple controllers. While I2C and SPI are similar, it is important to note that I2C is a multi-controller bus, while SPI is a controller-peripheral bus. It is common to find the same devices available with either an SPI or I2C interface.

<figure><img src="../../.gitbook/assets/C2F9.png" alt=""><figcaption><p><em>Figure2.9: I2C communication between controllers and peripherals</em></p></figcaption></figure>

The I2C bus consists of two wires: SDA and SCL. Each wire connects to every SDA or SCL pin of all I2C ports connected to the bus, and each wire has a pull-up resistor. When an I2C device is inactive, both SDA and SCL pins are in high-impedance mode, and both lines sit at logic one. Any device can take ownership of the bus by pulling down the SCL line.

An I2C device can act as a controller only, a peripheral only, or as both at different times. For example, if two controllers are connected to an I2C peripheral EEPROM, they need to check the bus status before accessing the EEPROM. If both lines are sitting at logic one, the bus is inactive and available for use. A controller can take control of the bus by sending a START condition, which involves setting SDA to 0 while SCL stays at 1. Other devices must wait until the controller is done with the bus before using it. The controller signals the end of its transmission by sending a STOP condition, which involves setting SDA back to 1 while SCL stays at 1.

<figure><img src="../../.gitbook/assets/C2F10.png" alt=""><figcaption><p><em>Figure2.10: Stop conditions on I2C SDA and SCL lines</em></p></figcaption></figure>

Each device on the I2C bus is assigned a unique 7-bit address, with some bits being fixed and others programmable through flash or pullup/down resistors. This allows for differentiation between multiple identical components connected to the same I2C bus. Once the address is determined, a Read/Write bit is added to indicate the direction of the next byte. To read data from the EEPROM, the controller must first specify the memory address to read from (a write operation, accomplished by setting the 8th bit to 1), then instruct the EEPROM to send the data at that location (a read operation, accomplished by setting the 8th bit to 0). Following the transfer, the recipient is expected to acknowledge the byte.

<figure><img src="../../.gitbook/assets/C2F111.png" alt=""><figcaption><p><em>Figure2.11: I2C register sequence</em></p></figcaption></figure>

The following table shows a complete sequence between the controller and the EEPROM on the SCA line:

| #  | Name               | Description                                                                                                                                                                                                          |
| -- | ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1  | Start Sequence     | The Controller Takes Control over The bus                                                                                                                                                                            |
| 2  | Peripheral Address | The controller Sends the 7-bit address of EEPROM it wants to read                                                                                                                                                    |
| 3  | R/\*W bit          | Sending a Zero or One for Read or Write respectively (in this case 0 for reading)                                                                                                                                    |
| 4  | Acknowledge        | Releasing SDA and expecting the EEPROM to signal reception of the memory by setting SDA to 0                                                                                                                         |
| 5  | EEPROM Address     | The controller sends the 8-bit EEPROM memory address.                                                                                                                                                                |
| 6  | Acknowledge        | Releasing SDA and expecting the EEPROM to signal reception of the memory by setting SDA to 0                                                                                                                         |
| 7  | Start Sequence     | The Controller Takes Control over The bus                                                                                                                                                                            |
| 8  | Peripheral Address | The controller Sends the 7-bit address of EEPROM it wants to read                                                                                                                                                    |
| 9  | R/\*W bit          | Sending a Zero or One for Read or Write respectively (in this case 1 for writing)                                                                                                                                    |
| 10 | Acknowledge        | Releasing SDA and expecting the EEPROM to signal reception of the memory by setting SDA to 0                                                                                                                         |
| 11 | EEPROM data        | The EEPROM sends the 8 data bits from the memory address to the controller.                                                                                                                                          |
| 12 | Acknowledge        | The controller sets SDA to 0 to ack it has received the byte                                                                                                                                                         |
| 13 | Repeat             | As long as the controller keeps toggling SDA, the EEPROM will send success bytes of data to the controller. When enough bytes are read, the controller will send a NACK (Not Ack) to communicate with the peripheral |
| 14 | Stop Sequence      | Setting both SDA and SCL to 1, leaving the BUS control.                                                                                                                                                              |

Due to its multi-controller capability, it is possible to join an I2C bus and act as a controller, whereas this is not possible with an SPI bus.

### **Secure Digital Input/Output and Embedded Multimedia Cards**

SDIO is a protocol that utilizes the physical and electrical SD card interface for input/output operations. eMMCs, on the other hand, are surface-mount chips that implement the same interface and protocol as many cards, without the need for extra packaging or a socket.

Although SD cards are backward compatible with SPI, with the right connections, it is possible to read and write data on the card. While SD cards and eMMCs were once considered low-speed serial buses, they have evolved over time. However, by using a low-speed SPI sniffer, it is still possible to extract useful information from these devices.

For various cards that still support SPI, the pinout connections for MMC, SD, MiniSD, and MicroSD cards are shown in the table below. Additionally, the locations of the SPI pins for these cards are depicted in the figure provided.

<img src="../../.gitbook/assets/C2F12.png" alt="" data-size="original"><img src="../../.gitbook/assets/C2F12_1.png" alt="" data-size="original">

### **CAN Bus**

The Controller Area Network (CAN Bus) is a bus system commonly utilized in automotive applications to link microcontrollers that communicate with sensors and actuators. In automobiles, the CAN Bus is used to send commands from buttons on the steering wheel to the car stereo, as well as to retrieve real-time engine data and diagnostics. Additionally, the CAN Bus can be used to access engine controls through a compromised cellular connection to the vehicle's microcontroller.

CAN uses differential signaling as it operates in a noisy electrical environment, requiring robustness. Two main types of CAN are high or low-speed fault-tolerant CAN, both using a differential pair of wires known as CAN High and CAN Low. These names correspond to voltage levels for logical one or zero.

High-speed CAN has bit rates from 40Kbps to 1Mbps and uses CAN High = CAN Low = 2.5V for logical one, and CAN High = 3.75V and CAN Low = 1.25V for logical zero. Low-speed CAN has bit rates from 40Kbps to 125Kbps and uses CAN High = 5V and CAN Low = 0V for a logical one, and CAN High ≤ 3.85V and CAN Low ≥1.15V for a logical zero. An updated version of CAN known as Flexible Data-rate (FD) increases the speed up to 12Mbps and enables a maximum of 64 transferred bytes sent in one packet.

### **JTAG**

The Joint Test Action Group (JTAG) is an important hardware debugging interface that is crucial for security purposes. It was created by the IEEE 1149.1 standard to standardize the means of testing and debugging chips as well as testing PCBs for manufacturing errors. Originally used for testing or debugging multilayer PCBs without exposing the inner layers to the outside world, JTAG uses existing chips on the PCB to test the connections.

Boundary scanning is a technique used to ensure that all connections are properly connected. For example, to verify that Chip A pin 6 is connected to Chip B pin 9, you can drive pin 6 low and then high and observe on pin 9 whether that signal actually arrives. By extending this technique to all chips, you can verify the correct manufacturing of the PCB.

JTAG typically employs a range of four to six pins:

| Test Data in (TDI)            | Shifts data into JTAG chain                                     |
| ----------------------------- | --------------------------------------------------------------- |
| Test Data Out (TDO)           | Shifts data out of JTAG chain                                   |
| Test Clock (TCK)              | Clocks all test logic on the JTAG chain                         |
| Test Mode Select (TMS)        | Selects a mode of operation (Boundary Scan, or TAP Operations ) |
| Test Reset (TRST, Optional)   | Resets the test logic (Holding TMS=1 for 5 clock cycles)        |
| System Reset (SRST, Optional) | Resets the entire system                                        |

## High-Speed Serial Interfaces

High-speed serial interfaces have been instrumental in enabling data rate increases in the last decade. The old 40-pin Parallel ATA cables, which operated at a maximum frequency of 133 MHz, have been replaced by 7-pin cables that can run at 6 GHz.

One of the challenges of using parallel wires is that it is essential to ensure that all signals are stable at the receiver end within one clock cycle. This becomes more difficult with increased frequencies, as all wires must have similar physical properties such as length and electrical characteristics. Additionally, parallel wires are prone to "crosstalk," where one wire acts as an antenna and the other wires act as receivers, leading to data errors. In contrast, single wires have less impact than parallel wires, and using differential signaling reduces the impact even further.

It is much more challenging to intercept or inject data on a 6 GHz differential signal than on a 400 KHz single-ended signal. However, this difficulty is relative to cost, as it requires a logic analyzer that costs more than a car.

### **Universal Serial Bus (USB)**

USB set a great example as the first external interface to utilize high-speed differential signaling. If a USB 3.0-supported drive is connected to a USB 2.0-supported host, for instance, both devices establish a connection at the highest common standard. In case of transmission errors, they are automatically reattempted. USB also defines various characteristics, including connector shapes and pinouts, electrical and data protocols, and device classes, along with how to interact with them. For instance, the USB Human Interface Device (HID) specification is utilized for keyboards and mice, enabling the operating system to have a generic plug-and-play driver for all USB keyboards instead of a driver for each manufacturer. However, this feature could be maliciously abused to launch BadUSB attacks.

A USB connection has one host and up to 127 devices, and various bit rates are available, ranging from 12Mbps for USB 1.1 to 20Gbps for USB 3.2. Four wires are used for data rates up to 480Mbps, while five additional wires are introduced for rates above 480Mbps. The nine-wire pinout is described as follows.

| VBUS                       | 5V line used to power a device                                                                                  |
| -------------------------- | --------------------------------------------------------------------------------------------------------------- |
| D+ and D-                  | The differential pair for communication (≤USB2.0)                                                               |
| GND                        | Ground                                                                                                          |
| SSRX+, SSRX-, SSTX+, SSTX- | Two differential pairs, one for reception, and one to transmission. (≥USB3.0)                                   |
| GND\_DRAIN                 | a ground for signal; has less noise than power ground, which may be dealing with much larger currents (≥USB3.0) |

If you count the pins on any Micro-USB connector, you will find that there are five. The fifth pin is the ID pin, which was originally used for USB on-the-go (OTG) and indicates which end is inserted, allowing the device to determine whether its role should be host or peripheral. A grounded ID pin indicates "Host," while a floating one indicates "Peripheral." Furthermore, hidden functionality can be activated through resistance values other than floating or grounded. For instance, by using a Galaxy Nexus and a 150KΩ resistor on the ID pin, USB can be turned off, and TTL UART can be enabled, providing debugging access.

### **PCI Express (PCIe)**

PCI Express represents the high-speed serial evolution of the former PCI bus. Its structure is quite comparable to USB, as both employ high-speed differential pairs, offer backward compatibility, and have well-defined hierarchies and protocols for device enumeration.

PCIe's clock speed begins at 2.5GHz, whereas USB's clock speed starts at 12MHz. PCIe is often closely integrated with the CPU or SoC, granting full access to system memory and all other hardware and devices in the system. If an attacker manages to insert a malicious PCI device into the targeted system, they may potentially gain control over all of the hardware in the entire system.

{% embed url="https://github.com/ufrisk/pcileech" %}

### **Ethernet**

Ethernet was initially standardized in 1983 to establish computer networks, and it provides a wide range of options for physical cables, speeds, and frame types. The most common types of Ethernet used in embedded systems are 100BASE-TX (100Mbps) and 1000BASE-T (1Gbps), both utilizing an 8P8C plug.

The 8P8C plug is connected to a cable that includes four pairs of wires that are twisted. Each pair is used for differential signaling, and the twisting process helps to reduce crosstalk. The primary difference between 100BASE-TX and 1000BASE-T is that 100BASE-TX employs +1 V, 0 V, or -1 V over a single wire pair, whereas 1000BASE-T utilizes -2 V, -1 V, 0 V, +1 V, and +2 V levels across all four wire pairs.
