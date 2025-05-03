- With a z endstop where the tip of the nozzle hits the endstop switch (like the one in
  the Voron V1 or V2), you can change nozzles without adjusting the offset:
  ![endstop-offset](https://user-images.githubusercontent.com/28778989/221303355-56e262dd-b4c5-4671-868f-c63aca2b605b.png)
- Or, by using a magnetic probe (or SuperPinda, but this does not directly probe the surface
  and therefore needs a different offset, which is not as constant as that of a mechanical
  switch). Configured as a z endstop, you can change the flex plates without adjusting the
  offset: ![probe-offset](https://user-images.githubusercontent.com/28778989/221303519-47f76396-1db3-44f9-8892-d0e77e3997fd.png)
- But, why can't you have both? Or even more.. ?

And this is what I did with this plugin. I simply combined these two probing methods to be
completely independent of any offset calibrations - forever. This is so amazing! :tada:
