# Validation - mezzanine_rviz_sim (Phase 1.1)

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

Expected RViz scene (DXF-driven positions):

- Fixed Frame `world` valid.
- Table 1.400 × 0.596 × 0.026 m visible at z = 0.
- Table supports visible below the tabletop.
- PLC cabinet at the back edge (0.050, 0.596, -0.073), hanging below.
- xArm 6 sitting on `cobot_base` at center (0.240, 0.426, 0).
- Custom gripper attached to xArm's `link6`.
- Conveyor 0.700 × 0.215 × 0.060 m at LL (0.385, 0.302, 0) on top of the tabletop.
- Photoelectric sensor body resting near the back edge of the conveyor;
  world center ~(0.617, 0.530, 0.080).
- Camera profile: column at LL (0.598, 0.556, 0) pegada a la cabina; arm
  extends -y by 0.169 m; camera body centered over (0.618, 0.407, 0.635).
- HMI at LL (0.650, 0.517, 0) sitting between conveyor back edge and PLC cabinet.
- Start/E-Stop panel at LL (1.170, 0.040, 0). Green start (r=0.019) at
  (1.220, 0.072) and red E-Stop mushroom (r=0.029) at (1.220, 0.146).
- Cube base + 9 colored cubes 3×3 at LL (0.020, 0.348, 0).
- White board / flat plate at LL (0.109, 0.034, 0).
- Computer workstation footprint at LL (1.137, 0.260); Lenovo tinker tower
  visible at the back-right of the footprint, monitor centered with stand,
  keyboard + mouse placeholders in front.

## Checklist

- [ ] xacro parses without error.
- [ ] `check_urdf` reports a single root link `world`.
- [ ] RViz opens without "fixed frame" errors.
- [ ] TF tree has no duplicate frames.
- [ ] Table scale matches 1.40 × 0.596 m.
- [ ] xArm meshes resolve via `package://xarm_description/...`.
- [ ] cobot_base position visually matches DXF green circle.
- [ ] Conveyor position visually matches DXF blue rectangle.
- [ ] Camera body is centered over DXF orange rectangle area.
- [ ] HMI sits between conveyor back edge and PLC cabinet front edge.
- [ ] Cube base and 9 colored cubes are upper-left of the cobot.
- [ ] Start/E-Stop panel is on the right-front of the table; buttons match DXF circle radii.
- [ ] PLC cabinet is on the back edge as documented.
- [ ] Gripper is attached to xArm's `link6` (visible at the tool flange).
- [ ] Computer workstation footprint matches DXF magenta rectangle; tower at back-right.
- [ ] All TBD items are explicitly documented and not silently invented.

## TF spot-check

```bash
rosrun tf tf_echo world cobot_base
# expected translation: 0.240, 0.426, 0.000
```

## Known issues / open items

- Photoelectric sensor Z above conveyor: 0.060 m (resting on top); real bracket
  lifts the body higher. To confirm with physical measurement.
- Camera body Z still from Phase 1 (0.635 m); DXF only provides planar XY.
- HMI display geometry is the Phase 1 placeholder (Siemens KTP700 Basic
  approximation). Base footprint now matches DXF.
- Tower is a primitive box with tinker bounding-box dimensions. Replace with
  the actual `tinker.obj` mesh when the asset is staged under
  `meshes/computer/tinker.obj` (scale 0.001 0.001 0.001).
- DOBOT conveyor official STEP/SolidWorks file not yet located.
- Y-axis sign vs photos: visual check expected after this update; if a render
  mirror is observed, patch ONLY the `table_origin` transform and document.
