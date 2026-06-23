# Validation - mezzanine_rviz_sim (Phase 1)

## Static parse validation

```bash
cd ~/mezzanine_ws
source devel/setup.bash
xacro src/Mezzanine/mezzanine_rviz_sim/urdf/mezzanine_cell.urdf.xacro > /tmp/mezzanine.urdf
# optional: requires liburdfdom-tools
check_urdf /tmp/mezzanine.urdf
```

Expected:

- `xacro` exits 0 with no error.
- `check_urdf` reports root link `world` and a connected tree (if installed).

## Launch validation

```bash
roslaunch mezzanine_rviz_sim view_mezzanine.launch
```

Expected RViz scene:

- Fixed Frame `world` valid.
- Table 1.400 x 0.596 x 0.026 m visible at z=0.
- Table supports visible below the tabletop.
- PLC cabinet visible at the back edge, hanging below the tabletop top.
- xArm 6 sitting on `cobot_base` at (0.240, 0.425, 0).
- Custom gripper attached to xArm's `link6`.
- Conveyor at (0.385, 0.300, 0) on top of the tabletop.
- Photoelectric sensor on the conveyor.
- Camera profile (vertical aluminum) standing near the conveyor, camera at
  ~0.635 m height.
- HMI placeholder near (0.650, 0.425, 0).
- Start/E-Stop panel placeholder at (1.170, 0.040, 0).
- Cube base + cube_01 at (0.348, 0.020, 0).
- White board at (0.090, 0.035, 0).

## Checklist

- [ ] xacro parses without error.
- [ ] `check_urdf` reports a single root link `world` (when liburdfdom-tools installed).
- [ ] RViz opens without "fixed frame" errors.
- [ ] TF tree has no duplicate frames.
- [ ] Table scale matches 1.40 x 0.596 m.
- [ ] xArm meshes resolve via `package://xarm_description/...`.
- [ ] cobot_base position visually matches measurement.
- [ ] Conveyor position visually matches measurement.
- [ ] Camera is above the photoelectric sensor.
- [ ] HMI is near the conveyor.
- [ ] Cubes and cube_base are in the documented corner.
- [ ] Start/E-Stop panel is on the right edge of the table.
- [ ] PLC cabinet is on the back edge as documented.
- [ ] Gripper is attached to xArm's `link6` (visible at the tool flange).
- [ ] All TBD items are explicitly documented and not silently invented.

## Known issues / TBD to revisit in Phase 2

- Conveyor length placeholder (0.400 m).
- Photoelectric sensor and camera positions are derived; physical confirmation
  needed.
- HMI and buttons-panel dimensions are placeholders.
- Computer is omitted; needs position confirmation.
- Y-axis sign vs photos to be validated by visual comparison; if inverted,
  patch only the `table_origin` transform and update this document.
- Validation in a fully headless environment (no display): RViz launch must be
  exercised on a machine with `DISPLAY` or via VNC.
