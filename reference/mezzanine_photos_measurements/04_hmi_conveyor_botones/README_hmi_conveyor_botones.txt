HMI, conveyor y botones - referencias para RViz/ROS

Estas imagenes corresponden a elementos de control y transporte de la estacion:

1. Panel de botones: boton START verde y paro de emergencia rojo/amarillo montados en placa blanca sobre la mesa.
2. Conveyor: banda transportadora negra vista superior, con referencia de medida, sensor fotoelectrico amarillo, motor lateral y HMI cerca.
3. HMI: pantalla Siemens SIMATIC HMI TOUCH montada sobre soporte/acrilico, identificada con IP 192.168.1.152.

Usar estas fotos como referencia visual para modelado en RViz:
- Posicion relativa sobre mesa industrial.
- Colores principales: mesa beige, conveyor negro, HMI gris/azul, boton start verde, emergency stop rojo/amarillo.
- Mantener nombres claros para frames/links: hmi_panel, conveyor, start_button_panel, emergency_stop_button.
- No asumir medidas exactas si no estan definidas; usar proporciones aproximadas y documentar cualquier escala.
