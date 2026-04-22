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
If you want to narrow down the scope of your tasks, you can use tags:
```bash
ansible-playbook -i inventory.yml main.yml --tags klipper_cfg
```

---

# Slicer configs
## SuperSlicer
### Artillery Sidewinder X1
> You can find the config in `docs/superslicer/SWX1.ini`

---

# Calibrating a printer

https://ellis3dp.com/Print-Tuning-Guide/articles/index_tuning.html

## 1. [PID Calibration](https://www.klipper3d.org/Config_checks.html#calibrate-pid-settings)
```gcode
PID_CALIBRATE HEATER=extruder TARGET=200
PID_CALIBRATE HEATER=heater_bed TARGET=60
```

## 2. [Bed leveling (manual)](https://www.klipper3d.org/Manual_Level.html)
```gcode
Z_ENDSTOP_CALIBRATE
BED_SCREWS_ADJUST 
```

## 3. [Bed mesh (manual)](https://www.klipper3d.org/Bed_Mesh.html)
First, make sure to pre-heat the bed
In Mainsail, go to "Heightmap", then "Calibrate"

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
# Hold the reset button, and release it right after starting this command:
avrdude -cwiring -patmega2560 -P/dev/ttyUSB0 -D -Uflash:w:out/klipper.elf.hex:i
```
