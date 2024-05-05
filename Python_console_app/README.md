
# SUP Console Flash Programmer

## _Description of Console Programs and the Communication Protocol with the Programmer_

# Communication Protocol
The communication protocol with the programmer is simple and consists of only a few commands. Each command begins with the `$` symbol, otherwise it will respond with `$ERR\r\n`. If a command is not found, the response will be `$NODATA`. In response, the programmer sends brief responses or data requested by the user.

List of commands:
| Command | Description |
| ------- | ----------- |
| $AUT | "Are you there?" Queries the programmer for its presence. Should respond with `$OK\r\n` |
| $MRT | "Memory Type." If flash is present, it sends back `<7 bytes>\r\n` as the flash identifier |
| $ERS | "Erase." Clears the flash. It’s quite a lengthy process! At the start of execution, it sends `$WAIT\r\n`, at the end, it sends `$OK\r\n`, and in case of an error, it sends `$ERR\r\n` |
| $WRB | "Write block." Writes information to the specified block. The format and possible responses are described below |
| $RDR | "Read Range." Responds with `$OK\r\n` and sends a stream of data read from the specified address range after 1 second. The format and possible responses are described below |
| $RDA | "Read Address." Sends the 16-bit content of a flash cell at the specified address. Least significant byte first |
| $RST | "Reset." Software reset of the flash memory. Should respond with `$OK\r\n` |

Description of the data sending format by the `$WRB` command:
The default data block size is 2048 bytes, with an additional 16 bytes of service data included in the request. The block size is specified in the firmware source code in the `system.c` file under the definition `#define RX0_Buffer_Size 2048 + 16`, where 2048 is the maximum size of data sent at once, and 16 is the size of the service information. If the amount of information is less than the specified block size, the block must be filled with `0xFF` bytes up to the specified size.

Data format:
```sh
$WRB<2 bytes block size><4 bytes start address of the block><Block data><2 bytes CRC16 of the data>
```
Service bytes are written in order of seniority: from most significant to least significant.
After receiving, the programmer can send one of 3 responses:
| Response | Description |
| -------- | ----------- |
| $OK\r\n | All is well. The writing was successful |
| $ERRCRC\r\n | CRC error. The received data is incorrect |
| $WRTCRCERR\r\n | CRC error when reading the block from flash |

If there is a failure in writing, the flash needs to be erased; otherwise, writing will be impossible, and there will be constant checksum errors in data recording.

Description of the data sending format by the `$RDR` command:
The range of possible reading addresses is specified in the firmware source code in the `k5l.c` file under the definitions `#define Flash_address_begin 0x000000` and `#define Flash_address_end 0x7FFFFF`. Each unit of address corresponds to 2 bytes from flash. The least significant byte will come first.

Data format:
```sh
$RDR<4 bytes start address of the reading block><4 bytes end address of the reading block>
```
A similar request for reading one cell:
```sh
$RDA<4 bytes address of the reading block>
```
Service bytes are written in order of seniority: from most significant to least significant.
In response, a sequence of requested data will arrive. `Important!` Checksums are absent.



# Description of the Write Utility
The utility is designed to transfer a file to the programmer for writing. The file will be transferred and written in binary form, byte by byte.

To launch, enter the project directory:
```sh
cd sup_console_programmator
```
Activate the previously installed virtual environment:
```sh
source venv/Scripts/activate
```
Enter the utilities directory:
```sh
cd Python_console_app
```
Start the program:
```sh
python Write.py
```
That's it! Keep an eye on the console messages.

# Configuration of the Write Utility

The file name is specified in the constant `WRITEFILE = 'write.bin'`, where `write.bin` is the name of the file to be written, located in the same directory as the file `Write.py`

The COM port number, which belongs to the Arduino Mega 2560, is specified in the constant `COMPORT = 'COM3'`, where COM3 is the Arduino port

`SendBlockLength = 2048` - This specifies the size of the data block being transmitted to the programmer. It should not exceed the value specified in the `system.c` file of the programmer's firmware source code, under the definition `#define RX0_Buffer_Size 2048 + 16`, where 2048 is the maximum size of data sent at once; 16 is the size of the service information.

`Com_Baudrate = 1000000` - The speed of the COM port, specified in baud. Must match the speed specified in the `usart.c` file of the programmer's firmware source code, under the definition `#define BAUD_0 1000000`. This value is the maximum. In case of data transmission failures, it is recommended to lower the values until reliable data transmission is established.

# Description of the Read Utility
The utility is designed for reading data from flash and writing data to a file. The file will be overwritten with each launch of the utility and written in binary form, byte by byte.

To launch, enter the project directory:
```sh
cd sup_console_programmator
```
Activate the previously installed virtual environment:
```sh
source venv/Scripts/activate
```
Enter the utilities directory:
```sh
cd Python_console_app
```
Start the program:
```sh
python Read.py
```
Keep an eye on the console messages. Unlike Write.py, this utility does not display progress in the console, but only states the facts of events.


# Configuration of the Read Utility

The file name is specified in the constant `READFILE = 'dump.bin'`, where `dump.bin` is the name of the file for writing, located in the same directory as the file `Read.py`

The COM port number, which belongs to the Arduino Mega 2560, is specified in the constant `COMPORT = 'COM3'`, where COM3 is the Arduino port

`COM_BAUDRATE = 1000000` - The speed of the COM port, specified in baud. It must match the speed specified in the `usart.c` file of the programmer's firmware source code, under the definition `#define BAUD_0 1000000`. IMPORTANT! Since there is no check on the correctness of the received data when reading, it is recommended to perform the dump at a low speed.

`FLASH_ADDRESS_BEGIN = 0x00000000` and `FLASH_ADDRESS_END = 0x0000FF00` specify the initial and final address for reading flash cells. IMPORTANT! Flash is read in 16-bit words, hence in the case presented above, 128 kilobytes of data will be read from the flash.
