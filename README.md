### RetroHat ###

A Raspberry Pi HAT for connecting retro joysticks and buttons.


#### Programming the flash ####

First check that WP jumper on the back is NOT bridged and execute the following
commands to enable EEPROM flashing:

    sudo bash -c 'echo "dtparam=i2c_vc=on" >> /boot/config.txt'
    sudo reboot

After system has rebooted, install and compile tools:

    git clone https://github.com/raspberrypi/hats.git
    cd hats/eepromutils/
    make clean ; make

clean the EEPROM:

    dd if=/dev/zero ibs=1k count=4 of=blank.eep
    sudo ./eepflash.sh -w -f=blank.eep -t=24c32

create EEPROM file:

    dtc -@ -b 0 -I dts -O dtb -o retrohat.dtbo retrohat.dts
    ./eepmake retrohat.txt retrohat.eep retrohat.dtbo

and flash the EEPROM:

    sudo ./eepflash.sh -w -f=retrohat.eep -t=24c32
    sudo reboot

Now you can solder-bridge WP jumper on your board and disable EEPROM I2C:

    sudo bash -c 'sed -i "/^dtparam=i2c_vc=on$/d" /boot/config.txt'
    sudo reboot

To verify operation succeeded, use raspi-gpio (expected INPUT for all pins):

    sudo raspi-gpio get
    sudo cat /sys/kernel/debug/gpio

After reboot, keys will be automatically detected. To verify workings you can
use evtest:

    evtest


---

*[www.medo64.com](https://www.medo64.com/)*
