# Graficacion-Rodrigo-Cera.-O
# 1.5. Representación y trazo de líneas y polígonos.
![Mi Gráfico](./assets/h.png)
![Mi Gráfico](./assets/he.png)
![Mi Gráfico](./assets/hex.png)
![Mi Gráfico](./assets/hexa.png)
```
import bpy
import math

def crear_poligono_2d(nombre, lados, radio):
    # Crear una nueva malla y un nuevo objeto
    malla = bpy.data.meshes.new(nombre)
    objeto = bpy.data.objects.new(nombre, malla)
    
    # Vincular el objeto a la escena actual
    bpy.context.collection.objects.link(objeto)
    
    vertices = []
    aristas = []
    
    # Cálculo de vértices usando coordenadas polares a cartesianas
    for i in range(lados):
        angulo = 2 * math.pi * i / lados
        x = radio * math.cos(angulo)
        y = radio * math.sin(angulo)
        vertices.append((x, y, 0)) # Z = 0 para mantenerlo en 2D
        
    # Definir las conexiones (aristas) entre los vértices
    for i in range(lados):
        aristas.append((i, (i + 1) % lados))
        
    # Cargar los datos en la malla
    malla.from_pydata(vertices, aristas, [])
    malla.update()

# Limpiar la escena antes de empezar
bpy.ops.object.select_all(action='SELECT')
bpy.ops.object.delete()

# Llamada a la función: Un hexágono de radio 5
crear_poligono_2d("Poligono2D", lados=6, radio=5)
```
![Mi Gráfico](./assets/hexag.png)
![Mi Gráfico](./assets/hexago.png)
