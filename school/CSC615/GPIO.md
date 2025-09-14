# GPIO

<!--toc:start-->

- [GPIO](#gpio)
  - [LED on a Pi](#led-on-a-pi)
  - [Pulse-Width Modulation](#pulse-width-modulation)
  - [Analog/Digital Signals](#analogdigital-signals)
  - [Peripheral Communication](#peripheral-communication)
    - [Synchronous vs. Asynchronous](#synchronous-vs-asynchronous)
    - [Serial vs. Parallel Communication](#serial-vs-parallel-communication)
  - [Serial Peripheral Interface (SPI)](#serial-peripheral-interface-spi)
    - [SPI Specs](#spi-specs)
    - [Clock Polarity and Clock Phase](#clock-polarity-and-clock-phase)
    - [Slave Select](#slave-select)
    - [How SPI Works - MOSI and MISO](#how-spi-works-mosi-and-miso)
    - [SPI Data Transmission](#spi-data-transmission)
    - [SPI Advantages](#spi-advantages)
    - [SPI Disadvantages](#spi-disadvantages)
  - [Inter-Integrated Circuit (I2C)](#inter-integrated-circuit-i2c)
    - [Basics](#basics)
    - [Messages](#messages)
      - [Address Frame](#address-frame)
      - [Data Frame](#data-frame)
    - [I2C Specs](#i2c-specs)
    - [I2C Data Transmission](#i2c-data-transmission)
    - [I2C Advantages](#i2c-advantages)
    - [I2C Disadvantages](#i2c-disadvantages)
  - [End](#end)
  <!--toc:end-->

## LED on a Pi

- Simple LED circuit consists of LED and resistor
  - Resistor limits current being drawn; called _current limiting resistor_
  - Use Ohm's law to find resistor value
    - $R_{omega}=\frac{V}{I}$
    - Ex. LED VF = 1.7V, IF = 20mA, output voltage = 3.3V
      - $R_{omega}=\frac{V}{I}=\frac{3.3-VF}{IF}=\frac{3.3-1.7}{20mA}=80ohm$

## Pulse-Width Modulation

- Period - Time for one full cycle of graph (e.g. sine wave)
- Frequency - Reciprocal of period; cycles per second
  - Measured in Hz
- Digital signals cannot vary voltage; instead vary time high vs low
  - Pulse Width - Time of high level output
  - Duty Cycle - Percentage of pulse width, out of entire period
  - Effective voltage = volts \* duty cycle

## Analog/Digital Signals

- ADC - Analog to Digital Converter
- DAC - Digital to Analog Converter
- Represent analog values as $0$ to $2^n$
  - $n$ is number of bits
- Samples at $x$ Hz (frequency)
- Ex. 3.3V and $2^8$ (or 256) bit resolution
  - $3.3V/256$ possible output voltages; 0 = off @ 0V and 255 = on @ 3.3V

## Peripheral Communication

- Three common comm. protocols
  - Serial Peripheral Interface (SPI)
  - Inter-Integrated Circuit (I2C)
  - Universal Asynchronous Receiver/Transmitter (UART)

### Synchronous vs. Asynchronous

- Synchronous - Devices share a clock signal
  - SPI, I2C
- Asynchronous - No clock signal used
  - UART

### Serial vs. Parallel Communication

- Data (bits) transmitted via parallel or serial
- Parallel - Bits sent all at same time through separate wires
- Serial - Bits sent one by one through single wire

## Serial Peripheral Interface (SPI)

### SPI Specs

- MOSI (Master Output/Slave Input) - Master -> Slave
- MISO (Master Input/Slave Output) - Master <- Slave
- SCLK (Clock) - Clock signal
- SS/CS (Slave Select/Chip Select) - Master selects slave to send data to

| Specs              |                           |
| ------------------ | ------------------------- |
| Wires Used         | 4                         |
| Maximum Speed      | Up to 10 Mbps             |
| Synch              | Synchronous               |
| Serial or Parallel | Serial                    |
| Max # of Masters   | 1                         |
| Max # of Slaves    | Theoretically unlimited\* |

\* In practice, number of slaves limited by load capacitance

- One bit / clock cycle
- Speed determined by frequency of clock
- Comm. is initiated by master, master configures and generates
  clock signal

### Clock Polarity and Clock Phase

- Clock polarity - Bits sampled on either rising or falling edge of cycle
- Clock phase - Sampling occurs on either first or second edge of cycle,
  regardless of rising or falling

### Slave Select

- Master selects slave by dropping line's voltage
- Idle, non-transmitting slaves kept at high voltage
- Multiple SS pins -> each slave connected via dedicated wire
- Single SS pin -> SS wire split in parallel to each slave (called _daisy chaining_)
  - All other pins connected via _daisy chaining_

### How SPI Works - MOSI and MISO

- Data sent serially
- Master -> Slave via MOSI
  - MSB sent first
- Slave -> Master via MISO
  LSB sent first
- Data is sent to and from master and slave in reverse order, so that data is
  formatted the same on both ends

### SPI Data Transmission

1. Master outputs clock signal
2. Master drops desired slave's voltage
3. Master -> Slave (MSB first) via MOSI, slave reads bits as received
4. Slave -> Master (LSB first) via MISO

### SPI Advantages

- No start/stop bits, data streamed continuously
- No complicated slave addressing
- Higher data transfer rate than I2C
- Separate MISO/MOSI lines, data sent/received at same time

### SPI Disadvantages

- Uses four wires (I2C and UARTs use two)
- No received ack.
- No error checking
- Only one master

## Inter-Integrated Circuit (I2C)

### Basics

- Two-wire serial comm.
- Must be connected to _serial data_ (SDA) and _serial clock_ (SCL) lines
  (called I2C bus)
- Each bus has I2C master w/ SDA and SCL lines, connected via dedicated pins
- Each device has unique address, used as transmitter/receiver to comm. w/
  devices on bus
  - Multiple devices can be on I2C bus

### Messages

- Data transferred in _messages_
  - Broken up into _frames_ of data
- Each message has:
  - Address frame (binary address of slave)
    - 7 or 10 bit sequence that identifies slave
  - One or more data frames
    - In between each frame:
      - Read/write bits
        - Specifies whether master sends (low voltage) or requests (high
          voltage) data
      - ACK/NACK bits
        - If address/data frame received, receiver sends ACK -> back to sender
  - Start/stop conditions
    - Start condition
      - SDA switches high to low voltage _before_ SCL switches high to low
    - Stop condition
      - SDA switches low to high voltage _after_ SCL switches low to high

#### Address Frame

- Address frame always first frame after start bit
  - Corresponding slave sends low voltage ACK to master
  - R/W bit follows address frame
    - Master send data - R/W -> low voltage
    - Master request data - R/W -> high voltage
- Address frame -> R/W bit -> ACK bit

#### Data Frame

- Always 8 bits long, MSB first
- Followed by ACK/NACK bit
- Recieved by master/slave, depending on who sent data, before next data frame
  is sent
- When finished, master sends stop condition to slave
  - SCL: low -> high, _then_ SDA: low -> high

### I2C Specs

| Specs              |                       |
| ------------------ | --------------------- |
| Wires Used         | 2                     |
| Maximum Speed      | Standard = 100 kbps   |
|                    | Fast = 400 kbps       |
|                    | High speed = 3.4 Mbps |
|                    | Ultra fast = 5 Mbps   |
| Synch              | Synchronous           |
| Serial or Parallel | Serial                |
| Max # of Masters   | Unlimited             |
| Max # of Slaves    | 1008                  |

### I2C Data Transmission

1. Master sends start condition to every slave via switching SDA high -> low _before_ SCL high -> low
2. Master sends each slave address frame, along with R/W bit
3. Each slave compares address; matching slave sends ACK by dropping voltage for one bit
4. Master sends or receives data frame
5. After each data frame, receiver returns ACK to sender
6. To stop transmission, master sends stop condition

### I2C Advantages

### I2C Disadvantages

## End
