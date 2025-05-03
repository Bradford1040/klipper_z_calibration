- **v1.1.2** (2024-08-01)
  - Fix compatibility with Klipper not reading the zero_reference_position from the bed mesh.
- **v1.1.1** (2024-07-30)
  - (Error) messages revised
- **v1.1.0** (2024-06-16)
  - Bugfix for not reading the safe_z_home position
  - Add new gcode parameter for the switch position
  - Add new gcode parameter for the switch offset
- **v1.0.3** (2024-06-10)
  - Compatibility with Klipper restored
  - Add new gcode parameter for the nozzle position
  - Use probe session to indicate multiple probes
- **v1.0.2** (2023-06-28)
  - Add support for the new `zero_reference_position` of the mesh configuration
    which replaces the `zero_reference_index`.
  - Deprecate `clearance` and `max_deviation` properties (as introduced in version 1.0.0).
- **v1.0.1** (2023-05-08)
  - Fix a parsing error when only one value was defined for offset_margins
  - Remove the `max_deviation` default value to be able to configure the `offset_margins`.
- **v1.0.0** (2023-04-25)
  - The documentation can now be found in the wiki.
  - Split movement from dock to endstop into separate x and y moves.
  - Add option to wiggle after nozzle probing (thanks to [poernahi](https://github.com/poernahi)).
  - The "nozzle_xy_position" is now optional and defaults to "safe_z_home" position
    (inspired by [whi-tw](https://github.com/whi-tw)).
  - The "switch_xy_position" is now also optional and can be replaced by the new
    "switch_xy_offsets" parameter, which adds the offsets to the nozzle position instead.
  - The deprecated separated x and y parameters for the positions are now removed.
  - The offset result is now output with the underlying formula.
  - Likewise, a new z position_endstop is output based on the current offset. This can be used
    as a reference point for adjusting the configuration.
  - The `clearance` is deprecated and is replaced by the `safe_z_height` due to the change in
    behavior. The `safe_z_home` is now an absolute z position to move to.
  - The `max_deviation` is deprecated and is replaced by the `offset_margins` parameter which
    takes one value like the `max_deviation` or two values as `min,max`.
  - Add new Command `CALCULATE_SWITCH_OFFSET` for a manual estimation of the switch-offset.
  - The installation script can now add the update manager section to Moonraker and has an
    uninstall option.

  **:exclamation:ACTION NEEDED:**
  - Move from `clearance` to `safe_z_home`
  - Move from `max_deviation` to `offset_margins` and fine tune the values.
  - Change to combined position parameters if still separated parameters for x and y are used.
- **v0.9.2** (2022-07-08)
  - The probe is now docked again before throwing an error when end_gcode is used (thanks to top-gun).
  - Probings are now more consistent because the probing sequence is also used for bed probing.
  - Safer: the check for an attached probe is now performed after movement to the probing position.
- **v0.9.1** (2022-04-06)
  - The dummy system service for Moonraker's update manager is removed. :tada:
  - **:exclamation:Please update this plugin and Moonraker, edit your moonraker.conf and run
    the install.sh script again!**
- **v0.9.0** (2022-04-05)
  - **CAUTION:** The X and Y position configurations are now merged into a combined X,Y parameter,
  - **:exclamation:Please update your Configuration!**
  - The CALIBRATE_Z command has an optional parameter for the probing position on the bed.
  - Removed restriction on relative reference index in "bed_mesh" section.
  - Some other small changes from the failed Klipper PR are now merged back in.
  - Example configurations are removed because they were just examples and confused many people.
- **v0.8.1** (2022-02-21)
  - Now, the relative reference index (RRI) of the bed mesh is read each time the calibration.
    is performed. So, feel free to use any adaptive mesh macro you like. :tada:
  - Checks for homed axes and attached probe just before use.
  - A new Z-Tilt macro in the examples.
  - Documentation improvements for installation, configuration and switch_offset.
- **v0.8.0** (2021-08-09)
  - New configurations for executing G-Code commands (useful for Voron V1 users).
  - Bugfix for configuring the z_calibration too early (many thanks to Frix-x).
  - New example configurations.
  - **Action needed** for the Moonraker update, see: [Moonraker Update Manager](https://github.com/Bradford1040/klipper_z_calibration/wiki/How-To-Install-It#moonraker-update-manager)
- **v0.7.0** (2021-06-23)
  - New "PROBE_Z_ACCURACY" command.
  - Rename the dummy service (**CAUTION**: the configuration needs to be adjusted for this!).
  - Fix in "_SET_ACC" Macro.
- **v0.6.2** (2021-06-01)
  - Added Moonraker Update capability as requested.
- **v0.5** (2021-05-30)
  - Added compatibility with newer versions of Klipper.
- **v0.4** (2021-05-17)
  - The "calibrate_z:probe_bed_x|y" settings can be omitted in the configuration and the
  "mesh:relative_reference_index" of the bed mesh is used by default instead.
- **v0.3** (2021-05-13)
  - Added a new option to quickly probe down before recording probing samples.
  - And all indirect properties from other sections can now be customized.
- **v0.2** (2021-05-12)
  - Probing repeatability is now dramatically improved by using the probing
    procedure instead of the homing procedure!
