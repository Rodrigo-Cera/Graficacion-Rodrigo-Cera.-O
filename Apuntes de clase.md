# Graficacion-Rodrigo-Cera.-O
# 1.5. Representación y trazo de líneas y polígonos.
![Mi Gráfico](./assets/h.png)
![Mi Gráfico](./assets/he.png)
![Mi Gráfico](./assets/hex.png)
![Mi Gráfico](./assets/hexa.png)
## Codigo listo para copiar
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

# Práctica 1: Geometría Generativa con Python en Blender
### Esta práctica demuestra cómo utilizar la programación para crear geometría compleja basada en patrones matemáticos. En este caso, desarrollamos la figura de la "Flor de la Vida" automatizando la creación de círculos periféricos alrededor de un origen. 
### La Base MatemáticaPara posicionar los círculos, no utilizamos coordenadas cartesianas x, y  directamente, sino coordenadas polares (sen,cos y angulo). 
### Para que Blender pueda procesarlas, el script realiza la siguiente conversión: x = r * cos(angulo), y = r * sen(angulo). Donde r es el radio y lo de dentro de parentésis es el ángulo en radianes. Como el círculo completo tiene 360°, utilizamos math.radians() para convertir los grados a un formato que Python pueda interpretar.
### El siguiente código incluye la configuración del entorno, la creación del círculo central y el ciclo while solicitado.
```
import bpy
import math

# 1. Configuración del entorno: Limpiar la escena
# Selecciona todos los objetos y los elimina para evitar encimarlos [cite: 18, 26, 28]
bpy.ops.object.select_all(action='SELECT')
bpy.ops.object.delete()

# 2. Definición de variables [cite: 19, 30]
radio = 3  # Distancia desde el origen [cite: 31]
angulo_actual = 0  # Punto de partida en grados [cite: 32]
paso_angular = 60  # Paso de 60° para obtener 6 círculos uniformes [cite: 15, 33]

# 3. Trazado del origen: Círculo Central
# Se crea la primitiva en el centro exacto de la escena (0,0,0) [cite: 20, 35, 36]
bpy.ops.mesh.primitive_circle_add(radius=radio, location=(0, 0, 0), vertices=64)

# 4. Ciclo WHILE para completar el patrón (El Reto)
# Se ejecuta mientras el ángulo actual sea menor a 360 grados [cite: 94, 95]
while angulo_actual < 360:
    # Calcular la nueva posición (x, y) usando el ángulo actual [cite: 96]
    x = radio * math.cos(math.radians(angulo_actual))
    y = radio * math.sin(math.radians(angulo_actual))
    
    # Llamar a la función de Blender para añadir el círculo en la nueva ubicación [cite: 97]
    bpy.ops.mesh.primitive_circle_add(radius=radio, location=(x, y, 0), vertices=64)
    
    # Actualización de estado: Incrementar el ángulo actual [cite: 99]
    # Sumamos el paso (60°) en cada vuelta para evitar un bucle infinito [cite: 99, 100]
    angulo_actual += paso_angular

print("Patrón generado exitosamente.")
```
## Una vez escrito el codigo en blender pasamos a darle a run script, con esto nos generará la siguiente figura("Flor de vida").
![Mi Gráfico](./assets/Flor.png)
