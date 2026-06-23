# Measurements - mezzanine_rviz_sim (Phase 1)

## Origin and units

- Origin = lower-left corner of the tabletop, seen from above.
- `x` = table length (0 to 1.400 m).
- `y` = table width (0 to 0.596 m).
- `z` = height; `z = 0` at the tabletop top surface; negative `z` is below.
- Source measurements were in centimeters. ROS uses meters: `m = cm * 0.01`.

## Convention: xyz_m means lower-left-front corner of the box

This was approved with the user. Every `xyz_m` value in
`config/layout_params.yaml` and in each component Xacro represents the
LOWER-LEFT-FRONT CORNER of the component box, in the `table_origin` frame.

In URDF, the `<origin xyz="...">` of a `<box size="L W H">` puts the geometric
CENTER of the box at that origin. To make the link frame coincide with the
anchor point (corner), each Xacro macro:

1. Sets the joint `<origin xyz="X Y Z">` to the documented corner position.
2. Inside the link, sets the geometry `<origin xyz="${L/2} ${W/2} ${H/2}">` so
   the box extends in +x, +y, +z from the link frame.

This keeps measurement values readable and matches the documented origin.

## Measurement table (in meters)

| Component | Size (X x Y x Z) m | Position (x,y,z) m | Status |
|---|---|---|---|
| Table | 1.400 x 0.596 x 0.026 | 0,0,-0.026 (top at z=0) | OK |
| Table support base (x2) | 0.070 x 0.576 x 0.030 | (left, right) | placeholder |
| Table support column | 0.040 x 0.194 x 0.690 | (local in support base) | OK |
| PLC cabinet | 0.700 x 0.250 x 0.500 | 0.050, 0.596, -0.073 | OK |
| Cobot base | n/a (frame only) | 0.240, 0.425, 0.000 | OK |
| Cube | 0.040 x 0.040 x 0.050 | on cube_base | OK |
| Cube base | 0.140 x 0.140 x 0.020 | 0.348, 0.020, 0.000 | OK |
| White board | 0.303 x 0.227 x 0.020 | 0.090, 0.035, 0.000 | OK |
| Conveyor | 0.400 x 0.132 x 0.055 | 0.385, 0.300, 0.000 | length **TBD** |
| Photoelectric sensor | 0.030 x 0.020 x 0.040 (TBD) | abs ~ 0.595, 0.420, 0.050 | **TBD** local/abs |
| Camera profile cross | 0.040 x 0.040 | n/a | OK |
| Camera | 0.060 x 0.060 x 0.060 (TBD) | ~ 0.595, 0.260, 0.635 | **TBD** derived |
| HMI | 0.200 x 0.050 x 0.150 (TBD) | 0.650, 0.425, 0.000 | dims **TBD** |
| Start/E-Stop panel | 0.100 x 0.150 x 0.050 (TBD) | 1.170, 0.040, 0.000 | dims **TBD** |
| Gripper base | 0.080 x 0.080 x 0.006 | on xArm link6 | OK |
| Gripper body | 0.060 x 0.060 x 0.040 (TBD) | on gripper_base | placeholder |
| Gripper fingers | 0.008 m thick, 0.044 m opening, 0.030 m height (TBD) | on gripper_body | placeholder |
| Computer | n/a | TBD ("right side") | **omitted Phase 1** |

## Open items (TBD)

1. Conveyor length: only width (0.132 m) and thickness (0.055 m) are confirmed.
   Placeholder = 0.400 m.
2. Photoelectric sensor position: reported `(21, 12, 5)` cm is assumed local to
   the conveyor; absolute assumed `(0.595, 0.420, 0.050)` m. To confirm.
3. Camera position: derived from sensor position + `y` offset -0.16 m + height
   0.635 m. Placed at `(0.595, 0.260, 0.635)` m. To confirm.
4. HMI, panel of buttons, photoelectric sensor and camera: dimensions are
   placeholders; mark `TBD` until physical measurement.
5. PLC cabinet exact orientation against table back edge.
6. Computer position: omitted from Phase 1.
7. xArm DOF: assumed 6 (xArm 6) per handoff. Top-level Xacro takes `arm_model`
   as arg so changing later does not require restructuring.
8. Y-axis possible inversion against photos: do NOT modify measurements; if the
   render mirrors the layout, only the `table_origin` transform should be
   adjusted, and the change must be documented here.
