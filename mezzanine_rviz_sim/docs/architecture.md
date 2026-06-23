# Architecture - mezzanine_rviz_sim (Phase 1.2)

## Scope

Phase 1.x produces a static RViz layout of the Mezzanine cell: table, supports,
PLC cabinet, xArm 6 with a custom pneumatic gripper, conveyor, photoelectric
sensor, camera with aluminum profile, HMI, Start/E-Stop panel, cubes + cube base,
white board, and a computer workstation. No MoveIt, no Gazebo, no control logic.

## Decisions (approved in handoff + Phase 1.x updates)

1. ROS1 Noetic + catkin (Ubuntu 20.04).
2. RViz classic (not rviz2).
3. xArm 6 from `xarm_description` of the official `xarm_ros`.
4. xArm is included via Xacro macro, NOT duplicated meshes; we reference
   `package://xarm_description/...`.
5. Custom pneumatic gripper (NOT the official UFACTORY gripper).
6. Modular Xacro: one file per major component.
7. **Phase 1.2**: `world` = real floor (RViz grid at z=0). `table_origin` is a
   child of `world` elevated 0.746 m so the table sits on its supports above
   the floor. XY measurements in `table_origin` are unchanged from the DXF.
8. All `xyz_m` in the YAML and Xacro instantiations represent the
   LOWER-LEFT-FRONT CORNER of each component box. The geometry inside each link
   shifts by +size/2 so that link frames sit at the documented anchor point.
9. **Phase 1.2**: the camera aluminum profile is parented to the PLC cabinet
   via a `plc_cabinet_camera_mount` frame, so the column visually rises from
   the cabinet top instead of floating in the middle of the table.

## TF tree (Phase 1.2)

```
world  (real floor, z = 0)
|
+-- table_origin   xyz = (0, 0, 0.746)  -- table top surface
    |
    +-- table_top
    +-- table_support_left   (base rests on world floor z=0)
    +-- table_support_right  (base rests on world floor z=0)
    +-- plc_cabinet
    |   `-- plc_cabinet_camera_mount   xyz = (0.548, 0, 0)  (cabinet top, front face)
    |       `-- camera_profile
    |           `-- camera_frame   (over DXF orange footprint, world Z = 1.381)
    +-- white_board
    +-- cube_base
    |   +-- cube_base_lip
    |   `-- cube_00 .. cube_22  (9 cubes, 3x3 grid)
    +-- conveyor_base
    |   `-- photoelectric_sensor
    +-- hmi_panel
    |   `-- hmi_display
    +-- start_button_panel
    |   +-- start_button
    |   `-- emergency_stop_button
    +-- computer
    `-- cobot_base
        `-- link_base (xArm; created by xarm_device macro, attached to cobot_base)
            `-- link1 ... link6
                `-- gripper_tool (fixed)
                    +-- gripper_base   (aluminum_dark)
                    +-- gripper_body   (aluminum_light)
                    +-- left_finger    (brushed_aluminum)
                    `-- right_finger   (brushed_aluminum)
```

## Home pose (visual only)

`view_mezzanine.launch` sets the `zeros` rosparam namespace on
`joint_state_publisher` / `joint_state_publisher_gui` to publish an initial
JointState matching this home pose:

| Joint  | Value (rad) | Notes |
|--------|-------------|-------|
| joint1 | 0.0         | base rotation facing +x of table |
| joint2 | -0.6        | shoulder tilted ~-34 deg |
| joint3 | -1.2        | elbow bent ~-69 deg |
| joint4 | 0.0         | wrist roll neutral |
| joint5 | 1.5         | wrist pitch ~86 deg, gripper looking down |
| joint6 | 0.0         | tool roll neutral |

## xArm integration

Top-level Xacro declares an arg `arm_model` (default `xarm6`) that maps to a
`dof` value (5/6/7). The xArm is brought in via:

```xml
<xacro:include filename="$(find xarm_description)/urdf/xarm_device_macro.xacro"/>
<xacro:xarm_device prefix=""
  dof="${dof}"
  robot_type="xarm"
  attach_to="cobot_base"
  attach_xyz="0 0 0"
  attach_rpy="0 0 0"
  create_attach_link="false"
  add_gripper="false"
  add_vacuum_gripper="false"
  add_realsense_d435i="false"
  load_gazebo_plugin="false"
  mesh_suffix="stl" />
```

- `add_gripper=false` because the real gripper is custom pneumatic.
- `add_realsense_d435i=false` because the station camera is separate.
- `load_gazebo_plugin=false` because Phase 1 has no Gazebo.
- `create_attach_link=false` because we define `cobot_base` ourselves.

## Custom gripper

Mounted to the xArm's `link6` via a fixed joint `link6 -> gripper_tool`.
Modeled with:

- `gripper_base` 0.080 x 0.080 x 0.006 m (mounting plate)
- `gripper_body` 0.060 x 0.060 x 0.040 m (TBD - actuator body)
- `left_finger` and `right_finger` 0.008 m thick fingers with opening 0.044 m

The fingers are fixed joints in Phase 1. A revolute or prismatic joint can be
added later for actuation simulation.

## Files

| File | Role |
|------|------|
| `urdf/mezzanine_cell.urdf.xacro` | Top-level: args, world/table_origin links, includes all components, xArm |
| `urdf/materials.xacro` | Color materials (gray, black, white, blue, etc.) |
| `urdf/table.xacro` | `table_origin`, `table_top` |
| `urdf/table_supports.xacro` | Left/right table supports |
| `urdf/plc_cabinet.xacro` | PLC cabinet box |
| `urdf/conveyor.xacro` | Conveyor base box |
| `urdf/photoelectric_sensor.xacro` | Photoelectric sensor box on conveyor |
| `urdf/camera_frame.xacro` | Aluminum profile + camera |
| `urdf/hmi.xacro` | HMI display |
| `urdf/buttons_panel.xacro` | Start + E-Stop panel |
| `urdf/cubes_and_base.xacro` | Cube base + one cube |
| `urdf/white_board.xacro` | Working white board |
| `urdf/gripper_adapter.xacro` | Custom pneumatic gripper macro |
