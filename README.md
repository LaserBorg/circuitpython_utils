# CircuitPython Utilities

PiDAR CircuitPython-Version - CURRENTLY DISCONTINUED:
- CircuitPython optimized LD06 serial/UART driver
- CircuitPython optimized A4988 driver

Other:
- serial connection between host and board
- datarate-test
- BNO055 inertial navigation


## wiring

LD06 port (left to right)
- UART Tx, PWM, GND, 5V

Pico:
- LD06 UART0 Rx: GP1 (Pin2)
- LD06 PWM: GP2 (Pin4)
- LD06 5V: VBUS (Pin40)
- LD06 GND: e.g. Pin38
- A4988 direction: GP15 (pin20)
- A4988 step: GP14 (Pin19)
- A4988 microstepping: GP11, GP12, GP13 (Pin 15,16,17)


### Serial Protocol
baudrate 230400, data bits 8, no parity, 1 stopbit  
sampling frequency 4500, scan frequency 5-13 Hz, distance 2cm - 12 meter, ambient light 30 kLux

total package size: 48 Byte, big endian.
- starting character：Length 1 Byte, fixed value 0x54, means the beginning of data packet;
- Data Length: Length 1 Byte, the first three digits reserved, the last five digits represent the number of measured points in a packet, currently fixed value 12;
- speed：Length 2 Byte, in degrees per second;
- Start angle: Length: 2 Byte; unit: 0.01 degree;
- Data: Length 36 Byte; containing 12 data points with 3 Byte each: 2 Byte distance (unit: 1 mm), 1 Byte luminance. For white objects within 6m, the typical luminance is around 200.
- End Angle: Length: 2 Byte; unit: 0.01 degree；
- Timestamp: Length 2 Bytes in ms, recount if reaching to MAX 30000；
- CRC check: Length 1 Byte

The Angle value of each data point is obtained by linear interpolation of the starting angle and the ending angle.  
The calculation method of the angle is as following:

    step = (end_angle – start_angle)/(len – 1)  
    angle = start_angle + step*i  

len is the length of the packet, and the i value range is [0, len].

