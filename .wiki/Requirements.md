- A z endstop where the tip of the nozzle drives on a switch (like the standard
  Voron V1/V2 endstop). **It does not work with the virtual pin of the probe as an endstop!**
  It is not necessary to have a Voron printer, but this type of endstop.
- A magnetic switch based probe at the print head - instead of the standard inductive probe
  (e.g. [this one from Annex](https://github.com/Annex-Engineering/Quickdraw_Probe),
  or the popular drop in replacement [KlickyProbe](https://github.com/jlas1/Klicky-Probe)
  with many mounting options)
- Both, the z endstop and the mag-probe are configured correctly and homing (and QGL/Z-Tilt) are working.
- The probe has to be configured as normally normally closed contact. Then, macros can detect
  if the probe is properly attached/released. Additionally, the plugin checks just before
  every probing if the mag-probe is still attached to the print head - otherwise it will stop!
- (My previous Klipper macro to compensate for the temperature dependent expansion of the
  z endstop rod is **not** needed anymore.)
