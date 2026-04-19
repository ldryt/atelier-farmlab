# FarmLAB

This repository contains all the configurations you need to manage the farm.

## Using Ansible
### SSH config
This Ansible configuration make use of the ssh config file to connect to the pi.
Make sure this block is present in your `~/.ssh/config`
```
Host farmlab
    HostName farmlab.local
    User pi
    IdentityFile "~/.ssh/farmlab_ssh_key"
```
Make sure you can ssh to the pi: `ssh farmlab`

### Deploy it!
Whether you want to set up a fresh pi for farmlab, update its system, or change a printer's config, all you need is to run the main playbook:
```bash
ansible-playbook -i inventory.yml main.yml
```

---

# Slicer configs
## SuperSlicer
### Artillery Sidewinder X1
> You can find the config in `docs/superslicer/SWX1.ini`

---

# Flashing firmware
## Artillery Sidewinder X1
### How to dump existing SWX1 firmware
Hold the reset button, and release it right after starting this command:
```bash
avrdude -patmega2560 -cwiring -P /dev/ttyUSB0 -F -Uflash:r:dump.hex:i
```
> You can find the stock firmware in `docs/firmware/sidewinder_x1/original.hex`

### How to flash klipper firmware on SWX1
```bash
cd /home/pi/klipper
make menuconfig
make
avrdude -cwiring -patmega2560 -P/dev/ttyUSB0 -D -Uflash:w:out/klipper.elf.hex:i
```
