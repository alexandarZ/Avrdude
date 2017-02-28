# Arduino Leonardo - AVRDUDE

Arduino is most used development kit by makers all over the world today. Main IDE for programming them is Arduino IDE but sometimes we need to write our custom made hex file into him. AVRDUDE is a very popular command-line program for programming AVR chips and Arduino IDE uses him for programming Arduino boards.

#### Used items:
* Arduino Leonardo
* USB cable
* WinAVR
* Python
* Python PySerial library
* Windows OS

There exists two options:
  1. Use AVRDUDE from Arduino IDE directory ("Arduino\hardware\tools\avr\bin")
  2. Install WinAVR and use AVRDUDE from their directory

In this test WinAVR is used.

#### Problem statement:
Couldn't program Arduino Leonardo using *AVRDUDE* on *Windows OS*. Every time when we try, we get response from AVRDUDE:  

**'Connecting to programmer: .avrdude: butterfly_recv(): programmer is not responding'**

This happens because Arduino Leonardo doesn't enter bootloader mode.

#### Problem solution:

**First solution** is to press reset button on Leonardo, wait 4,5s and release it. Leonardo will enter bootloader mode after this.

**Second solution** is to enter into bootloader mode by opening and closing __COM N__ port where Leonardo is connected on __1200__ baud rate, wait 5s and then open __COM N+1__ port on __115200__ baud rate. After this Leonardo will be in bootloader mode and ready for flashing.

#### Python script for writing custom hex file into Leonardo:

```Python
import serial, time, subprocess, os

s_port_a      = 'COM4'  # Port where Leonardo is connected                        
s_port_b      = 'COM5'  # Port where will be AVR109 bootloader connected       
hex_file_path = 'D:\Blink.hex' # Path to hex file
avrdude_cmd   = 'avrdude -c avr109 -b 115200 -p atmega32u4 -P '+s_port_b # AVRDUDE command   
write_cmd     = ' -U flash:w:'+hex_file_path # Write command
read_cmd      = ' -U flash:r:'+hex_file_path+":r" # Read command

try:
  print('Entering bootloader mode...')

  ser = serial.Serial(s_port_a,1200)

  # Open serial port
  ser.open()

  #Set handshake
  ser.setDTR(False)
  ser.setRTS(True)

  # Check is port open
  print('Connected: {0}'.format(ser.is_open))
  # Close serial port
  ser.close()

  # Wait board to enter bootloader mode
  time.sleep(5)

  print('Bootloader mode')
  print('Calling AVRDUDE')  

  # Start AVRDUDE as subprocess
  subprocess.Popen(avrdude_cmd+write_cmd,stdout=subprocess.PIPE)

 # Show AVRDUDE results
  avrdude_result = proc.communicate()

  print('AVRDUDE: \n\n')
  print(avrdude_result)

  print('\n\nDone...')

# Serial port error
except serial.SerialException:
  print('Ops! Not valid serial port '+s_port_a+'. Check where is your Arduino connected!')

# Couldn't start AVRDUDE
except subprocess.CalledProcessError
  print('Ops! Could not start AVRDUDE!)

```

This script could be changed to work on Linux/Mac OS also. Serial port and hex file path needs to be changed.
