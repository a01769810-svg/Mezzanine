# Handoff: implementar la simulación RViz/ROS de la estación Mezzanine (Ubuntu)

> Copia/pega este documento completo en una nueva conversación con Claude en **Ubuntu**.
> Es un prompt operativo: define qué clonar, qué leer, qué crear, qué validar y qué entregar.

Eres Claude trabajando en **Ubuntu**. Vas a implementar la **Fase 1** de una simulación
industrial en **RViz clásico (ROS1)** para la estación "Mezzanine" (cobot xArm 6 + conveyor +
HMI + cámara + cubos). El diagnóstico ya fue hecho y aprobado en Windows; tu trabajo es ejecutar.

NO empieces a programar de inmediato. Primero lee, analiza y confirma. Luego pídeme aprobación
del plan antes de implementar (la skill del repo te obliga a esto).

---

## 0) Repos y branches

- Repo principal:        https://github.com/a01769810-svg/Mezzanine
  - branch `main`        -> referencias, medidas y este prompt (`reference/`)
  - branch `RVIZ-skill`  -> la skill `.claude/skills/rviz-mezzanine-simulation/SKILL.md`
- Repo oficial del cobot: https://github.com/xArm-Developer/xarm_ros  (ROS1)

Trabaja la implementación en una branch NUEVA (no en main ni en RVIZ-skill):

```bash
git checkout -b feature/mezzanine-rviz-simulation
```

---

## 1) Decisiones YA APROBADAS (no las re-litigues)

1. ROS1 / catkin.
2. RViz clásico (NO rviz2).
3. Cobot = xArm 6 para la primera implementación.
4. Usar el repo oficial `xArm-Developer/xarm_ros` (`xarm_description`) para el cobot.
5. NO modelar el cobot desde cero con cajas/primitivas.
6. NO copiar meshes oficiales del xArm innecesariamente (referenciarlos vía `package://`).
7. Gripper = CUSTOM (pinza neumática de 2 dedos), NO el gripper oficial de UFACTORY.
8. NO usar MoveIt ni Gazebo en la Fase 1.
9. Fase 1 = visualización RViz + URDF/Xacro modular + TF correcto.
10. Simulación modular: un Xacro por componente.
11. NO borrar ni modificar simulaciones anteriores del repo.

Sistema de coordenadas (NO cambiar sin documentar):
- Origen = esquina inferior izquierda de la mesa (vista superior).
- x = largo de mesa, y = ancho de mesa, z = altura. z=0 = superficie de la mesa.
- Medidas fuente en cm -> ROS en metros: `m = cm * 0.01`.

---

## 2) Verifica el entorno Ubuntu ANTES de instalar nada

NO asumas que ROS está instalado. Ejecuta y repórtame:

```bash
lsb_release -a
rosversion -d 2>/dev/null || echo "ROS no detectado"
which catkin catkin_make rviz xacro 2>/dev/null
dpkg -l | grep -E "ros-noetic-(robot-state-publisher|joint-state-publisher-gui|xacro|rviz)" || true
```

Compatibilidad de SO (IMPORTANTE):
- ROS1 Noetic está soportado oficialmente en **Ubuntu 20.04 (Focal)**. Es lo ideal.
- Si el equipo tiene **Ubuntu 22.04 / 24.04**, NO instales Noetic a la fuerza. Propón una de:
  - (a) Docker con imagen `osrf/ros:noetic-desktop-full` (recomendado), o
  - (b) explicar la incompatibilidad y pedir confirmación antes de instalar.

  Documenta la opción elegida antes de continuar.
- Si ROS no está instalado y SÍ es 20.04, instala Noetic + las herramientas mínimas:

```bash
sudo apt update
sudo apt install -y ros-noetic-desktop \
    ros-noetic-robot-state-publisher ros-noetic-joint-state-publisher-gui \
    ros-noetic-xacro ros-noetic-rviz python3-catkin-tools python3-rosdep
sudo rosdep init 2>/dev/null; rosdep update
```

---

## 3) Qué leer PRIMERO (en este orden)

1. `.claude/skills/rviz-mezzanine-simulation/SKILL.md`  (rama RVIZ-skill) — reglas que gobiernan todo.
2. `reference/mezzanine_photos_measurements/00_medidas_y_layout/mezzanine_medidas_posiciones.md`  (FUENTE PRINCIPAL de medidas/posiciones).
3. `reference/mezzanine_photos_measurements/00_medidas_y_layout/layout_ros_params_m.yaml`  (parámetros ya en metros).
4. `reference/mezzanine_photos_measurements/00_medidas_y_layout/component_dimensions_cm.csv`
5. `reference/mezzanine_photos_measurements/00_medidas_y_layout/component_positions_cm.csv`
6. Las fotos en `01_estacion/` … `06_gripper/` (solo referencia visual; las medidas mandan).

Las medidas mandan sobre las fotos. No inventes medidas faltantes: márcalas `TBD`.

---

## 4) Analiza la simulación ROS previa del repo y CONFÍRMAME

Antes de implementar, revisa las sims previas (si están presentes en el entorno) y confirma:
- ROS version (esperado: ROS1).
- Layout del paquete catkin (`package.xml` format 2, `CMakeLists.txt`).
- Patrón de launch (esperado: `robot_state_publisher` + `joint_state_publisher_gui` + `rviz -d`).
- Patrón de RViz config (RobotModel sobre `robot_description`, Fixed Frame raíz).
- Patrón URDF/Xacro (la previa es URDF plano; nosotros haremos Xacro modular).
- Meshes (`package://`, escala 0.001 mm->m).
- Flujo TF / joint_states (usa el topic ESTÁNDAR `/joint_states`, NO `/robot/cobot_joints`).

Entrega un diagnóstico breve. NO implementes hasta que yo apruebe tu plan.

---

## 5) Prepara el workspace catkin e integra xarm_ros

```bash
mkdir -p ~/mezzanine_ws/src && cd ~/mezzanine_ws/src
# Clona el repo del proyecto (o agrégalo como remoto si ya lo tienes):
git clone https://github.com/a01769810-svg/Mezzanine.git mezzanine_repo
# Integra el cobot oficial (xarm_ros es ROS1). Como submódulo del repo del proyecto
# (preferido) o clonado directo en src/. NO copies sus meshes a tu paquete.
git clone https://github.com/xArm-Developer/xarm_ros.git
cd xarm_ros && git submodule update --init --recursive && cd ..

cd ~/mezzanine_ws
rosdep install --from-paths src --ignore-src -r -y
catkin_make    # o: catkin build
source devel/setup.bash
```

Integración del xArm 6 (en tu Xacro top-level), NO modelado manual:
- `<xacro:include filename="$(find xarm_description)/urdf/xarm6/xarm6.urdf.xacro"/>`
- Instancia el macro con un `prefix` y adjunta su base a tu frame `cobot_base`
  (junta `fixed`, o usa los args oficiales `attach_to`/`attach_xyz`/`attach_rpy`).
- Referencia útil de "robot sobre mesa": `xarm_gazebo/launch/xarm7_beside_table.launch`.
- NO actives `add_gripper:=true` (el gripper oficial NO aplica; el real es neumático custom).

---

## 6) Estructura a crear: paquete `mezzanine_rviz_sim` (ROS1/catkin)

```
mezzanine_rviz_sim/
├── package.xml            # format 2; depend: xarm_description, xacro,
│                          #   robot_state_publisher, joint_state_publisher_gui, rviz
├── CMakeLists.txt         # catkin; install(DIRECTORY urdf meshes launch rviz config scripts docs ...)
├── README.md
├── launch/view_mezzanine.launch
├── urdf/
│   ├── mezzanine_cell.urdf.xacro   # top: incluye todos + xArm6 + materiales
│   ├── materials.xacro
│   ├── table.xacro · table_supports.xacro
│   ├── plc_cabinet.xacro
│   ├── conveyor.xacro · photoelectric_sensor.xacro
│   ├── camera_frame.xacro          # perfil aluminio 4x4 vertical + brazo
│   ├── hmi.xacro · buttons_panel.xacro
│   ├── cubes_and_base.xacro · white_board.xacro
│   └── gripper_adapter.xacro       # pinza neumática custom
├── meshes/custom/ + meshes/README.md
├── rviz/mezzanine.rviz
├── config/layout_params.yaml       # copia de layout_ros_params_m.yaml
├── scripts/joint_state_simulator.py # opcional: anima xArm sobre /joint_states
└── docs/architecture.md · measurements.md · validation.md
```

Nombres de frames recomendados: `world`, `table_origin`, `table_top`, `table_support_left`,
`table_support_right`, `plc_cabinet`, `cobot_base`, `xarm_base_link`, `gripper_tool`, `conveyor_base`,
`photoelectric_sensor`, `camera_profile`, `camera_frame`, `hmi_panel`, `start_button_panel`,
`start_button`, `emergency_stop_button`, `cube_base`, `cube_01`, `white_board`, `computer`.

---

## 7) Qué implementar en FASE 1 (orden sugerido)

1. Crear primero los docs (`architecture.md`, `measurements.md`, `validation.md`).
2. Crear/copiar `config/layout_params.yaml`.
3. Mesa + `table_origin`/`table_top`.
4. Soportes de mesa (2).
5. Cabina PLC.
6. `cobot_base`.
7. Integrar xArm 6 oficial (placeholder claramente marcado SOLO si el modelo no carga).
8. Gripper neumático custom (montado en la brida/`gripper_tool`).
9. Conveyor.
10. Sensor fotoeléctrico.
11. Perfil de aluminio + cámara.
12. HMI.
13. Botones Start + Emergency Stop.
14. Base de cubos + cubos.
15. Pintarrón / placa blanca.
16. Placeholder de computadora (posición TBD).
17. `launch/view_mezzanine.launch` (robot_state_publisher + joint_state_publisher_gui + rviz -d).
18. `rviz/mezzanine.rviz`.
19. Validar TF, escala y alineación visual.
20. Documentar los TBD restantes.

Medidas clave en metros (de la fuente):
- mesa `1.400 × 0.596 × 0.026`
- soporte base `0.070 × 0.576 × 0.030` (×2); soporte columna `0.040 × 0.194 × 0.690`
- cabina PLC `0.700 × 0.250 × 0.500` @ `[0.050, 0.596, -0.073]`
- cubo `0.040 × 0.040 × 0.050`; base cubos `0.140 × 0.140 × 0.020` @ `[0.348, 0.020, 0]`
- pintarrón `0.303 × 0.227 × 0.020` @ `[0.090, 0.035, 0]`
- gripper base `0.080 × 0.080 × 0.006`, ancho `0.060`, dedo `0.008`, apertura `0.044`
- conveyor `(largo TBD) × 0.132 × 0.055` @ `[0.385, 0.300, 0]`
- cobot_base @ `[0.240, 0.425, 0]`; HMI @ `[0.650, 0.425, 0]`; botones @ `[1.170, 0.040, 0]`

---

## 8) Validación en RViz (checklist)

```bash
roslaunch mezzanine_rviz_sim view_mezzanine.launch
```

Verifica:
- xacro parsea sin error; URDF se genera sin error.
- RViz abre; Fixed Frame válido; árbol TF conectado (sin frames duplicados).
- Paths de meshes resuelven (xArm vía `package://`, sin paths rotos).
- Escala de mesa correcta (`1.40 × 0.596` m).
- `cobot_base` en su posición medida; conveyor en su posición; cámara arriba del sensor fotoeléctrico;
  HMI cerca del conveyor; cubos/base en su zona; botones en su zona; cabina PLC al borde/frente;
  gripper unido a la brida del cobot.
- Medidas faltantes marcadas `TBD`; supuestos documentados.

Comando útil para depurar el URDF generado:

```bash
xacro urdf/mezzanine_cell.urdf.xacro > /tmp/mezzanine.urdf && check_urdf /tmp/mezzanine.urdf
```

---

## 9) TBD / pendientes de validación (NO inventar)

- Largo del conveyor (solo ancho `0.132` y grosor `0.055`).
- Sensor fotoeléctrico `(21,12,5)` cm: probablemente LOCAL al conveyor -> abs ≈ `(0.595, 0.420, 0.050)` m. Validar con fotos.
- Cámara: derivada (sobre el sensor, offset `y=-0.16` m, `z=0.635` m) -> ≈ `(0.595, 0.260, 0.635)` m. Validar.
- Posición de la computadora: TBD ("a la derecha").
- Dimensiones de HMI, panel de botones y orientación de cabina PLC: aproximar y marcar pendiente.
- Posible inversión del eje `y` vs. fotos: si ocurre, NO cambiar medidas; ajustar el transform del frame de mesa y documentarlo.
- DOF del xArm: se asume xArm 6; confirmar físicamente contando juntas si hay duda.

---

## 10) Qué NO hacer

- No borrar/mover simulaciones anteriores ni romper launch files existentes.
- No reescribir el proyecto desde cero ni implementar antes de que apruebe el plan.
- No inventar medidas; no cambiar el origen sin documentarlo.
- No recrear el cobot con primitivas; no duplicar meshes oficiales del xArm.
- No usar MoveIt ni Gazebo en Fase 1.
- No usar el topic `/robot/cobot_joints` (usa `/joint_states`); no mezclar lógica de control con lo visual sin documentar.
- No hacer `git add .` sin revisar; commitea solo el paquete nuevo.

---

## 11) Entregables que debes generar

1. Diagnóstico breve de la sim previa (sección §4) ANTES de implementar.
2. Plan de implementación para mi aprobación.
3. Paquete `mezzanine_rviz_sim` con Xacro modular, launch y rviz config.
4. `docs/architecture.md`, `docs/measurements.md` (con conversiones cm->m), `docs/validation.md` (checklist + resultados).
5. Reporte de validación en RViz (capturas o descripción de TF/escala).
6. Lista de TBD restantes.
7. Todo en la branch `feature/mezzanine-rviz-simulation`; abre PR hacia `main` cuando esté validado.

Empieza por el paso §3 (leer la skill y las medidas), luego §2 (verificar entorno), luego §4
(diagnóstico). NO implementes hasta que apruebe tu plan.
