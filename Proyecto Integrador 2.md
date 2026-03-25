


## 🛠️ Referencias y Herramientas

* **Software:** Blender (Workspace de 2D Animation).
* **Tutorial Base:** Me basé en la técnica de [este video de YouTube](https://www.youtube.com/watch?v=0myBDB1vuq0).
* **Referencias:** Usé la imagen `stick.jpg` (incluida aquí) para basarme en los 4 fotogramas clave del movimiento.

---

## 🚀 Proceso de Animación paso a paso

### 1. Configurar nuestro espacio de trabajo
Primero, abrí Blender y creé un nuevo proyecto seleccionando **"2D Animation"**. Usamos esta función porque nos prepara automáticamente un lienzo en blanco, bloquea la cámara en la posición correcta y nos activa la herramienta de dibujo, ideal para hacer animación tradicional.

### 2. Importar la referencia
Para no animar a ciegas, usamos nuestra imagen de referencia (`stick.jpg`) que contiene las 4 posturas clave del ciclo de carrera. La importé directamente al espacio de trabajo para saber exactamente cómo debía ir la inclinación del cuerpo y la posición de las extremidades en cada paso crítico.

### 3. Dibujar los fotogramas clave (Keyframes)
En el *Dope Sheet* (nuestra línea de tiempo), creé un *keyframe* vacío para empezar a dibujar. 
Usamos la herramienta de **Draw** con el *Grease Pencil* para trazar la cabeza, el torso y las extremidades. Repetí este proceso dibujando las 4 poses principales de la imagen de referencia, dejando un espacio de fotogramas entre ellas.

### 4. Uso del "Onion Skinning" (Papel Cebolla)
Para asegurarme de que el movimiento fuera fluido, activé la función de **Onion Skinning**. 
Usamos esta herramienta para ver una silueta difuminada de nuestro frame anterior y del frame siguiente al mismo tiempo. Esto nos sirve de guía perfecta para hacer los *in-betweens* (los dibujos intermedios que conectan las poses clave) sin perder las proporciones del *stickman*.

### 5. Ajustando el "Timing"
Una vez que tuve todos los dibujos, ajusté la velocidad. En la línea de tiempo, movemos los keyframes acercándolos o alejándolos entre sí. Usamos esto para darle más velocidad al impacto de la pierna y un poco más de tiempo de "aire" en la zancada, tal como se explica en el tutorial.

---pp
