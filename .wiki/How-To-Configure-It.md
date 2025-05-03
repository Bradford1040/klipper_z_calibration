>:pushpin: **Quick Hint:** Remember, the smaller the switch-offset, the further the
> nozzle is away from the bed! :wink:

Although I wrote at the beginning that you never have to calibrate the z-offset manually
again, you should do it now. This will prevent the calculated z-offset from becoming too
large. So, this plugin acts more like an automatic baby-stepping.

### Preconditions

As a prerequisite, the probe must be properly configured. It must work reliably without any
problems! If you don't know how, **please have a look at the
[KlickyProbe](https://github.com/jlas1/Klicky-Probe) and how to configure it!**
There is also a very good but complex example configuration by
[zellneralex](https://github.com/zellneralex/klipper_config/tree/master).

Now, that the probe is working reliably, this automatic z-offset calibration is basically
configured by adding the `z_calibration` section. All possible properties and it's defaults
are documented here in [configurations](#configurations).

A minimal startup configuration might look like this:

```text
[z_calibration]
#nozzle_xy_position:   <X,Y position for clicking the nozzle on the z endstop - not needed if [safe_z_home] is used>
switch_xy_offsets:    <X,Y offsets from the nozzle position for clicking the probe's switch body on the z endstop>
#bed_xy_position:      <X,Y position for probing the bed, for instance the center point - not needed if mesh with zero_reference_position is used>
switch_offset:        <offset of the switch trigger (read the Switch Offset section!)>
start_gcode:          <macro name for attaching the probe>
#before_switch_gcode: <macro name for attaching the probe AFTER probing the nozzle>
end_gcode:            <macro name for docking the probe>
```

It is good practice to use more than one sample and to use "median" as "probe:samples_result".
And it's **important** to configure an appropriate probe offset in X, Y and **Z**. The
z-offset does not need to be an exact value, since we are not using it as an offset, but it
must be close to a real value!

It doesn't even matter what "stepper_z:position_endstop" value is configured in Klipper.
All positions are relative to this point - only the absolute values are different. However,
it's advisable to configure a safe value here, so that the nozzle does not accidentally crash
into the build plate. The plugin only changes the GCode offset and it's still possible to
move the nozzle beyond this offset.

### Configurations

>:bulb: **NEW:** The nozzle position is set to the "safe_z_home" position by default
> and the switch position can now be defined as an offset from the nozzle position.

The following configuration is required to enable the plugin set some necessary values:

```text
[z_calibration]
nozzle_xy_position: default from home_xy_position of safe_z_home
#   A X, Y coordinate (e.g. 100,100) of the nozzle, clicking on the z endstop.
switch_xy_position: optional when switch_xy_offsets is used
#   A X, Y coordinate (e.g. 100,100) of the probe's switch body, clicking on
#   the z endstop.
switch_xy_offsets: optional when switch_xy_position is used
#   Instead of an absolute position (switch_xy_position), this configuration
#   adds an X, Y offset (e.g. -6,-18) to the nozzle position.
bed_xy_position: default from zero_reference_position (or relative_reference_index) of bed_mesh
#   A X, Y coordinate (e.g. 100,100) where the print surface (e.g. the center
#   point) is probed. These coordinates are adjusted by the
#   probe's X and Y offsets. The default is the zero_reference_position which
#   replaces the deprecated relative_reference_index
#   of the configured bed_mesh, if configured. It's possible to change the zero
#   reference position at runtime or use the GCode argument BED_POSITION of CALIBRATE_Z.
wiggle_xy_offsets: 0,0
#   After probing the nozzle and retracting, move x some distance away and
#   back. Useful to prevent the z endstop pin sticking to the nozzle and
#   being pulled out of the assembly. Can be negative. Defaults to zero to
#   disable it. Can be combined in x and y to move diagonally. Be careful
#   to not move your nozzle out of range!
switch_offset:
#   The trigger point offset of the used mag-probe switch.
#   A larger value will position the nozzle closer to the bed.
#   This must be determined manually. More on this later
#   in this section..
offset_margins: -1.0,1.0
#   The minimum and maximum margins allowed for the calculated offset.
#   If the offset is outside these values, it will stop!
#   The margin can be defined as "min,max" e.g. "-0.5,1.5" or by just one
#   value e.g. "1.0" which translates to "-1.0,1.0" (which is also the default).
max_deviation: DEPRECATED - please use offset_margins instead!
#   The maximum allowed deviation of the calculated offset.
#   If the offset exceeds this value, it will stop!
#   The default is 1.0 mm.
samples: default from "probe:samples" section
#   The number of times to probe each point. The probed z-values
#   will be averaged. The default is from the probe's configuration.
samples_tolerance: default from "probe:samples_tolerance" section
#   The maximum Z distance (in mm) that a sample may differ from other
#   samples. The default is from the probe's configuration.
samples_tolerance_retries: default from "probe:samples_tolerance_retries" section
#   The number of times to retry if a sample is found that exceeds
#   samples_tolerance. The default is from the probe's configuration.
samples_result: default from "probe:samples_result" section
#   The calculation method when sampling more than once - either
#   "median" or "average". The default is from the probe's configuration.
safe_z_height: default is 2 * z_offset from the "probe:z_offset" section
#   The absolute z position in mm to move to before moving to the next
#   position. The default is two times the z_offset from the probe's
#   configuration. The minimum safe z height is 3mm.
clearance: DEPRECATED - please use safe_z_height instead!
#   The distance in mm to move up before moving to the next
#   position. The default is two times the z_offset from the probe's
#   configuration.
position_min: default from "stepper_z:position_min" section.
#   Minimum valid distance (in mm) used for probing move. The
#   default is from the Z rail configuration.
speed: 50
#   The moving speed in X and Y. The default is 50 mm/s.
lift_speed: default from "probe:lift_speed" section
#   Speed (in mm/s) of the Z axis when lifting the probe between
#   samples and clearance moves. The default is from the probe's
#   configuration.
probing_speed: default from "stepper_z:homing_speed" section.
#   The fast probing speed (in mm/s) used, when probing_first_fast
#   is enabled. The default is from the Z rail configuration.
probing_second_speed: default from "stepper_z:second_homing_speed" section.
#   The slower speed (in mm/s) for probing the recorded samples.
#   The default is second_homing_speed of the Z rail configuration.
probing_retract_dist: default from "stepper_z:homing_retract_dist" section.
#   Distance to retract (in mm) before probing the next sample.
#   The default is homing_retract_dist from the Z rail configuration.
#   Caution: if sensorless homing is used on the Z axis with
#   homing_retract_dist set to zero, this must be set to a value greater zero.
probing_first_fast: false
#   If true, the first probing will be faster by the probing speed.
#   This is to get down faster and not record the result as a
#   probing sample. The default is false.
start_gcode:
#   A list of G-Code commands to run before each calibration command.
#   See docs/Command_Templates.md for the G-Code format. This can be used to
#   attach the probe.
before_switch_gcode:
#   A list of G-Code commands to run before to each probing on the
#   mag-probe. See docs/Command_Templates.md for the G-Code format. This can
#   be used to attach the probe after probing on the nozzle and before probing
#   on the mag-probe.
end_gcode:
#   A list of G-Code commands to run after each calibration command.
#   See docs/Command_Templates.md for the G-Code format. This can be used to
#   detach the probe afterwards.
```

### Bed Mesh

>:bulb: **NEW:** Adaptive mesh macros can now be used by redefining the RRI of the mesh
> and/or by using the new "BED_POSITION" parameter of the "CALIBRATE_Z" command.

When using a bed mesh, it is recommended to configure it with a zero reference position
which used to be the relative reference index ("bed_mesh:zero_reference_position" setting).
However, this is no longer enforced. With a configured zero reference position, the mesh
becomes Z=0 at this position. So, it's good to calibrate Z at this point. If there is no
"bed_xy_position", in the configuration, the zero reference position will be read
each time the calibration is started. It's possible to change this position with a macro
at runtime.

This is used by adaptive mesh macros. They create a smaller mesh that is applied only to the
area that will be used by the current print job. An example would be the Frix-x macro which
can be found [here](https://github.com/Frix-x/klippain).

>:point_up: **NOTE:** Be careful with adaptive mesh macros because they use Klipper's mesh function
>in an unsupported way! It's important to calibrate Z correctly for the created mesh!

### Switch Offset

The "z_calibration:switch_offset" is the aforementioned offset from the switch body (which is
the probed position) to the actual trigger point above it. A starting point for this value can
be taken from the datasheet of the Omron (microswitch) switch (D2F-5: 0.5mm and SSG-5H: 0.7mm). It's safe to
start with a little less depending on the squishiest you prefer for the first layer (for me,
it's about 0.46 for the D2F-5). **So, with a smaller offset value, the nozzle is further away from
the bed!** This value cannot be negative.

For example, the datasheet of the D2F-5:

![d2f-example](https://user-images.githubusercontent.com/28778989/221305269-a68a013b-6456-4689-ad25-3f62968951bb.png)

And the calculation of the offset base:

```text
offset base = OP (Operation Position) - switch body height
     0.5 mm = 5.5 mm - 5 mm
```

#### How About A Negative Switch Offset?

First of all, there cannot be a negative switch_offset! If the switch_offset is already
really small after tuning it and the nozzle is still too close to the bed, then something
is wrong with the measurement of the probe body. The following picture illustrates this:

![negative-switch-offset](https://user-images.githubusercontent.com/28778989/221305211-55783c0e-4a54-4e40-9683-93e36ead6bb9.png)

Therefore, please check your endstop, the rod of the endstop and the position of the switch
body touching the endstop!

> **:exclamation: Please, do NOT drive the endstop pin on the switch actuator directly!
> Otherwise, you do it at your own risk and I will reject any request for support!**

If you do so, a correct or at least a working measured height at the switch is all due to
the various forces in the system. But forces can change for many reasons. The best case
is that the actuator is pushed all the way in until the pin touches the body of the
switch - before the endstop is triggered! But it could be anything in between...
So, there is no reason not to touch the body directly in a safe and robust way :thumbsup:

### Probe Dock Position

For example, if the probe is not docked to the gantry, but to the bed, it is important
to have a correct Z-height. So, the docking process may fail after auto-calibration if
the applied offset is too high. However, there is a workaround by setting the GCode offset
back to zero within the attach and dock macros. To make it valid only in the macro, you
can use the save/restore GCode state commands like this:

```text
SAVE_GCODE_STATE NAME=YOUR_MACRO_NAME
SET_GCODE_OFFSET Z=0.0

your macro content here...

RESTORE_GCODE_STATE NAME=YOUR_MACRO_NAME
```

This will save the state including the calibrated offset, reset it to zero, run the
attach/dock routine and restore the calibrated offset.
