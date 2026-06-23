# Validation - mezzanine_rviz_sim (Phase 1.2)

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

Expected RViz scene (DXF-driven positions in table_origin frame; world = floor):

- Fixed Frame `world` valid; grid renders at world z = 0 (floor).
- `table_origin` is elevated at world z = 0.746 m (= support base 0.030 +
  column 0.690 + tabletop 0.026).
- Table 1.400 × 0.596 × 0.026 m visible with its top at world z = 0.746.
- Table supports touch the floor: support bases at world z = 0,
  columns rising to just below the tabletop bottom (world z = 0.720).
- PLC cabinet hanging from the back edge of the table; cabinet top at
  world z = 0.673 (table_origin z = -0.073), bottom at world z = 0.173.
- xArm 6 sitting on `cobot_base` centered at world (0.240, 0.426, 0.746).
- Custom gripper attached to xArm's `link6`. Aluminum tones:
  base = aluminum_dark, body = aluminum_light, fingers = brushed_aluminum.
- xArm shown in the home pose (joint5 ~1.5, joint3 ~-1.2, joint2 ~-0.6).
- Conveyor 0.700 × 0.215 × 0.060 m at table_origin LL (0.385, 0.302, 0).
- Photoelectric sensor body resting near the back edge of the conveyor.
- Camera support: parented to `plc_cabinet_camera_mount` on the PLC cabinet
  top surface (world (0.598, 0.596, 0.673)). Column rises +Z 0.768 m, arm
  extends -y by 0.209 m, camera body centered at world (0.618, 0.407, 1.381).
- HMI at table_origin LL (0.650, 0.517, 0) between conveyor back edge and
  PLC cabinet front face.
- Start/E-Stop panel at table_origin LL (1.170, 0.040, 0). Green start
  (r=0.019) at world (1.220, 0.072, 0.796) and red E-Stop mushroom (r=0.029)
  at world (1.220, 0.146, 0.796).
- Cube base + 9 colored cubes 3×3 at table_origin LL (0.020, 0.348, 0).
- White board / flat plate at table_origin LL (0.109, 0.034, 0).
- Computer workstation footprint at table_origin LL (1.137, 0.260, 0).

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

## TF spot-check (Phase 1.2)

```bash
rosrun tf tf_echo world table_origin
# expected translation: 0.000, 0.000, 0.746
rosrun tf tf_echo world cobot_base
# expected translation: 0.240, 0.426, 0.746
rosrun tf tf_echo world camera_frame
# expected translation: 0.618, 0.407, 1.381
rosrun tf tf_echo world plc_cabinet_camera_mount
# expected translation: 0.598, 0.596, 0.673
```

## Home pose (xArm 6)

`view_mezzanine.launch` publishes initial joint states via the `zeros`
rosparam. Values used:

```
joint1 = 0.0     joint2 = -0.6    joint3 = -1.2
joint4 = 0.0     joint5 = 1.5     joint6 = 0.0
```

These produce a semi-extended arm pointing toward the work area with the
gripper looking down. Adjust via the joint_state_publisher_gui sliders to
explore alternative poses.

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
