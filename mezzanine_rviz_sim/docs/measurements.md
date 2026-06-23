# Measurements - mezzanine_rviz_sim (Phase 1.1, DXF-driven)

## Origin and units

- Origin = lower-left corner of the tabletop, seen from above.
- `x` = table length (0 to 1.400 m).
- `y` = table width (0 to 0.596 m).
- `z` = height; `z = 0` at the tabletop top surface; negative `z` is below.
- Project unit: meters.
- New source of truth: `EstacionMezzanine.dxf` (mm, `$INSUNITS=4`).
- Conversion: `m = mm * 0.001`; DXF table-origin offset
  `(3.32483, -0.27342) mm` is subtracted before scaling.

## Convention: xyz_m means lower-left-front corner of the box

Every `xyz_m` value in `config/layout_params.yaml` and in each component Xacro
represents the LOWER-LEFT-FRONT CORNER of the component box, in the
`table_origin` frame.

In URDF, the `<origin xyz="...">` of a `<box size="L W H">` puts the geometric
CENTER of the box at that origin. To make the link frame coincide with the
anchor point (corner), each Xacro macro:

1. Sets the joint `<origin xyz="X Y Z">` to the documented corner position.
2. Inside the link, sets the geometry `<origin xyz="${L/2} ${W/2} ${H/2}">` so
   the box extends in +x, +y, +z from the link frame.

### Exceptions

- `table` and `plc_cabinet` use TOP-LEFT-FRONT corner because they extend below
  `z = 0`.
- `cobot_base` uses the CENTER of its circular base (see Phase 1 sign-off).
  xArm 6 mounts centered on this point.

## DXF color → component map (entities found)

| Color (DXF) | RGB | Entity | Component |
|---|---|---|---|
| White | 255,255,255 | LWPOLYLINE rect | Table outline |
| Lime green | 88,186,72 | LWPOLYLINE rect | PLC cabinet (Cabina) |
| Yellow | 255,255,0 | 9 × LWPOLYLINE | Cubes 3×3 |
| Brown-orange | 205,105,40 | 2 × LWPOLYLINE (outer/inner) | Cube base + 5 mm lip |
| Green | 0,255,0 | CIRCLE r=60 mm | Cobot base center |
| Red | 255,0,0 | LWPOLYLINE rect | Whiteboard (flat plate) |
| Blue | 0,0,255 | LWPOLYLINE rect | Conveyor |
| Light blue | 122,175,223 | LWPOLYLINE rect | HMI |
| Magenta | 255,0,255 | LWPOLYLINE rect | Computer workstation footprint |
| Cream | 248,246,176 | 3 × LINE (U-shape) | Photoelectric sensor bracket |
| Orange | 248,153,30 | LWPOLYLINE rect | Camera body footprint (top view) |
| Cyan | 0,255,255 | 4 LINEs + 2 CIRCLEs | Start/E-Stop panel + buttons |

## Measurement table (in meters)

| Component | Size (X × Y × Z) m | Position (x, y, z) m | Anchor | Source / Status |
|---|---|---|---|---|
| Table | 1.400 × 0.596 × 0.026 | (0, 0, top at z=0) | top-left-front | OK |
| Table support base (×2) | 0.070 × 0.576 × 0.030 | left (0.030, 0.010, -0.720) / right (1.300, 0.010, -0.720) | LL | placeholder |
| Table support column | 0.040 × 0.194 × 0.690 | local in support base | — | OK |
| PLC cabinet (Cabina) | 0.700 × 0.250 × 0.500 | (0.050, 0.596, -0.073) | top-left-front | DXF confirms XY; Z retained |
| Cobot base | r = 0.060 (cyl) | (0.240, 0.426, 0) | CENTER | DXF green circle |
| Cube base | 0.140 × 0.140 × 0.020 | (0.020, 0.348, 0) | LL | DXF orange outer 140×140 |
| Cubes (3×3 grid) | 9 × 0.040³ | grid local origin (0.010, 0.010, 0.020) | LL each | DXF yellow 40×40 |
| Whiteboard | 0.303 × 0.227 × 0.020 | (0.109, 0.034, 0) | LL | DXF red; flat plate |
| Conveyor (DOBOT) | 0.700 × 0.215 × 0.060 | (0.385, 0.302, 0) | LL | DXF blue + DOBOT official spec |
| Photoelectric sensor | 0.030 × 0.020 × 0.040 | local in `conveyor_base` (0.217, 0.218, 0.060); world center ~(0.617, 0.530, 0.080) | LL local | DXF cream U-shape; Z TBD |
| Camera profile (column) | 0.040 × 0.040 × 0.720 + arm 0.040 × 0.169 × 0.040 | column LL (0.598, 0.556, 0) | LL | DXF derived; pegada a la cabina |
| Camera body | 0.060 × 0.060 × 0.060 | center (0.618, 0.407, 0.635) | center | DXF orange; Z from Phase 1 |
| HMI base | 0.168 × 0.067 × 0.012 | (0.650, 0.517, 0) | LL | DXF light blue |
| HMI display | 0.214 × 0.041 × 0.158 | on top of base + arm, tilted ~15° | — | retained |
| Start/E-Stop panel | 0.100 × 0.150 × 0.050 | (1.170, 0.040, 0) | LL | DXF cyan rectangle |
| Start button | r = 0.019, h = 0.012 | panel-local (0.050, 0.032), world (1.220, 0.072, 0.050) | center | DXF cyan circle |
| E-Stop button | r = 0.029, h = 0.012 | panel-local (0.050, 0.106), world (1.220, 0.146, 0.050) | center | DXF cyan circle (mushroom) |
| Computer workstation | 0.372 × 0.432 (XY footprint) | (1.137, 0.260, 0) | LL | DXF magenta; extends past table |
| Tower (Lenovo tinker) | 0.11825 × 0.07239 × 0.0861 | back-right of footprint | LL local | primitive; mesh TODO |
| Gripper base | 0.080 × 0.080 × 0.006 | on xArm link6 | — | OK |
| Gripper body | 0.060 × 0.060 × 0.040 (TBD) | on gripper_base | — | placeholder |
| Gripper fingers | thickness 0.008, opening 0.044, height 0.030 (TBD) | on gripper_body | — | placeholder |

## Phase 1 → Phase 1.1 deltas (DXF-driven)

| Component | Phase 1 (m) | Phase 1.1 (m) | Δ |
|---|---|---|---|
| cube_base | (0.110, 0.030) | (0.020, 0.348) | −0.090 X, +0.318 Y |
| cubes | 1 demo cube | 3×3 grid (9 differentiated) | new geometry |
| conveyor pos | (0.500, 0.300) | (0.385, 0.302) | −0.115 X |
| conveyor size | 0.400 × 0.132 × 0.055 | 0.700 × 0.215 × 0.060 | DOBOT official |
| hmi pos | (0.650, 0.425) | (0.650, 0.517) | +0.092 Y |
| hmi base | 0.140 × 0.060 | 0.168 × 0.067 | larger footprint |
| computer pos | (1.250, 0.030) | (1.137, 0.260) | full footprint redesign |
| photo sensor (local) | (0.210, 0.120, 0.050) | (0.217, 0.218, 0.060) | now at back edge |
| camera profile_x | 0.690 | 0.598 | -0.092 |
| camera arm_len | 0.316 | 0.169 | recomputed exact |
| camera body center | (0.710, 0.260) | (0.618, 0.407) | over DXF projection |
| white_board | (0.090, 0.035) | (0.109, 0.034) | +0.019 X |

## Open items (remaining TBD)

1. Photoelectric sensor Z (currently 0.060 m, resting on conveyor top).
2. Camera body Z confirmation (still 0.635 m from Phase 1).
3. HMI display dimensions vs measured Siemens KTP700 Basic.
4. Gripper body and finger dimensions.
5. Tower mesh: swap primitive box for `tinker.obj` when staged under
   `meshes/computer/`.
6. CAD STEP for DOBOT conveyor (not located on official site yet).
