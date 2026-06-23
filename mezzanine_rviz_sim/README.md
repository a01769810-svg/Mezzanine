# mezzanine_rviz_sim

ROS1/catkin package - RViz visualization of the Mezzanine industrial station.

- Phase 1: layout-only URDF/Xacro with correct TF and metric scale.
- No MoveIt, no Gazebo.
- xArm 6 from official `xarm_description` (no manual modeling, no copied meshes).
- Custom pneumatic gripper.

## Quick start

```bash
cd ~/mezzanine_ws
catkin_make
source devel/setup.bash
roslaunch mezzanine_rviz_sim view_mezzanine.launch
```

## Layout

```
mezzanine_rviz_sim/
|-- package.xml
|-- CMakeLists.txt
|-- launch/view_mezzanine.launch
|-- urdf/                       # modular Xacro per component
|-- meshes/custom/              # custom meshes (none in Phase 1)
|-- rviz/mezzanine.rviz
|-- config/layout_params.yaml
|-- scripts/                    # Phase 1: empty
`-- docs/                       # architecture, measurements, validation
```

See `docs/` for design notes, the measurement origin convention, and the validation
checklist.
