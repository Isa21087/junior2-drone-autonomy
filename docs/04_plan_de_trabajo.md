# Plan de trabajo compartido

## Fuentes y alcance

- Presentación Clase_ROS2_Gazebo_CAD_a_Bridge.
- Propuesta aprobada de Isabela y Felipe.
- Pruebas realizadas sobre los modelos de PX4.
- Recomendaciones de organización de Iván.

Las recomendaciones de Iván no son obligaciones adicionales de la entrega.

## Estado actual

Los bridges de cámara, CameraInfo, IMU y LiDAR están implementados
mediante YAML y launch. Se probaron con dos variantes separadas de PX4.

Faltan URDF/Xacro, TF/RViz, mundo guardado, modelo combinado y
arranque general. Estas capacidades se abordarán progresivamente.

## Trabajo por pilares

Cada pilar tiene tareas complementarias y una verificación conjunta.

| Pilar | Isabela | Felipe | Verificación conjunta |
|---|---|---|---|
| Base y ejecución | Explicar y documentar los bridges existentes | Reproducir instalación y ejecución | Ambos ejecutan los launch y verifican los tópicos |
| Mundo de pruebas | Configuración visual, ventanas y cámara de la interfaz | Física, suelo, obstáculos y spawn | Ambos ejecutan el mundo guardado |
| Sensores | Configuración del bridge y mensajes ROS 2 | Parámetros del sensor y mensajes Gazebo | Ambos comparan datos, frames y frecuencias |
| Descripción y RViz | Configuración de RViz y launch | Links, joints y posiciones | Ambos revisan el árbol TF |

La cámara de la interfaz de Gazebo y la cámara montada en el dron
son elementos distintos.

Este reparto es inicial. En las siguientes tareas se intercambiarán
implementación y revisión.

## Ciclo inmediato

1. Terminar y verificar la reorganización del repositorio.
2. Felipe reproduce la base existente.
3. Isabela explica el recorrido sensor → tópico Gazebo → bridge → ROS 2.
4. Ambos ejecutan una prueba y registran las dudas.
5. Comenzar la siguiente capacidad de la clase: descripción y TF/RViz.

No es necesario esperar a que una persona termine todo un subsistema.
Las tareas deben ser pequeñas y acordar previamente nombres de
tópicos, frames y archivos que compartirán.

## Reglas de Git

- Trabajar en ramas por tarea.
- Mantener main con capacidades comprobadas.
- Revisar los cambios antes de fusionarlos.
- La otra persona reproduce la prueba cuando tenga disponible el entorno.
- No subir compilaciones, entornos virtuales ni el repositorio completo de PX4.
- Documentar resultados y limitaciones reales.

## Criterio de terminado

Una tarea está terminada cuando:

1. Sus archivos están guardados en Git.
2. Tiene instrucciones claras.
3. Existe evidencia de funcionamiento.
4. La otra persona puede reproducirla.
5. Ambos pueden explicar su propósito y su relación con el sistema.