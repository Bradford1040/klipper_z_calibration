Do not worry too much about the absolute values of the calculated offsets. These can vary greatly.
What matters is the real position from the nozzle to the bed. To check this, the result of the
calibration can be verified by `GET_POSITION`:

```text
> CALIBRATE_Z
> Z-CALIBRATION: probe=3.093 - (switch=2.370 - nozzle=-0.267 + switch_offset=0.466) --> new offset=-0.010000
> GET_POSITION
> mcu: stepper_x:17085 stepper_y:15625 stepper_z:-51454 stepper_z1:-51454 stepper_z2:-51454 stepper_z3:-51454
> stepper: stepper_x:552.500000 stepper_y:-47.500000 stepper_z:10.022500 stepper_z1:10.022500 stepper_z2:10.022500 stepper_z3:10.022500
> kinematic: X:252.500000 Y:300.000000 Z:10.022500
> toolhead: X:252.500000 Y:300.000000 Z:10.021472 E:0.000000
> gcode: X:252.500000 Y:300.000000 Z:9.990000 E:0.000000
> gcode base: X:0.000000 Y:0.000000 Z:-0.010000 E:0.000000
> gcode homing: X:0.000000 Y:0.000000 Z:-0.010000
```

Here, the Z position in "gcode base" reflects the calibrated z-offset.

The offset can then be tested by slowly moving the nozzle to zero in several steps. It's
good to do this by using GCodes, because the offset is applied as GCode-Offset. For example
like this:

```gcode
> G90
> G0 Z5
> G0 Z3
> G0 Z1
> G0 Z0.5
> G0 Z0.3
> G0 Z0.1
```

Check the distance to the print surface after each step. If there is a small discrepancy
(which should be less than the offset base from the switch's datasheet), then adjust
the "z_calibration:switch_offset" by this value. Decreasing the "switch_offset" will move
the nozzle further away from the bed.

Finally, once you have double checked that the calibrated offset is correct, you can use
the "z_calibration:switch_offset" to fine tune the the first layer by actually printing tests.
