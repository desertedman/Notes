# GPIO Condensed

<!--toc:start-->

- [GPIO Condensed](#gpio-condensed)
  - [LED on a Pi](#led-on-a-pi)
  - [Pulse-Width Modulation](#pulse-width-modulation)
  - [Analog and Digital Signals](#analog-and-digital-signals)
  - [Peripheral Communication](#peripheral-communication)
    - [Synchronous vs Asynchronous](#synchronous-vs-asynchronous)
    - [Serial vs Parallel Communication](#serial-vs-parallel-communication)
  - [Serial Peripheral Interface SPI](#serial-peripheral-interface-spi)
    - [SPI Specs](#spi-specs)
    - [Clock Polarity and Clock Phase](#clock-polarity-and-clock-phase)
    - [SPI Data Transmission](#spi-data-transmission)
    - [SPI Advantages](#spi-advantages)
    - [SPI Disadvantages](#spi-disadvantages)
  - [Inter-Integrated Circuit I2C](#inter-integrated-circuit-i2c)
    - [I2C Specs](#i2c-specs)
    - [Messages](#messages)
      - [I2C Start Bit](#i2c-start-bit)
      - [I2C Address Frame](#i2c-address-frame)
      - [I2C Read or Write Bit](#i2c-read-or-write-bit)
      - [I2C ACK Bit](#i2c-ack-bit)
      - [Data Frame](#data-frame)
      - [I2C Stop Bit](#i2c-stop-bit)
    - [I2C Data Transmission](#i2c-data-transmission)
    - [I2C Advantages](#i2c-advantages)
    - [I2C Disadvantages](#i2c-disadvantages)
  - [Universal Asynchronous Receiver and Transmitter UART](#universal-asynchronous-receiver-and-transmitter-uart)
    - [UART Specs](#uart-specs)
    - [UART Transmission](#uart-transmission)
      - [Data Packets](#data-packets)
        - [UART Start Bit](#uart-start-bit)
        - [UART Data Frame](#uart-data-frame)
        - [UART Parity](#uart-parity)
        - [UART Stop Bit](#uart-stop-bit)
    - [UART Data Transmission](#uart-data-transmission)
    - [UART Advantages](#uart-advantages)
    - [UART Disadvantages](#uart-disadvantages)
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

## Analog and Digital Signals

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

### Synchronous vs Asynchronous

- Synchronous - Devices share a clock signal
  - SPI, I2C
- Asynchronous - No clock signal used
  - UART

### Serial vs Parallel Communication

- Data (bits) transmitted via parallel or serial
- Parallel - Bits sent all at same time through separate wires
- Serial - Bits sent one by one through single wire

## Serial Peripheral Interface SPI

### SPI Specs

- MOSI (Master Output/Slave Input) - Master -> Slave
- MISO (Master Input/Slave Output) - Master <- Slave
- SCLK (Clock) - Clock signal
- SS/CS (Slave Select/Chip Select) - Master selects slave to send data to

| Specs                  |                           |
| ---------------------- | ------------------------- |
| **Wires Used**         | 4                         |
| **Maximum Speed**      | Up to 10 Mbps             |
| **Synch**              | Synchronous               |
| **Serial or Parallel** | Serial                    |
| **Max # of Masters**   | 1                         |
| **Max # of Slaves**    | Theoretically unlimited\* |

\* In practice, number of slaves limited by load capacitance

- One bit / clock cycle
- Speed determined by frequency of clock
- Comm. is initiated by master, master configures and generates
  clock signal

### Clock Polarity and Clock Phase

- Clock polarity - Bits sampled on either rising or falling edge of cycle
- Clock phase - Sampling occurs on either first or second edge of cycle,
  regardless of rising or falling

### SPI Data Transmission

1. Master outputs clock signal
2. Master drops desired slave's voltage via SS line
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

## Inter-Integrated Circuit I2C

### I2C Specs

- I2C Bus composed of:
  - _Serial Data_ (SDA)
  - _Serial Clock_ (SCL)
- Each bus has I2C master
- Each device has unique address, used as transmitter/receiver to comm. w/
  devices on bus
  - Multiple devices can be on I2C bus

| Specs                  |                       |
| ---------------------- | --------------------- |
| **Wires Used**         | 2                     |
| **Maximum Speed**      | Standard = 100 kbps   |
|                        | Fast = 400 kbps       |
|                        | High speed = 3.4 Mbps |
|                        | Ultra fast = 5 Mbps   |
| **Synch**              | Synchronous           |
| **Serial or Parallel** | Serial                |
| **Max # of Masters**   | Unlimited             |
| **Max # of Slaves**    | 1008                  |

### Messages

- Data transferred in _messages_
  - Broken up into _frames_ of data
- Start -> Address -> R/W -> ACK -> Data -> ACK -> Stop
- Start -> Address -> R/W -> ACK -> Data -> ACK -> Data -> ACK -> Stop (Multiple data)

#### I2C Start Bit

- SDA high -> low _before_ SCL high -> low

#### I2C Address Frame

- 7 or 10 bit sequence that identifies slave
- Address frame always first frame after start bit
  - Corresponding slave sends low voltage ACK to master

#### I2C Read or Write Bit

- Master send data - R/W -> low voltage
- Master request data - R/W -> high voltage

#### I2C ACK Bit

- If address/data frame received, receiver ACK -> sender

#### Data Frame

- Always 8 bits long, MSB first
- Followed by ACK/NACK bit
- Recieved by master/slave, depending on who sent data, before next data frame
  is sent

#### I2C Stop Bit

- SDA low -> high, _after_ SCL low -> high

### I2C Data Transmission

1. Master sends start condition to every slave
2. Master sends each slave address frame, along with R/W bit
3. Each slave compares address; matching slave sends ACK by dropping voltage for one bit
4. Master sends or receives data frame
5. After each data frame, receiver returns ACK to sender
6. To stop transmission, master sends stop condition

### I2C Advantages

- Only two wires
- Multiple masters and slaves
- ACK/NACK gives confirmation of success
- Hardware less complicated than UART
- Well known and widely used

### I2C Disadvantages

- Slower transfer rate than SPI
- Data frame limited to 8 bits
- More complicated hardware than SPI

## Universal Asynchronous Receiver and Transmitter UART

### UART Specs

- Tx (transmission) line
- Rx (reading) line
- Receiving UART detects start bit, reads at a frequency known as _baud_ rate
  - Baud rate - speed data transfer, measured in bits per second (bps)
  - Both UARTs must operate at within ~10% of same baud rate

| Specs                  |                                      |
| ---------------------- | ------------------------------------ |
| **Wires Used**         | 2                                    |
| **Maximum Speed**      | Up to 115200 baud, usually 9600 baud |
| **Synch**              | Asynchronous                         |
| **Serial or Parallel** | Serial                               |
| **Max # of Masters**   | 1                                    |
| **Max # of Slaves**    | 1                                    |

### Data Packets

- Data between UART is delivered in packets
- Start -> Data (Size depends on Parity) -> Parity (Optional) -> Stop (1 or 2 bits)

#### UART Start Bit

- Idle transmission line held at high voltage
- To start transfer, transmitting UART pulls voltage of line high -> low for one cycle
- Receiving UART detects voltage drop, begins reading packet at baud rate

#### UART Data Frame

- Parity bit -> 5 to 8 bits long
- No parity bit -> Can bit 9 bits long
- Data sent LSB first

#### UART Parity

- Receiving UART counts number of "1" bits and checks if it is even/odd
- Parity bit can either be:
  - 0, even parity
  - 1, odd parity
- If mismatch between parity and number of odd/even bits, UART knows data is corrupt

#### UART Stop Bit

- 1 or 2 bits
- Sender Tx low -> high voltage for at least two bit transmissions

### UART Data Transmission

1. Transmitting UART receives data in parallel from data bus
2. Transmitting UART adds start/stop and parity bits to data frame
3. Transmits packet in serial via Tx. Receiver samples data line at baud rate from Rx
4. Receiver discards start/stop and parity bits from data frame
5. Receiver converts serial data back into parallel and transfers to data bus on receiving end

### UART Advantages

- Only two wires
- No clock signal
- Parity bit for error checking
- Structure of data packet can be changes as long as both sides are set up for it
- Well documented and widely used method

### UART Disadvantages

- Max data frame size of 9 bits
- No multiple slave/master systems
- Baud rates must be within 10% of each other
- Relatively slow

## End
