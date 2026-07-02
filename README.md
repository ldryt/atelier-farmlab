# FarmLAB

This repository contains all the configurations you need to manage the farm.

## Using Ansible
### SSH config
This Ansible configuration make use of the ssh config file to connect to the pi.
Make sure this block is present in your `~/.ssh/config`
```
Host farmlab
    HostName 10.69.16.19
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
> You can find the config in `docs/slicer_configs`

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
> You can find pictures of the wirings in `docs/wirings`
## How to dump existing firmware
### Wanhao Duplicator 12/300 (MKS Robin Nano v1.2)
Not possible :)) (amazing STM32 Readout Protection)
### Artillery Sidewinder X1 (MKS Gen L v1.0)
Hold the reset button, and release it right after starting this command:
```bash
avrdude -patmega2560 -cwiring -P /dev/ttyUSB0 -F -Uflash:r:dump.hex:i
```
> You can find the stock firmware in `docs/firmware/sidewinder_x1/original.hex`

## How to flash klipper firmware
### Wanhao Duplicator 12/300 (MKS Robin Nano v1.2)
```bash
cd /home/pi/klipper
make menuconfig # Config reference in roles/klipper/templates/wanhao_duplicator_12_300.cfg.j2
make
./scripts/update_mks_robin.py out/klipper.bin out/Robin_nano35.bin
# Copy to sd card, insert it in the printer, and power it on to flash
```

### Artillery Sidewinder X1 (MKS Gen L v1.0)
```bash
cd /home/pi/klipper
make menuconfig # Config reference in roles/klipper/templates/artillery_sidewinder_x1.cfg.j2
make
# Hold the reset button, and release it right after starting this command:
avrdude -cwiring -patmega2560 -P/dev/sidewinder_XXX -D -Uflash:w:out/klipper.elf.hex:i
```
