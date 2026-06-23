# RViz Mezzanine Simulation Skill

Use this skill whenever working on the Mezzanine RViz/ROS simulation, industrial digital twin layouts, URDF/Xacro files, meshes, launch files, TF frames, joint states, cobot integration, conveyors, sensors, cameras, HMI panels, grippers, or plant-level visualization.

## Core rule

Do not start coding immediately.

Before making changes, first analyze:

1. Existing simulation projects in this repository.
2. Previous RViz/ROS architecture.
3. URDF/Xacro structure.
4. Launch files.
5. Mesh organization.
6. TF and joint-state flow.
7. Existing scripts or nodes.
8. The Mezzanine reference photos.
9. The Mezzanine measurements and positions.
10. The official xArm ROS repository if the cobot is involved.

Always preserve the existing project style unless there is a clear reason to improve it.

## Required first step

Before modifying files, search the repository for:

- `urdf/`
- `xacro/`
- `meshes/`
- `launch/`
- `rviz/`
- `config/`
- `scripts/`
- `nodes/`
- `package.xml`
- `CMakeLists.txt`
- `robot_state_publisher`
- `joint_state_publisher`
- `tf`
- `tf_static`
- `gazebo`
- `moveit`
- `xarm`
- `cobot`
- `conveyor`
- `camera`
- `hmi`
- `gripper`
- `digital twin`
- `scada`

Then produce a diagnosis before implementation.

## Diagnosis required before coding

Before writing code, provide:

1. Summary of previous simulations.
2. Main files and folders used by previous simulations.
3. How the simulations were launched.
4. Which URDF/Xacro files were used.
5. Which meshes were used.
6. Which RViz configs were used.
7. Which topics and TF frames were used.
8. Which parts were purely visual.
9. Which parts had movement or logic.
10. What can be reused.
11. What should not be reused.
12. Risks, missing files, broken paths, duplicated frames, or bad structure.
13. Proposed architecture for the Mezzanine simulation.

Do not implement until the user approves the plan.

## Mezzanine reference package

When working on the Mezzanine simulation, review:

```txt
reference/mezzanine_photos_measurements/
```

Important folders:

* `00_medidas_y_layout/`
* `01_estacion/`
* `02_cubos_y_base/`
* `03_camara_y_cabina/`
* `04_hmi_conveyor_botones/`
* `05_soportes_de_mesa/`
* `06_gripper/`

The main measurement file is:

```txt
reference/mezzanine_photos_measurements/00_medidas_y_layout/mezzanine_medidas_posiciones.md
```

Also review:

```txt
reference/mezzanine_photos_measurements/00_medidas_y_layout/component_dimensions_cm.csv
reference/mezzanine_photos_measurements/00_medidas_y_layout/component_positions_cm.csv
reference/mezzanine_photos_measurements/00_medidas_y_layout/layout_ros_params_m.yaml
```

Measurements are more important than photos. Photos are visual references only.

## Coordinate system

Use this coordinate convention unless the user explicitly changes it:

* Origin: lower-left corner of the tabletop.
* `x`: table length.
* `y`: table width.
* `z`: height.
* `z = 0`: tabletop surface.
* Negative `z`: below tabletop.
* Source measurements are in centimeters.
* ROS/RViz files must use meters.

Conversion:

```txt
meters = centimeters * 0.01
```

Do not change the origin silently. Any coordinate transform change must be documented.

## Main Mezzanine components

The simulation should keep these components separate:

* table
* table supports
* PLC cabinet / control cabinet
* cobot base
* xArm cobot
* custom pneumatic gripper
* conveyor
* photoelectric sensor
* camera
* aluminum camera profile
* HMI
* Start button
* Emergency Stop button
* cubes
* cube base
* white board / work plate
* computer
* simple cable references when useful

Prefer separate Xacro files for each major component.

## xArm integration rule

For the cobot, check the official repository:

```txt
https://github.com/xArm-Developer/xarm_ros
```

Before modeling the cobot manually, investigate whether the project can use:

* `xarm_description`
* official meshes
* official URDF/Xacro
* xArm launch files
* simulation examples

Default assumption for this project:

* Use xArm 6.
* Use the official xArm ROS model if viable.
* Do not recreate the cobot using primitive boxes.
* Do not duplicate official xArm meshes unnecessarily.
* Do not use MoveIt or Gazebo in the first phase unless the user explicitly asks.

Acceptable integration options:

1. Use `xarm_ros` as an external dependency.
2. Add it as a git submodule.
3. Reference `xarm_description`.
4. Create only an adapter Xacro to mount the robot into the Mezzanine table.
5. Use a simplified placeholder only as a temporary fallback, clearly documented.

## Gripper rule

The gripper is custom.

Do not use the official UFACTORY gripper as the main model.

Model a custom pneumatic two-finger gripper using the user-provided measurements:

* Working area/opening: 4.4 cm.
* Total width: 6 cm.
* Finger thickness: 0.8 cm.
* Gripper base: 8 cm x 8 cm.
* Base thickness: 0.6 cm.

Mount it to the xArm tool flange / end-effector frame.

## URDF/Xacro rules

Use clean and readable Xacro.

Rules:

* Use meters.
* Keep names consistent.
* Keep components modular.
* Avoid duplicated frame names.
* Avoid hardcoded unexplained offsets.
* Put repeated values in properties or YAML.
* Use simple geometry for custom components first.
* Add visual detail only after the layout is validated.
* Keep collision geometry simpler than visual geometry.
* Use clear material names.
* Document all assumptions.

Recommended frame names:

```txt
world
table_origin
table_top
table_support_left
table_support_right
plc_cabinet
cobot_base
xarm_base_link
gripper_tool
conveyor_base
photoelectric_sensor
camera_profile
camera_frame
hmi_panel
start_button_panel
start_button
emergency_stop_button
cube_base
cube_01
white_board
computer
```

## Recommended future package structure

When the user approves implementation, create a new ROS1/catkin package similar to:

```txt
mezzanine_rviz_sim/
├── package.xml
├── CMakeLists.txt
├── README.md
├── launch/
│   └── view_mezzanine.launch
├── urdf/
│   ├── mezzanine_cell.urdf.xacro
│   ├── materials.xacro
│   ├── table.xacro
│   ├── table_supports.xacro
│   ├── plc_cabinet.xacro
│   ├── conveyor.xacro
│   ├── photoelectric_sensor.xacro
│   ├── camera_frame.xacro
│   ├── hmi.xacro
│   ├── buttons_panel.xacro
│   ├── cubes_and_base.xacro
│   ├── white_board.xacro
│   └── gripper_adapter.xacro
├── meshes/
│   ├── custom/
│   └── README.md
├── rviz/
│   └── mezzanine.rviz
├── config/
│   └── layout_params.yaml
├── scripts/
│   └── joint_state_simulator.py
└── docs/
    ├── architecture.md
    ├── measurements.md
    └── validation.md
```

Adapt this to the existing repository style if needed.

## Implementation order after approval

When approved to implement, follow this order:

1. Create documentation files first.
2. Create or update `layout_params.yaml`.
3. Create table and table origin.
4. Add table supports.
5. Add PLC cabinet.
6. Add cobot base.
7. Integrate xArm 6 official model or placeholder with clear note.
8. Add custom gripper adapter.
9. Add conveyor.
10. Add photoelectric sensor.
11. Add camera profile and camera.
12. Add HMI.
13. Add Start and Emergency Stop buttons.
14. Add cube base and cubes.
15. Add white board.
16. Add computer placeholder if position is not exact.
17. Create launch file.
18. Create RViz config.
19. Validate TF, scale, and visual alignment.
20. Document remaining TBD items.

## Validation checklist

After changes, verify:

* Xacro parses correctly.
* URDF is generated without errors.
* RViz opens.
* Fixed frame is valid.
* TF tree is connected.
* No duplicated frame names.
* Mesh paths resolve.
* Table scale is correct.
* Cobot base is at the measured position.
* Conveyor is at the measured position.
* Camera is above the photoelectric sensor.
* HMI is near the conveyor.
* Cubes and cube base are in the measured location.
* Start and Emergency Stop panel is in the measured location.
* PLC cabinet is below or at the front edge as measured.
* Gripper is attached to the cobot tool frame.
* Missing dimensions are marked as `TBD`.
* Any assumptions are documented.

## Things not to do

Do not:

* Delete previous simulations.
* Move old files without approval.
* Rewrite the entire project from scratch.
* Invent missing measurements.
* Change the coordinate origin without documenting it.
* Duplicate official xArm meshes unnecessarily.
* Hardcode random offsets.
* Break existing launch files.
* Use unclear frame names.
* Mix visual-only components with control logic without documenting it.
* Implement before giving the user a diagnosis and plan.

## Phase 1.x conventions (current source of truth as of 2026-06-23)

### Source of truth for XY positions

`reference/dxf/EstacionMezzanine.dxf` is the canonical layout (mm,
`$INSUNITS=4`). DXF entities are color-mapped to components — see
`mezzanine_rviz_sim/docs/measurements.md` for the table. Conversion:
`m = (mm - dxf_table_origin_mm) * 0.001` with table-origin offset
`(3.32483, -0.27342) mm`. The DXF only provides planar XY; Z values come
from physical measurements or component specs (table top z=0 plane in
the `table_origin` frame).

### Frame hierarchy (Phase 1.2)

`world` is the **real floor** (RViz grid renders at z=0). `table_origin`
is a child of `world` elevated at `xyz = (0, 0, 0.746)` (= support base
0.030 + column 0.690 + tabletop 0.026). All DXF-derived XY positions
remain expressed in `table_origin`. Table support bases rest on the
floor (`table_origin z = -0.746`).

The camera aluminum profile is parented to a `plc_cabinet_camera_mount`
frame on the PLC cabinet top, at cabinet-local `(0.548, 0, 0)`. The
column visually rises from the cabinet rather than floating mid-table;
the camera body still lands over the DXF orange footprint at world
`(0.618, 0.407, 1.381)` (still 0.635 m above the tabletop).

### Visual conventions

- xArm 6 home pose published via `joint_state_publisher` `zeros`
  rosparam in `view_mezzanine.launch`:
  `joint1=0, joint2=-0.6, joint3=-1.2, joint4=0, joint5=1.5, joint6=0`.
- Gripper materials: base `aluminum_dark`, body `aluminum_light`,
  fingers `brushed_aluminum` (defined in `materials.xacro`).
- Cube grid: 9 differentiated colors on a 3×3 grid; base + 5 mm reborde.
- Conveyor uses DOBOT Conveyor Belt Kit official spec
  `0.700 × 0.215 × 0.060 m`; no STEP/SolidWorks CAD found yet on
  `download.dobot.cc`.
- Computer footprint (magenta DXF rect) represents the whole
  workstation; tower uses Lenovo tinker bbox `0.11825 × 0.07239 × 0.0861`
  with `tinker.obj` mesh slot pending under `meshes/computer/`.

### Workflow expectations (learned)

- Always deliver a written diagnostic + comparison table + ambiguity
  list BEFORE modifying URDF/Xacro. Wait for explicit user approval.
- Branch convention: `feature/mezzanine-rviz-simulation`. Never touch
  `main` directly; go via PR. Convention: `--merge` commit (not squash).
- Commit author set per-commit via `GIT_AUTHOR_*` env vars
  (no global git config writes).
- User prefers Spanish responses and fast pace; gives explicit
  authorization once and expects continuation.
- PR template uses Spanish summary + English Test plan checklist.
