# GPIO

<!--toc:start-->

- [GPIO](#gpio)
  - [LED on a Pi](#led-on-a-pi)
  - [Pulse-Width Modulation](#pulse-width-modulation)
  - [Analog/Digital Signals](#analogdigital-signals)
  - [Peripheral Communication](#peripheral-communication) - [Synchronous vs. Asynchronous](#synchronous-vs-asynchronous) - [Serial vs. Parallel Communication](#serial-vs-parallel-communication) - [Serial Peripheral Interface (SPI)](#serial-peripheral-interface-spi) - [Specs](#specs) - [Clock Polarity and Clock Phase](#clock-polarity-and-clock-phase) - [Slave Select](#slave-select) - [How SPI Works - MOSI and MISO](#how-spi-works-mosi-and-miso) - [Data Transmission](#data-transmission) - [Advantages](#advantages) - [Disadvantages](#disadvantages) - [I2C](#i2c)
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

- Three common communication protocols
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

### Serial Peripheral Interface (SPI)

#### Specs

- MOSI (Master Output/Slave Input) - Master -> Slave
- MISO (Master Input/Slave Output) - Master <- Slave
- SCLK (Clock) - Clock signal
- SS/CS (Slave Select/Chip Select) - Master selects slave to send data to

| Wires Used | Maximum Speed | Synch       | Serial or Parallel? | Max # of Masters | Max # of Slaves           |
| ---------- | ------------- | ----------- | ------------------- | ---------------- | ------------------------- |
| 4          | Up to 10 Mbps | Synchronous | Serial              | 1                | Theoretically unlimited\* |

\* In practice, number of slaves limited by load capacitance

- One bit / clock cycle
- Speed determined by frequency of clock
- Communication is initiated by master, master configures and generates
  clock signal

#### Clock Polarity and Clock Phase

- Clock polarity - Bits sampled on either rising or falling edge of cycle
- Clock phase - Sampling occurs on either first or second edge of cycle,
  regardless of rising or falling

#### Slave Select

- Master selects slave by dropping line's voltage
- Idle, non-transmitting slaves kept at high voltage
- Multiple SS pins -> each slave connected via dedicated wire
- Single SS pin -> SS wire split in parallel to each slave (called _daisy chaining_)
  - All other pins connected via _daisy chaining_

#### How SPI Works - MOSI and MISO

- Data sent serially
- Master -> Slave via MOSI
  - MSB sent first
- Slave -> Master via MISO
  LSB sent first
- Data is sent to and from master and slave in reverse order, so that data is
  formatted the same on both ends

#### Data Transmission

1. Master outputs clock signal
2. Master drops desired slave's voltage
3. Master -> Slave (MSB first) via MOSI, slave reads bits as received
4. Slave -> Master (LSB first) via MISO

#### Advantages

- No start/stop bits, data streamed continuously
- No complicated slave addressing
- Higher data transfer rate than I2C
- Separate MISO/MOSI lines, data sent/received at same time

#### Disadvantages

- Uses four wires (I2C and UARTs use two)
- No received acknowledgement
- No error checking
- Only one master

### I2C
