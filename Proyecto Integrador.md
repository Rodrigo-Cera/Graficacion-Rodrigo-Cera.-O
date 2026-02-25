
# Documentación Técnica: Generación Procedural y Animación de Cámara

El presente documento expone la lógica geométrica y algorítmica utilizada para la generación automatizada de un escenario 3D mediante la API de Python en Blender (`bpy`). El desarrollo se dividió en fases secuenciales: la creación de una estructura lineal, la anexión de una curvatura matemática y, finalmente, la automatización del recorrido visual.

## 1. Construcción del Tramo Recto

La primera fase del algoritmo consiste en establecer un pasillo inicial rectilíneo. 

**Lógica de Implementación:**
* Se emplea un bucle iterativo que avanza a lo largo del eje Y. En cada iteración, se instancian dos mallas cúbicas que fungen como paredes laterales, separadas por una variable de distancia (`ancho`).
* De manera simultánea a la creación de la geometría, el algoritmo calcula el punto central exacto del pasillo en ese segmento espacial.
* **Implementación en el código:** Este punto central coordenado $(0, y\_pos, 1.5)$ se almacena dinámicamente en una lista denominada `puntos_camara`. Esta lista es la estructura de datos fundamental, ya que actúa como la "columna vertebral" espacial que posteriormente guiará a la cámara.

## 2. Integración y Cálculo del Tramo Curvo

Una vez concluido el tramo recto, el escenario requiere un giro. Para mantener la cohesión estructural, la curva no se modela de forma arbitraria, sino mediante un cálculo trigonométrico preciso.

**Lógica de Implementación:**
* Se define un punto de origen para la curva, denominado $(c_x, c_y)$. Este punto actúa como el centro de una circunferencia imaginaria sobre la cual rotarán los nuevos bloques.
* El giro planificado es de $90^\circ$ hacia la derecha. Para lograrlo, se itera sobre un ángulo $\theta$ que decrece desde $\pi$ radianes ($180^\circ$) hasta $\frac{\pi}{2}$ radianes ($90^\circ$).
* La posición bidimensional de cada bloque (tanto para la pared interna como externa) se calcula utilizando las ecuaciones paramétricas de la circunferencia:
  * $x = c_x + R \cdot \cos(\theta)$
  * $y = c_y + R \cdot \sin(\theta)$
  *(Donde $R$ representa el radio ajustado sumando o restando el ancho del pasillo).*
* **Alineación Geométrica:** Para que los bloques sigan fluidamente la tangente de la curva y no queden paralelos al eje original, se les aplica una rotación en el eje Z (`rot_z`), calculada a partir del ángulo actual.
* **Implementación en el código:** Al igual que en el tramo recto, el centro exacto de este segmento curvo se calcula y se adjunta (método `.append()`) a la lista preexistente `puntos_camara`. De este modo, la matriz de coordenadas integra de forma ininterrumpida la recta y la curva.

## 3. Sistema de Cámara y Seguimiento de Trayectoria

El desafío final radicaba en lograr que una cámara recorriera la totalidad del escenario generado de forma autónoma y fluida.

**Lógica de Implementación:**
Para resolver esto, se utilizó una técnica de "restricción de seguimiento" (Path Animation), dividida en tres pasos:

1. **Generación de la Ruta:** Se instancia un nuevo objeto de curva polinómica (`CURVE`). El algoritmo recorre la lista `puntos_camara` (que ahora contiene las coordenadas de todo el recorrido) y asigna cada tupla de coordenadas como un vértice espacial (`spline.points[idx].co`) de esta nueva línea invisible.
2. **Vinculación de la Cámara:** Se crea un objeto de cámara en la escena y se le asigna una restricción de tipo `FOLLOW_PATH`. Esta restricción obliga a la cámara a anclarse geométricamente a la curva Bezier generada en el paso anterior.
3. **Interpolación del Recorrido (Animación):** Para generar el desplazamiento en el tiempo, se interactúa con la propiedad `offset_factor` de la restricción. 
   * En el fotograma 1, se inserta un *keyframe* con el valor $0.0$ (inicio de la curva).
   * En el fotograma 200, se inserta un *keyframe* con el valor $1.0$ (final de la curva).
   * Blender interpola automáticamente los valores intermedios, resultando en un desplazamiento constante y suave a lo largo de los ejes recto y curvo sin requerir animación manual frame a frame.

### El siguiente código implementa todo lo mencionado anteriormente, dando como resultado un escenario con un camino recto  con una curva, ademas de la camara que sigue un trayecto enmedio del camino.
```
import bpy
import math

def crear_material(nombre, color_rgb):
    mat = bpy.data.materials.new(name=nombre)
    mat.use_nodes = True
    bsdf = mat.node_tree.nodes["Principled BSDF"]
    bsdf.inputs['Base Color'].default_value = (*color_rgb, 1.0)
    return mat

def generar_escenario():
    # 1. Limpiar escena
    bpy.ops.object.select_all(action='SELECT')
    bpy.ops.object.delete()

    # 2. Materiales
    mat_a = crear_material("ParedOscura", (0.1, 0.1, 0.1))
    mat_b = crear_material("ParedDetalle", (0.8, 0.2, 0.0))

    # 3. Parámetros
    largo_recto = 10
    ancho = 4
    radio_curva = 12
    puntos_camara = [] # Para guardar la trayectoria

    # --- 4. SECCIÓN 1: TRAMO RECTO ---
    for i in range(largo_recto):
        y_pos = i * 2
        # Pared Izquierda
        bpy.ops.mesh.primitive_cube_add(location=(-ancho, y_pos, 1))
        p_izq = bpy.context.active_object
        if i % 2 == 0:
            p_izq.data.materials.append(mat_a)
        else:
            p_izq.data.materials.append(mat_b)
            p_izq.scale.z = 1.5
        
        # Pared Derecha
        bpy.ops.mesh.primitive_cube_add(location=(ancho, y_pos, 1))
        bpy.context.active_object.data.materials.append(mat_a)
        
        # Guardar punto para la cámara (centro del pasillo)
        puntos_camara.append((0, y_pos, 1.5))

    # --- 5. SECCIÓN 2: TRAMO CURVO ---
    # Punto de origen de la curva (el centro del círculo para el giro)
    cx = radio_curva
    cy = (largo_recto - 1) * 2
    
    pasos_curva = 10
    for j in range(1, pasos_curva + 1):
        # Ángulo de 180° (PI) a 90° (PI/2) para girar a la derecha
        angulo = math.pi - (j * (math.pi / 2) / pasos_curva)
        rot_z = math.pi - angulo
        
        # Pared Exterior (Izquierda)
        x_ext = cx + (radio_curva + ancho) * math.cos(angulo)
        y_ext = cy + (radio_curva + ancho) * math.sin(angulo)
        bpy.ops.mesh.primitive_cube_add(location=(x_ext, y_ext, 1), rotation=(0, 0, rot_z))
        p_curva = bpy.context.active_object
        if (largo_recto + j) % 2 == 0:
            p_curva.data.materials.append(mat_a)
        else:
            p_curva.data.materials.append(mat_b)
            p_curva.scale.z = 1.5
            
        # Pared Interior (Derecha)
        x_int = cx + (radio_curva - ancho) * math.cos(angulo)
        y_int = cy + (radio_curva - ancho) * math.sin(angulo)
        bpy.ops.mesh.primitive_cube_add(location=(x_int, y_int, 1), rotation=(0, 0, rot_z))
        bpy.context.active_object.data.materials.append(mat_a)
        
        # Guardar punto para la cámara (centro de la curva)
        px_cam = cx + radio_curva * math.cos(angulo)
        py_cam = cy + radio_curva * math.sin(angulo)
        puntos_camara.append((px_cam, py_cam, 1.5))

    # --- 6. SUELO UNIFICADO ---
    bpy.ops.mesh.primitive_plane_add(size=1, location=(radio_curva/2, cy/2 + radio_curva/2, 0))
    suelo = bpy.context.active_object
    suelo.scale.x = 30
    suelo.scale.y = 30

    # --- 7. CÁMARA Y ANIMACIÓN ---
    # Crear la ruta (Bezier Curve)
    curva_data = bpy.data.curves.new('RutaCamara', type='CURVE')
    curva_data.dimensions = '3D'
    curva_data.use_path = True
    curva_data.path_duration = 200
    
    spline = curva_data.splines.new('POLY')
    spline.points.add(len(puntos_camara) - 1)
    for idx, p in enumerate(puntos_camara):
        spline.points[idx].co = (p[0], p[1], p[2], 1.0)
    
    ruta_obj = bpy.data.objects.new('RutaObj', curva_data)
    bpy.context.scene.collection.objects.link(ruta_obj)

    # Crear Cámara
    bpy.ops.object.camera_add()
    cam = bpy.context.active_object
    bpy.context.scene.camera = cam

    # Restricción de seguimiento
    follow = cam.constraints.new(type='FOLLOW_PATH')
    follow.target = ruta_obj
    follow.use_fixed_location = True
    
    # Animar offset de 0 a 1 en 200 frames
    follow.offset_factor = 0.0
    cam.keyframe_insert(data_path='constraints["Follow Path"].offset_factor', frame=1)
    follow.offset_factor = 1.0
    cam.keyframe_insert(data_path='constraints["Follow Path"].offset_factor', frame=200)

generar_escenario() 
```
<video src="https://github.com/user-attachments/assets/8d43b07c-d40a-4278-b3e6-2b3db16e8170" controls></video>




