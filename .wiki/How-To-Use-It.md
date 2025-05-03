>:point_up: **NOTE:** If you calibrate Z within a `SAVE_GCODE_STATE` and `RESTORE_GCODE_STATE`,
> the calibrated offset will be lost after running `RESTORE_GCODE_STATE`!

### Command CALIBRATE_Z

>:bulb: **NEW:** The configured position for probing on the bed can now be overridden by
> using the new "BED_POSITION" parameter.

The z-offset calibration is started by:

```text
CALIBRATE_Z [NOZZLE_POSITION=<X mm, Y mm>] [SWITCH_POSITION=<X mm, Y mm>] [BED_POSITION=<X mm, Y mm>] [SWITCH_OFFSET=<offset>]
```

The optional nozzle, switch and bed position parameter can be used to define a position by gcode or
to use/test a different value. There are three ways in which the positions can be set (from highest to lowest priority):

| Priority | nozzle position                  | switch position                  | bed position                    |
|---------:|----------------------------------|----------------------------------|---------------------------------|
|   __1.__ | parameter NOZZLE_POSITION        | parameter SWITCH_POSITION        | parameter BED_POSITION          |
|   __2.__ | configuration nozzle_xy_position | configuration switch_xy_position | configuration bed_xy_position   |
|   __3.__ | home_xy_position of safe_z_home  | configuration switch_xy_offsets  | zero_reference_position of mesh |

A position can be omitted from the configuration by using the corresponding parameter instead.

The SWITCH_OFFSET parameter can also be used to use a value by gcode or to test a different
offset without the need to change the configuration and restart the Klipper firmware.

If the probe is not attached to the printhead, calibration will be aborted (if a normally closed
configuration is used). So, macros can help here to attach and detach the probe like this:

```gcode
[gcode_macro CALIBRATE_Z]
rename_existing: BASE_CALIBRATE_Z
gcode:
    {% set nozzle_position = params.NOZZLE_POSITION %}
    {% set switch_position = params.SWITCH_POSITION %}
    {% set bed_position = params.BED_POSITION %}
    {% set switch_offset = params.SWITCH_OFFSET %}
    G28                         # can also be a conditional homing macro
    M117 Z-Calibration..
    ATTACH_PROBE                # a macro for fetching the probe first
    BASE_CALIBRATE_Z NOZZLE_POSITION={nozzle_position} SWITCH_POSITION={switch_position} BED_POSITION={bed_position} SWITCH_OFFSET={switch_offset}
    DETACH_PROBE                # and parking it afterwards (or DOCK_PROBE in klicky macros)
    M117
```

>:bulb: **INFO:** Meanwhile, it's possible to use the "start_gcode" or "before_switch_gcode"
> and the "end_gcode" instead of rewriting the `CALIBRATE_Z` command!

Then the `CALIBRATE_Z` command needs to be added to the `PRINT_START` macro.

**:exclamation: And remove any z-offset adjustments here (like `SET_GCODE_OFFSET`) :exclamation:**

The print start sequence might look like this:

1. Home all axes
2. Heat up the bed and nozzle (and chamber)
3. Get probe, make QGL or Z-Tilt, park probe if needed
4. Purge and clean the nozzle if available
5. (Get probe), CALIBRATE_Z, (park probe)
6. (Adjust z-offset if needed (e.g. textured plates))
7. create/load mesh if used
8. Print intro line
9. Start printing...

For textured plates, it might be necessary to adjust the offset to be closer to the bed.
This can be done from the slicers start GCode by adding a parameter to the `PRINT_START` macro
**after** the Z calibration:

```text
# Adjust the G-Code z-offset if needed
SET_GCODE_OFFSET Z_ADJUST={params.Z_ADJUST|default(0.0)|float} MOVE=1
```

Then, you can use `PRINT_START Z_ADJUST=0.0` in your slicer. This does **not** reset the
offset set by the calibration but adjusts it by the given value!

>:point_up: **NOTE:** Do not home Z again after performing this calibration or it will
> need to be performed again!

Now, I wish you happy printing with a perfect first layer every time - no matter what you have
just modded on your printhead or bed, or what nozzle and flex plate you plan to use for your
next project. It's just perfect :smiley:

#### Resetting the Calibration

The calibration can be reset by executing this GCode command:

```text
SET_GCODE_OFFSET Z=0.0
```

> **NOTE:** Running the `CALIBRATE_Z` command again will also reset the GCode offset before
> reapplying the new one. So there is no need to add this to the `PRINT_START` macro.

#### Conditional Calibration

If the calibration is only required once, the last successful calibration can be checked as follows:

```text
{% if printer.z_calibration.last_query != True %}
  CALIBRATE_Z
{% endif %}
```

#### The Z Position Endstop

The `CALIBRATE_Z` command also prints an adjusted position endstop value for the Z axis according
to the offset just determined. This value can be used to adjust the `position_endstop` value in
the configuration. This ensures that larger offsets do not occur.

### Command PROBE_Z_ACCURACY

There is also a PROBE_Z_ACCURACY command to test the accuracy of the z endstop:

```text
PROBE_Z_ACCURACY [NOZZLE_POSITION=<X mm, Y mm>] [PROBE_SPEED=<mm/s>] [LIFT_SPEED=<mm/s>] [SAMPLES=<count>] [SAMPLE_RETRACT_DIST=<mm>]
```

It calculates the maximum, minimum, average, median and standard deviation of multiple probe samples
at the endstop by taking the configured nozzle position at the endstop. The optional parameters default
to their corresponding settings in the z_calibration config section.

### Command CALCULATE_SWITCH_OFFSET

This command is intended to help you find the correct switch-offset by manually
calibrating the Z offset.

```text
CALCULATE_SWITCH_OFFSET [SWITCH_OFFSET=<offset>]
```

1. Set the `switch_offset` to any value greater 0.0 (for example: 0.5)
2. Restart the Klipper firmware
3. Run a `CALIBRATE_Z`
4. Without the probe, move the nozzle to the center of the bed and perform a manual Z offset
   calibration by moving the nozzle down, step by step, until it barely touches the print surface.
5. Then, run the `CALCULATE_SWITCH_OFFSET`
6. If everything is correct, it will print an estimated switch offset. Set this given value as
   `switch_offset` in the configuration and test it.
7. After verifying the switch offset, you can fine tune the the first layer by actually printing tests.

If the calculated switch-offset would be negative, a warning will be printed. This means that the
nozzle is still too far away from the surface or something else is wrong with the setup.

It may still be necessary to fine-tune the switch-offset afterwards to get a perfect first layer.

>:pushpin: **Note:** Again, the smaller the switch-offset, the further the
> nozzle is away from the bed!
