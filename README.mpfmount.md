# mpfmount - for mpfshell
originally 2016-03-25, sw@kaltpost.de
updated by sethcohn+mpfmount@gmail.com

A FUSE based mounter for ESP8266 
[Micropython](https://github.com/micropython/micropython) based devices.

This FUSE integration support for mpfshell allows you to mount an
SP8266 based Micropython device into the file system. 
It offers basic operations like read, write, delete and rename, 
as well as some special functions (all provided tough the filesystem).

## Requirements

General:

* ESP8266 board running latest [Micropython](https://github.com/micropython/micropython)
or CircuitPython by Adafruit

For the shell:


Additionally for the FuseMount:

* OS supporting fuse (Linux, Mac OS)
* The fusepy library >= 2.0 (sudo pip install fusepy)
  
__Note__: The tools only work if the REPL is accessible on the device!

## Installing

To install this tool execute the following:

    sudo pip install pyserial
    sudo pip install colorama
    sudo pip install fusepy
    sudo python setup.py install
    
## FUSE Mount Usage

Mount device on port to "$HOME/mp":

use one of these formats:
            ser:/dev/ttyUSB1,<baudrate>
            tn:192.168.1.101,<login>,<passwd>
            ws:192.168.1.102,<passwd>

    test -d $HOME/mp || mkdir $HOME/mp
    mpfmount -p ser:/dev/ttyUSB1 -m $HOME/mp
    cd $HOME/mp
      
Now, work with the files as if they were normal files. When done
editing, creating etc, you have to commit your changes back to the MP board.
    
Commit a single file (e.g. "boot.py"):

    echo "boot.py" > $HOME/mnt/.commit

Commit all changed files:

    echo "*" > $HOME/mp/.commit

Or, as a short-cut for the above, jsut execute the ".commit" file:
    ./.commit  [this might not work now]

__Note__: Changes are not committed before un-mounting will be lost!


[this part isn't working yet]
While the MP board is FUSE mounted, the serial line (to the REPL) is
occupied. To overcome this problem, there is an other special file
in the mounted FS called ".release". This file could be used to 
make the FUSE mounter release the serial line to allow terminal access,
and later reattach the line (while the serial line is released,
no commits to the MP board are possible).
 
Relase the serial line, access REPL with terminal (miniterm.py from pyserial):

    echo "1" > .release
    miniterm.py -p /dev/ttyUSB0 -b 115200

When done with the terminal, reattach the serial line:

    echo "0" > .release

For the above three commands, there is an other shortcut called ".terminal".
If executed, it will release the serial line, connect miniterm to the REPL, and
when minierm is ended by the user reattaches the serial line:

    ./.terminal

[not working at this time]
