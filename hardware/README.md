# Hardware

Hardware **releases** only. Design sources stay in the lab's Altium 365 workspace: <link to the Altium 365 project>.

For each board revision, export the outputs from Altium and add one folder:

```
hardware/
  <board>-v1.0/
    <board>-v1.0-schematic.pdf
    <board>-v1.0-gerbers.zip   # Gerber + drill files, as sent to the manufacturer
    <board>-v1.0-bom.csv
    <board>-v1.0-3d.pdf        # or PNG views
```

Never edit a released folder: any change is a new revision.
