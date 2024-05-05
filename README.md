
# SUP Console Flash Programmer
## _Everything presented here is done at your own risk._

>Note: the original repo can be found [here](https://github.com/Promolife/sup_console_programmator). All credits goes to [promolife](https://github.com/Promolife). I just took it and translated everything from russian to english.

Simple parallel flash memory programmer:

- Built on the readily available Arduino Mega 2560
- Minimal additional components required
- Easy to replicate

## Features

- Erase, read, and write memory chip K5L2731CAA-D770 or similar models
- Monitor data transfer process between the computer and the programmer
- Control the data writing process to Flash
- Light indicators for processes (Writing, Reading, Erasing, Error)
- Ability to define button behaviors (Through firmware source code)
- Can display information about the process on the programmer's screen (Through firmware source code)
- High speed operation: up to 1Mbit/s (Can be higher, but the microcontroller's performance does not allow for increased speed)

## Project Setup

Requires Python version no lower than 3.9.10. All operations described are for Windows OS with GitBash installed.
- Must be performed as an administrator.

Clone the repository from GIT:

```sh
git clone git@github.com:Promolife/sup_console_programmator.git
```
Enter the project directory

```sh
cd sup_console_programmator
```
Install and activate the virtual environment

```sh
python -m venv venv
source venv/Scripts/activate
```

Update pip and install dependencies

```sh
python -m pip install --upgrade pip
pip install pyserial
```

Ready! You can start using it.
