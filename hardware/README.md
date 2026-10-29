# Hardware

One folder per board **release** from Altium 365, so the repository holds the complete design.
Day-to-day work stays in the lab's Altium 365 workspace: <link to the Altium 365 project>.

For each release, download its data from Altium 365 and add:

```
hardware/
  <board>-v1.0/
    <board>-v1.0-source.zip    # design files (schematics, PCB, project) of this release
    <board>-v1.0-schematic.pdf
    <board>-v1.0-gerbers.zip   # Gerber + drill files, as sent to the manufacturer
    <board>-v1.0-bom.csv
    <board>-v1.0-3d.pdf        # or PNG views
```

Never edit a released folder: any change is a new release. Files over 50 MB (usually 3D models) go in a GitHub release instead.
