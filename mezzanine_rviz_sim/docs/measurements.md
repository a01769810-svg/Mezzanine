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

### Exception: cobot_base anchor is the CENTER of the circular base

The cobot is the only component whose documented `xyz_m` represents the
CENTER of its (circular metallic) base, NOT a corner. The xArm 6 mounts
centered on this point.

In the URDF this is already handled correctly: the `cobot_base` link frame
is placed at the documented `(0.240, 0.425, 0.000)` m, the puck cylinder
is centered on the link's z-axis, and the xArm attaches at `attach_xyz="0 0 0.010"`.
So the xArm rises centered over the documented xy position.

Confirmed by the user (Phase 1 sign-off). Other components remain
lower-left-front corner anchored.

## Measurement table (in meters)

| Component | Size (X x Y x Z) m | Position (x,y,z) m | Status |
|---|---|---|---|
| Table | 1.400 x 0.596 x 0.026 | 0,0,-0.026 (top at z=0) | OK |
| Table support base (x2) | 0.070 x 0.576 x 0.030 | (left, right) | placeholder |
| Table support column | 0.040 x 0.194 x 0.690 | (local in support base) | OK |
| PLC cabinet | 0.700 x 0.250 x 0.500 | 0.050, 0.596, -0.073 | OK |
| Cobot base | n/a (frame only) | 0.240, 0.425, 0.000 | OK |
| Cube | 0.040 x 0.040 x 0.050 | on cube_base | OK |
| Cube base | 0.140 x 0.140 x 0.020 | 0.110, 0.030, 0.000 | revised (was 0.348, 0.020) - left of cobot per user feedback |
| White board | 0.303 x 0.227 x 0.020 | 0.090, 0.035, 0.000 | OK |
| Conveyor | 0.400 x 0.132 x 0.055 | 0.500, 0.300, 0.000 | revised (was x=0.385) - shifted right per user feedback; length **TBD** |
| Photoelectric sensor | 0.030 x 0.020 x 0.040 (TBD) | abs ~ 0.595, 0.420, 0.050 | **TBD** local/abs |
| Camera profile cross | 0.040 x 0.040 | n/a | OK |
| Camera | 0.060 x 0.060 x 0.060 (TBD) | ~ 0.710, 0.260, 0.635 | derived from new conveyor x; column at back (pegado a la cabina) |
| Camera profile (column) | 0.040 x 0.040 x 0.720 + arm 0.040 x 0.316 x 0.040 | 0.690, 0.556, 0.000 | revised: pegado a la cabina per user feedback |
| HMI (Siemens KTP700 7") | base 0.140x0.060x0.012, display 0.214x0.041x0.158 tilted ~15deg | 0.650, 0.425, 0.000 | redesigned with base+arm+tilted display+screen face |
| Start/E-Stop panel | 0.100 x 0.150 x 0.050 (TBD) | 1.170, 0.040, 0.000 | dims **TBD** |
| Gripper base | 0.080 x 0.080 x 0.006 | on xArm link6 | OK |
| Gripper body | 0.060 x 0.060 x 0.040 (TBD) | on gripper_base | placeholder |
| Gripper fingers | 0.008 m thick, 0.044 m opening, 0.030 m height (TBD) | on gripper_body | placeholder |
| Computer | tower 0.090x0.230x0.380 + monitor 0.450x0.030x0.260 | 1.250, 0.030, 0.000 | placeholder per user feedback (right side) |

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
