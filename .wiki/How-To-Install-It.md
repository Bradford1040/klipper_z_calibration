## Automatic Installation

To install this plugin, you just need to clone this repo and run the `install.sh` script:

```bash
cd ~
git clone https://github.com/Bradford1040/klipper_z_calibration.git
./klipper_z_calibration/install.sh
```

And you are done!

If the Klipper installation is not in `~/klipper` or the `moonraker.conf` not in
`~/printer_data/config/`, then they must be specified with the install script:

```bash
./klipper_z_calibration/install.sh -k /my-home/my-klipper-directory -m /my-home/my-config/moonraker.conf
```

If you are using multiple instances of Klipper, use the `-n` option for the number of instances
used:

```bash
./klipper_z_calibration/install.sh -n 2
```

It is safe to run the install script multiple times.

This installer will link the `z_calibration.py` from this directory into the Klipper extras folder,
adds this repository to the update manager of Moonraker and finally restarts Moonraker and Klipper.

## Manual Installation

For a manual installation, copy the `z_calibration.py` from this repo into the `klippy/extras`
folder of Klipper. Like this:

```bash
/home/pi/klipper/klippy/extras/z_calibration.py
```

And restart Klipper (not Firmware restart!) in Mainsail/Fluidd or by:

```bash
sudo systemctl restart klipper
```

## Moonraker Update Manager

>:bulb: **NEW:** The automatic installation with the install script does this step for you now!

It's possible to keep this extension up to date with the Moonraker's update manager by
adding this configuration block to the "moonraker.conf" file:

```text
[update_manager z_calibration]
type: git_repo
path: ~/klipper_z_calibration
origin: https://github.com/Bradford1040/klipper_z_calibration.git
managed_services: klipper
```

This requires that you clone this repository into your home directory (e.g. /home/pi):

```bash
git clone https://github.com/Bradford1040/klipper_z_calibration.git
```

And link the plugin into Klipper:

```bash
ln -sf ~/klipper_z_calibration/z_calibration.py ~/klipper/klippy/extras/z_calibration.py
```

>:point_up: **NOTE:** If you are upgrading from a version prior to 0.9.1, please run
> the install script again.

## Uninstalling

To uninstall this plugin run:

```bash
~/klipper_z_calibration/install.sh -u
```

It will remove the plugin from Klipper.

You need to remove the `[z_calibration]` configuration from Klipper, the
`[update_manager z_calibration]` block from the `moonraker.conf` and delete
the cloned repository:

```bash
rm -r ~/klipper_z_calibration
```
