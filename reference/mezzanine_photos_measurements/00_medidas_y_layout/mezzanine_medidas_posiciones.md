# Mezzanine - medidas y posiciones para RViz/ROS

## Sistema de coordenadas propuesto

Todas las medidas están en **centímetros**.

El origen se toma en la **esquina inferior izquierda de la mesa**, visto desde arriba.

Convención recomendada para RViz/ROS:

- `x`: largo de la mesa, de 0 a 140 cm.
- `y`: ancho de la mesa, de 0 a 59.6 cm.
- `z`: altura.
- `z = 0`: superficie superior de la mesa.
- Valores negativos en `z`: elementos por debajo de la superficie de la mesa.
- Para ROS/RViz convertir de cm a metros multiplicando por `0.01`.

> Nota: si el eje `y` real queda invertido al comparar con las fotos, no cambiar las medidas todavía; primero documentar la inversión y ajustar el transform del frame de la mesa.

---

## Medidas principales

### Mesa

| Elemento | X / Largo | Y / Ancho | Z / Grosor |
|---|---:|---:|---:|
| Mesa | 140 cm | 59.6 cm | 2.6 cm |

Notas:
- Las esquinas de la mesa están redondeadas.
- Las esquinas están cubiertas por plástico negro de aproximadamente 0.3 cm de grosor.
- Para RViz se puede modelar como caja rectangular con esquinas simplificadas, y opcionalmente agregar cubiertas negras en los bordes/esquinas.

---

### Soportes de mesa

Cantidad: **2 soportes**

#### Base del soporte

| Elemento | X / Largo | Y / Ancho | Z / Grosor |
|---|---:|---:|---:|
| Base soporte mesa | 7 cm | 57.6 cm | 3 cm |

#### Columna del soporte

Posición de la columna dentro de la base:

| X | Y | Z |
|---:|---:|---:|
| 1 cm | 31 cm | 3 cm |

Medidas de la columna:

| X / Largo | Y / Ancho | Z / Altura |
|---:|---:|---:|
| 4 cm | 19.4 cm | 69 cm |

Notas:
- Modelar como estructura inferior de mesa.
- Mantenerla separada del tabletop para poder ajustar altura, patas y travesaños.

---

### Cabina PLC / gabinete de control

| Elemento | X / Largo | Y / Ancho | Z / Altura/Grosor |
|---|---:|---:|---:|
| Cabina PLC/gabinete | 70 cm | 25 cm | 50 cm |

Posición reportada:

| X | Y | Z |
|---:|---:|---:|
| 5 cm | 59.6 cm | -7.3 cm |

Notas:
- La cabina está al frente/borde de la mesa según referencia visual.
- `z = -7.3 cm` indica que se ubica por debajo del plano superior de la mesa.
- Confirmar orientación exacta del gabinete al montar en RViz.

---

### Cubos

| Elemento | X | Y | Z |
|---|---:|---:|---:|
| Cubo individual | 4 cm | 4 cm | 5 cm |

Notas:
- Cubos impresos en 3D de varios colores.
- Cada cubo tiene tapa/saliente circular superior.
- Para RViz se pueden modelar como cubos simples + cilindro superior.

---

### Base donde están los cubos

| Elemento | X | Y | Z |
|---|---:|---:|---:|
| Base azul de cubos | 14 cm | 14 cm | 2 cm |

Detalles:
- Grosor de borde aproximado: 0.5 cm.
- Tiene extracción/corte en el área interior.
- Sirve como fixture/base para ordenar cubos.

Posición:

| X | Y | Z |
|---:|---:|---:|
| 34.8 cm | 2 cm | 0 cm |

---

### Pintarrón / placa blanca de trabajo

| Elemento | X / Largo | Y / Ancho | Z / Grosor |
|---|---:|---:|---:|
| Pintarrón | 30.3 cm | 22.7 cm | 2 cm |

Posición:

| X | Y | Z |
|---:|---:|---:|
| 9 cm | 3.5 cm | 0 cm |

---

### Gripper

| Elemento | Medida |
|---|---:|
| Área de trabajo / apertura útil | 4.4 cm |
| Ancho total | 6 cm |
| Grosor de cada dedo | 0.8 cm |
| Base del gripper | 8 cm x 8 cm |
| Grosor de base | 0.6 cm |

Notas:
- Modelar como efector final del cobot.
- Separar links visuales: `gripper_base`, `gripper_body`, `left_finger`, `right_finger`.
- Si se simula movimiento, usar un joint simple de apertura/cierre.

---

### Conveyor

| Elemento | X / Largo | Y / Ancho | Z / Grosor |
|---|---:|---:|---:|
| Conveyor | TBD | 13.2 cm | 5.5 cm |

Posición:

| X | Y | Z |
|---:|---:|---:|
| 38.5 cm | 30 cm | 0 cm |

Notas:
- Falta confirmar largo exacto del conveyor.
- La banda es negra y tiene motor lateral.
- Hay un sensor fotoeléctrico montado en el conveyor.

---

### Sensor fotoeléctrico en conveyor

Medida/posición reportada:

| X | Y | Z |
|---:|---:|---:|
| 21 cm | 12 cm | 5 cm aprox. |

Interpretación recomendada:
- Tratar esta posición como **offset local sobre el conveyor**, salvo que se confirme que es absoluta desde el origen de mesa.
- Si es offset local y el origen del conveyor es `(38.5, 30, 0)`, entonces la posición aproximada absoluta sería `(59.5, 42, 5)` cm.
- Validar contra fotos antes de fijarlo.

---

### HMI

Posición:

| X | Y | Z |
|---:|---:|---:|
| 65 cm | 42.5 cm | 0 cm |

Notas:
- HMI Siemens SIMATIC HMI TOUCH.
- Montada en soporte/acrílico cerca del conveyor.
- Mantener como componente separado: `hmi_mount`, `hmi_screen`.

---

### Computadora

Notas:
- La computadora está a la derecha en la mesa.
- Falta posición exacta.
- Para RViz se puede colocar de forma aproximada con base en fotos, pero debe quedar marcado como `TBD`.

---

### Start button y Emergency Stop

Posición:

| X | Y | Z |
|---:|---:|---:|
| 117 cm | 4 cm | 0 cm |

Notas:
- Panel blanco con botón verde `START`.
- Botón rojo/amarillo `EMERGENCY STOP`.
- Para RViz separar visualmente: `start_button_panel`, `start_button`, `emergency_stop_button`.

---

### Base del cobot

Posición:

| X | Y | Z |
|---:|---:|---:|
| 24 cm | 42.5 cm | 0 cm |

Notas:
- Base circular metálica del cobot.
- Este punto debe usarse como frame base del robot o como referencia para ubicar `cobot_base_link`.

---

### Cámara

Descripción:
- La cámara está arriba del sensor fotoeléctrico.
- Está montada con soporte de perfil de aluminio de 4 cm x 4 cm.
- La cámara está desplazada aproximadamente **16 cm en `y` negativo** porque está sujeta a un perfil.
- Altura desde la mesa: **63.5 cm**.

Interpretación recomendada:
- Si el sensor fotoeléctrico se coloca en `(59.5, 42, 5)` cm por offset local del conveyor, entonces una primera aproximación para la cámara sería:
  - `x ≈ 59.5 cm`
  - `y ≈ 42 - 16 = 26 cm`
  - `z ≈ 63.5 cm`
- Confirmar esta interpretación contra las fotos antes de fijarla.
- El perfil de aluminio debe modelarse como vertical + brazo superior.

---

## Posiciones resumidas

| Componente | X | Y | Z | Nota |
|---|---:|---:|---:|---|
| Base cobot | 24 | 42.5 | 0 | Frame base del robot |
| Base cubos | 34.8 | 2 | 0 | Fixture/base azul de cubos |
| Conveyor | 38.5 | 30 | 0 | Falta largo exacto |
| Sensor fotoeléctrico | 21 | 12 | 5 aprox | Probablemente local al conveyor |
| HMI | 65 | 42.5 | 0 | Pantalla Siemens |
| Start + Emergency Stop | 117 | 4 | 0 | Panel de botones |
| Pintarrón | 9 | 3.5 | 0 | Placa blanca |
| Cabina PLC | 5 | 59.6 | -7.3 | Gabinete bajo/borde mesa |
| Cámara | Ver nota | Ver nota | 63.5 | Sobre sensor, offset `y=-16` |

---

## Recomendaciones para Claude

1. No inventar dimensiones faltantes sin marcarlas como `TBD`.
2. Crear primero un modelo simple de layout en URDF/Xacro usando cajas y cilindros.
3. Mantener cada componente separado en archivos Xacro:
   - `table.xacro`
   - `table_supports.xacro`
   - `plc_cabinet.xacro`
   - `cobot_mount.xacro`
   - `conveyor.xacro`
   - `camera_frame.xacro`
   - `hmi.xacro`
   - `buttons_panel.xacro`
   - `cubes_and_base.xacro`
   - `gripper.xacro`
4. Usar metros en el código ROS/RViz.
5. Documentar cualquier conversión de cm a m.
6. Validar en RViz:
   - Mesa en escala correcta.
   - Cobot dentro de la mesa.
   - Conveyor alineado con HMI/cámara.
   - Cubos y base en la esquina correcta.
   - Cabina PLC debajo o al borde de la mesa.
   - Cámara arriba del fotoeléctrico.
7. No mover el origen sin avisar.
