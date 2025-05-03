Ooze with any nozzle probe endstop can cause inaccuracies, as the filament will continues to leak or
deform over the space of multiple probes. It is highly recommended that some measures be taken to
prevent ooze before the nozzle probe portion of this plugin.

A slow long retraction, up to 15mm at the end of the print, can reduce the potential for ooze. If
you do this, consider adding a similar extrusion as the last command in your print start sequence
to bring the plastic back to the tip. (Retracts longer than 5mm have been associated with clogging
in many hotends, especially the Rapido. This should be considered a last resort, depending on the
exact hardware and filament).

Heating the nozzle for approximately one minute before scrubbing - using a purge bucket - will allow
any remaining plastic to drain from the nozzle and be removed with a simple wipe. When using a
purge and scrubbing bucket, do not purge the filament at this time.

An endstop switch that requires a stronger activation force, such as spring-loaded sexbolt, or
unklicky z, can help squash any remaining ooze and improve consistency.

Probing can be done at a hotend temperature below the full print temperature. If you are printing
at 250, you can preheat the nozzle to 180 and run the calibration before heating up to full
temperature. This can have different effects depending on the temperatures used.

Also consider picking up your probe before you wipe the nozzle, so that the calibration can probe
the nozzle immediately after cleaning.
