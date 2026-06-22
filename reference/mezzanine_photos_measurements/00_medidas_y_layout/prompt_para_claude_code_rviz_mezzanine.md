# Prompt para Claude Code - usar medidas reales en RViz

Ya agregué fotos reales y medidas de la planta Mezzanine. Antes de modelar en RViz, lee este folder completo:

- `00_medidas_y_layout/`
- `01_estacion/`
- `02_cubos_y_base/`
- `03_camara_y_cabina/`
- `04_hmi_conveyor_botones/`
- `05_soportes_de_mesa/`
- `06_gripper/`

Usa `00_medidas_y_layout/mezzanine_medidas_posiciones.md` como fuente principal de medidas y posiciones. Las fotos son referencia visual, pero las medidas mandan cuando exista conflicto.

Objetivo:
Crear o actualizar la simulación RViz/ROS de la estación Mezzanine usando una arquitectura limpia de URDF/Xacro.

Reglas importantes:

1. Las medidas originales están en centímetros, pero ROS/RViz debe usar metros.
2. El origen está en la esquina inferior izquierda de la mesa.
3. `z = 0` es la superficie superior de la mesa.
4. No cambiar el origen sin documentarlo.
5. No inventar dimensiones faltantes; marcarlas como `TBD`.
6. Primero crear un layout simple y validable con geometrías básicas.
7. Después agregar detalles visuales.
8. Mantener componentes separados en Xacro:
   - mesa
   - soportes
   - cabina PLC
   - cobot/base
   - conveyor
   - sensor fotoeléctrico
   - cámara y perfil de aluminio
   - HMI
   - botones
   - cubos y base
   - pintarrón
   - gripper

Validación esperada:
- Abrir en RViz sin errores.
- Mesa con dimensiones correctas.
- Componentes ubicados según las posiciones del archivo de medidas.
- Cámara encima del sensor fotoeléctrico.
- Conveyor cerca del HMI.
- Cubos/base en su posición real.
- Cobot montado en `(24 cm, 42.5 cm, 0 cm)`.
- Botones en `(117 cm, 4 cm, 0 cm)`.
- Documentar cualquier ajuste visual realizado para que la escena se vea correcta.

Antes de escribir código, dame:
1. Resumen del sistema de coordenadas.
2. Tabla de componentes a modelar.
3. Qué medidas están completas.
4. Qué medidas faltan.
5. Plan de archivos Xacro/launch/RViz.
