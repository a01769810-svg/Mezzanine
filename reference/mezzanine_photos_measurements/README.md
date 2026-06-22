# Mezzanine — Reference Photos and Measurements

This folder contains the **reference material** for the RViz/ROS simulation of the
**Mezzanine** industrial station (xArm 6 cobot + conveyor + HMI + camera + cubes).
It is reference data only — the actual simulation package (`mezzanine_rviz_sim`) is **not**
implemented here.

## How to use this reference

1. These photos and measurements are the reference for building the **RViz/ROS** simulation
   of the Mezzanine station.
2. **Measurements take priority over photos.** Photos are visual reference only; whenever a
   photo and a measurement disagree, the measurement wins.
3. The **original measurements are in centimeters (cm)**.
4. **ROS/RViz must use meters (m)**. Convert with `meters = centimeters * 0.01`.
5. The **coordinate origin** is the **lower-left corner of the tabletop** (seen from above):
   `x` = table length, `y` = table width, `z` = height.
6. **`z = 0` is the tabletop (top) surface.** Negative `z` is below the tabletop.
7. **Do not invent missing measurements.** Anything missing or ambiguous must be marked `TBD`
   and flagged as pending validation.
8. The **xArm cobot must be taken from the official repository**
   [`xArm-Developer/xarm_ros`](https://github.com/xArm-Developer/xarm_ros) (ROS1). Do not model
   the cobot from scratch with primitives, and do not duplicate official xArm meshes unnecessarily.

## Folder structure

```
reference/mezzanine_photos_measurements/
├── 00_medidas_y_layout/        # PRIMARY SOURCE — measurements, positions, ROS params
│   ├── mezzanine_medidas_posiciones.md     # main measurement/position document
│   ├── component_dimensions_cm.csv
│   ├── component_positions_cm.csv
│   ├── layout_ros_params_m.yaml            # same data already converted to meters
│   └── prompt_para_claude_code_rviz_mezzanine.md
├── 01_estacion/                # station overview photos
├── 02_cubos_y_base/            # cubes and cube base
├── 03_camara_y_cabina/         # camera, aluminum profile and PLC cabinet
├── 04_hmi_conveyor_botones/    # HMI, conveyor, Start/Emergency-Stop buttons
├── 05_soportes_de_mesa/        # table supports (underside)
└── 06_gripper/                 # custom pneumatic gripper
```

**Primary measurement source:**
`00_medidas_y_layout/mezzanine_medidas_posiciones.md`

## Approved technical context (Phase 1)

- ROS1 / catkin, **RViz classic** (not rviz2).
- Cobot assumed **xArm 6**, from official `xarm_ros`.
- Gripper is **custom** (pneumatic 2-finger), not the official UFACTORY gripper.
- No MoveIt and no Gazebo in Phase 1; Phase 1 = RViz visualization + modular URDF/Xacro + correct TF.
- Previous simulations must not be deleted or modified.

See the operative implementation prompt at `reference/UBUNTU_HANDOFF_PROMPT.md` and the skill at
`.claude/skills/rviz-mezzanine-simulation/SKILL.md` (branch `RVIZ-skill`).
