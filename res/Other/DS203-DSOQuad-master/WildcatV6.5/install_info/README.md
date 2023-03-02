## INSTALLATION PROCEDURE:

----

**1]** Put DS203 into DFU (device firmware update) mode:
- Turn DS203 off
- Press ">||" button
- and while holding this button down, turn DS203 on

----

**2]** Connect DS203 to PC (using USB cable). New removable USB drive should appear on your PC (we'll refer to it as to "USB drive" later)

----

**3a]** Easy way  
Just copy the *app1.hex* file to USB drive. After copying ends, the USB drive should disappear briefly and reappear again and the extension of *app1.hex* will be changed to either *.RDY* (update successful) or *.ERR* (update NOT successful).
- If you get *.RDY*, then just turn off DS203 and that's it (at next power on, you'll boot to updated app)  
- If you get *.ERR*, try next step 3b]

----

**3b]** The hard way  
(skip to ***"OK! Now we're ready to update"*** if you're not interrested in technical details)  
<br />
**Explanation:**  
Sometimes / on newer devices with newer DFU version (V3.46C), the 3a] step will not work
- on Windows it will stop in the middle of copying, reporting that it cannot access the drive
- on Linux it will finish copying, but *.ERR* extension will appear

I suspect that this is because this app is too big (for smaller apps the 3a] is working ok - tested).  
<br />
Fortunately, there's another way how to update - copying raw binary *.BIN* files to USB drive. But in binary files, there is no information to which address / memory location to copy that raw data. So the actual procedure in this case is in reality to copy *.ADR* file first and then *.BIN* file.  
<br />
*.ADR* files are plain text files, containing only one text line - 32-bit address in C-like hexadecimal format.  
(example of *.ADR* file):

    0x0800C000

By doing this, we're telling to DFU that next *.BIN* file that will be copied should be written starting at that address.  
So we just need to convert our *app1.hex* to *app1.bin* file, right?

    objcopy --input-target=ihex --output-target=binary app1.hex app1.bin

Well .. not so fast. If we do this, we'll overwrite other important data in flash memory! This is because the program code in *app1.hex* is not continuous. It's divided into 3 chunks that are on different locations in memory. By doing one *.BIN*, the gaps between program chunks will be stuffed by zeroes.  
So what we need to do is to somehow analyze the *.HEX* file and separate it into smaller *.HEX* files where in each of them, the program data are continuous. Fortunately, *.HEX* are (more or less) human readable - see IHEX format for details (https://en.wikipedia.org/wiki/Intel_HEX).  
For this W6.5 version the *app1.hex* was splitted into these 3 *.HEX* files:

    app1_a_addr_0800C000.hex
    app1_b_addr_0802C000.hex
    app1_c_addr_08047000.hex 

(I've done this manually in text editor. There's probably a way how to do similar thing using ARM GNU toolchain tools, but this was faster for me).  
Then we'll convert to *.BIN* files:

    objcopy --input-target=ihex --output-target=binary app1_addr_0800C000.hex app1_a.bin
    objcopy --input-target=ihex --output-target=binary app1_addr_0802C000.hex app1_b.bin
    objcopy --input-target=ihex --output-target=binary app1_addr_08047000.hex app1_c.bin

And manually create *.ADR* files with proper addresses:

    app1_a.adr  <-- containing text "0x0800C000"
    app1_b.adr  <-- containing text "0x0802C000"
    app1_c.adr  <-- containing text "0x08047000"

**OK! Now we're ready to update.  
=> Copy these files to DS203's USB drive (while DS203 is in DFU mode) _in this order_:**  
(see directory ***/app1hex_to_bin*** where I left this files already prepared for you)

    app1_a.adr
    app1_a.bin
    app1_b.adr
    app1_b.bin
    app1_c.adr
    app1_c.bin

When you're done, just turn DS203 off.  
<br />
Tested on:  
- DS203 purchased on Dec-01 2017 via eBay
- HW ver. 2.82
- DFU: V3.46C
- SYS: ver. 1.64
- original APP when purchased: ver. 1.13
