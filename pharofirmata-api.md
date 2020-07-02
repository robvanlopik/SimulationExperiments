# Pharo Firmata API


## Introduction

Firmata consists of two parts. It consists of two parts:
1. A protocol for serial communication between a host (in our case a Pharo image) and a Arduino-type microcontroller (that wee will call Arduino from here on);
2. A program (sketch) running on the Arduino. This can be Standard Firmata. But also a specialized version with only the functionalities needed (see Configurable Firmata)

This software concerns controlling a Arduino from Pharo through a serial port (or USB, or Bluetooth).

### Basic concepts.

The communication is asynchronous. A command from the host initiates action on the Arduino side, but the response is returned asynchronously. The Firmata package listens for answers and stores the results internally, so the user can retrieve them at will. In some cases Firmata will *announce* the availability of data. The user program can subscribe to these announcements.

Changes in digital inputs are immediately transmitted to the host. Other input (analog or from I2C devices) are sampled at a certain rate and then reported. By default the sampling interval is 19 ms, but this can be increased.

### Threads
Because we don’t want to lose data from the Arduino, reading the serial port is done in a separate thread that runs at priority lowIOPriority (60). It is in this thread that announcement originate, but because processing these may take too much time, announcements are made from another thread, at priority userInterruptPriority (50).

### Thread-safety

In principle Firmata is not thread-safe. The user will have to implement his own locking. We ensure however that command sequences (sysex command) sent to the Arduino will not be intermixed. So, as long as different threads use different parts of the protocol (e.g. analog in, i2c, servo) there should be no problem.

### Pitfalls

Before starting Firmata, the Arduino has to have been freshly initialized. It should not be active on tis erial output port (see LEDs). Also make sure to enter the correct baud rate (default 57600, but can be changed in the sketch).

## The API

### Initialisation

```aduino := Firmata onPort:’COM3’ baudRate: 57600.```

Of course you can also use a Unix device name.




