1. A normal homing of all axes using the z endstop for Z (this is not part of this plugin).
   Now we have a zero point in Z. Everything is now relative to this point. So, a new
   homing would change the calibration because the homing is not as precise. This is one point,
   why absolute offset values (of a calibration result) are not so relevant.
2. Determine the height of the nozzle by touching the tip of the nozzle on the z endstop
   (this can be slightly different to the homed one):
   ![nozzle-position](https://user-images.githubusercontent.com/28778989/221304410-21439073-023c-474f-bcf8-0866a8ee4d4b.png)
3. Determine the height of the mag-probe by probing the switch body at the z-endstop:
   ![switch-position](https://user-images.githubusercontent.com/28778989/221304459-a0b864e3-7af3-46c8-ab76-5535110f7144.png)
4. Calculate the offset between the tip of the nozzle and the trigger point of the
   mag-probe:

   $nozzle switch offset = mag probe height - nozzle height + switch offset$

   ![switch-offset](https://user-images.githubusercontent.com/28778989/221304654-1bbe52f5-72d2-4b41-a6ba-b78dfe3da72c.png)

5. Determine the height of the print surface by probing one point with the mag-probe.
6. Now, calculate the final offset:

   $probe offset = probed height - calculated nozzle switch offset$

7. Finally, the calculated offset is applied by using the `SET_GCODE_OFFSET` command
   (a previous offset is reset first!).

### There is still one Offset

The only disadvantage is, that the trigger point of the mag-probe cannot be probed directly.
Therefore, the switch body is clicked onto the endstop. This small offset between the
switch body and the trigger point can be taken from the data sheet of the switch. The good thing
about this offset is that it is hardly affected by temperature changes or the like. On the other
hand, this is the perfect setting for fine tuning the first layer.

### Interference

Changes in temperature or humidity are not a big deal because the switch is not much
affected by them, and all values are measured in a small time period and only the relations
to each other are used. The nozzle height in step 2 can be determined some time later and even
at a much higher temperature in the printer compared to the homing in step 1. Therefore, the
nozzle is probed again and may vary slightly from the first reference position.

### Example

The output of the calibration with all determined positions looks like this
(the offset result is the value that will be used as the GCode offset):

```text
Z-CALIBRATION: probe=5.602 - (switch=7.366 - nozzle=2.202 + switch_offset=0.440) --> new offset=-0.002750
```

The endstop value is the homed Z position which is always zero or the configure
"stepper_z:position_endstop" setting - and in this case, it's even the same as the
probed nozzle height.

### Thermal Frame Expansion

There is a feature in Klipper to adjust the Z height to the thermal expansion of the
printer frame continuously after starting a print. [Here](https://www.klipper3d.org/Config_Reference.html#z_thermal_adjust)
is the documentation of it.

In the meantime, this approach has even led to a slightly different further development
by JiMcNulty and can be found [here](https://github.com/JiMcNulty/FDC).
