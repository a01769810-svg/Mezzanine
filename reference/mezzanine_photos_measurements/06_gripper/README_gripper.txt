Gripper / pinza del robot - referencia para RViz/ROS

Esta imagen muestra un acercamiento frontal del efector final (gripper) montado en el cobot.

Elementos visibles:
1. Cuerpo del actuador neumático/aluminio.
2. Conexiones neumáticas y manguera azul.
3. Placa/adaptador superior al tool flange.
4. Dos dedos metálicos paralelos tipo pinza.
5. Relación del gripper con la zona de pick/place sobre la mesa.

Usar esta foto como referencia visual para modelado en RViz:
- Mantener un look industrial simple y limpio.
- Separar visualmente: tool flange, actuator_body, gripper_mount y gripper_fingers.
- Si se modela cinemática, representar la apertura/cierre de los dedos con un joint simple.
- No asumir medidas exactas si no están definidas; usar proporciones aproximadas y documentar cualquier escala.
