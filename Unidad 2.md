# Unidad 2: Graficación 2D - Fundamentos y Representación Matricial

Este apartado profundiza en los pilares matemáticos que sostienen la manipulación de objetos en un plano bidimensional, fundamentado en la geometría analítica y el álgebra lineal aplicada a la computación visual.

---

## 2.1. Transformación Bidimensional

En el contexto de la graficación por computadora, una transformación es un mapeo funcional que sitúa puntos de un espacio de entrada en un espacio de salida. De acuerdo con **Foley et al. (Computer Graphics: Principles and Practice)**, las transformaciones son el mecanismo fundamental para el modelado de escenas, donde cada objeto se define en su propio sistema de coordenadas local y luego se posiciona en un sistema de coordenadas global.

### 2.1.1. Traslación
La traslación es una transformación rígida que desplaza cada punto de un objeto a una distancia constante en una dirección especificada. Es una operación aditiva que no altera la forma, el tamaño ni la orientación del objeto.

* **Fundamento Matemático:**
    Dado un punto $P = (x, y)$ y un vector de desplazamiento $T = (t_x, t_y)$, el nuevo punto $P' = (x', y')$ se calcula como:
    $$x' = x + t_x$$
    $$y' = y + t_y$$

* **Ejemplo de Aplicación:**
    Si un sistema de coordenadas tiene su origen $(0,0)$ en la esquina superior izquierda (común en APIs como HTML5 Canvas o JavaFX) y se desea mover un rectángulo 50 unidades a la derecha y 30 hacia abajo:
    * $t_x = 50$
    * $t_y = 30$
    * Resultado: $(x+50, y+30)$

### 2.1.2. Escalamiento
El escalamiento modifica el tamaño de un objeto mediante la multiplicación de sus coordenadas por factores de escala. Según **Hearn & Baker**, esta operación se realiza siempre respecto al origen del sistema de coordenadas, lo que significa que el objeto no solo cambia de tamaño, sino que también cambia su posición relativa al origen a menos que este se encuentre en $(0,0)$.

* **Parámetros:**
    * $s_x$: Factor de escala en el eje horizontal.
    * $s_y$: Factor de escala en el eje vertical.
* **Tipos:**
    1.  **Uniforme:** $s_x = s_y$. Mantiene la proporción del objeto.
    2.  **Diferencial:** $s_x \neq s_y$. Distorsiona la figura (ej. convertir un cuadrado en un rectángulo).
* **Ecuaciones:**
    $$x' = x \cdot s_x$$
    $$y' = y \cdot s_y$$

### 2.1.3. Rotación
La rotación gira un objeto alrededor de un punto pivote. Por convención matemática, los ángulos positivos generan rotaciones en sentido antihorario. Según los principios descritos en **Fundamentals of Computer Graphics (Shirley & Marschner)**, la rotación requiere el uso de funciones trigonométricas derivadas del círculo unitario.

* **Fórmulas de Rotación (sobre el origen):**
    $$x' = x \cos \theta - y \sin \theta$$
    $$y' = x \sin \theta + y \cos \theta$$

* **Limitación:** Si se desea rotar sobre un punto $(x_r, y_r)$ distinto al origen, se debe realizar una secuencia: Trasladar al origen $\rightarrow$ Rotar $\rightarrow$ Trasladar de vuelta al punto original.

### 2.1.4. Sesgado (Shearing)
El sesgado o cizallamiento produce una deformación en la que las líneas paralelas permanecen paralelas, pero los ángulos internos cambian. Es útil para simular perspectivas simples o deformaciones de materiales.

* **Sesgado Horizontal ($sh_x$):**
    $$x' = x + sh_x \cdot y$$
    $$y' = y$$
    En este caso, la coordenada $x$ se altera proporcionalmente a su distancia desde el eje $X$.

---

## 2.2. Representación Matricial de las Transformaciones

Para procesar gráficos de manera eficiente, es imperativo tratar todas las transformaciones como multiplicaciones de matrices. Sin embargo, la traslación es una suma, lo que rompe la uniformidad del álgebra lineal estándar en 2D.

### Coordenadas Homogéneas
La solución, introducida en la computación gráfica por **James Blinn**, consiste en expandir el espacio de 2D a 3D, añadiendo una tercera coordenada $w$. Un punto $(x, y)$ se convierte en $(x, y, 1)$. Esto permite que la traslación se represente como una matriz de $3 \times 3$.

#### Matrices de Transformación Básicas:

1.  **Traslación:**
    $$T(t_x, t_y) = \begin{bmatrix} 1 & 0 & t_x \\ 0 & 1 & t_y \\ 0 & 0 & 1 \end{bmatrix}$$

2.  **Escalamiento:**
    $$S(s_x, s_y) = \begin{bmatrix} s_x & 0 & 0 \\ 0 & s_y & 0 \\ 0 & 0 & 1 \end{bmatrix}$$

3.  **Rotación:**
    $$R(\theta) = \begin{bmatrix} \cos \theta & -\sin \theta & 0 \\ \sin \theta & \cos \theta & 0 \\ 0 & 0 & 1 \end{bmatrix}$$

### Composición de Transformaciones
La gran ventaja de este modelo es que se pueden concatenar múltiples operaciones en una sola **Matriz de Transformación Global** ($M_g$).
$$P' = (M_3 \cdot M_2 \cdot M_1) \cdot P$$
*Nota: El orden de multiplicación importa (el álgebra de matrices no es conmutativa).*

### Ejercicio de Control con Teclas de Dirección
En el desarrollo de videojuegos o interfaces interactivas, se captura el evento del teclado para modificar la matriz de traslación en tiempo real. 

```python
# Implementación lógica en Python (Pseudocódigo)
# Matriz de identidad inicial
matrix_transform = [1, 0, 0, 
                    0, 1, 0, 
                    0, 0, 1]

def on_key_press(event):
    step = 5
    if event.key == "ARROW_UP":
        current_ty -= step
    elif event.key == "ARROW_DOWN":
        current_ty += step
    elif event.key == "ARROW_LEFT":
        current_tx -= step
    elif event.key == "ARROW_RIGHT":
        current_tx += step
    
    # Actualizar la columna de traslación en la matriz
    matrix_transform[2] = current_tx
    matrix_transform[5] = current_ty
```

# Unidad 2: Graficación 2D - Geometría de Curvas y Sistemas Fractales

Este apartado explora la representación de formas no lineales y la generación de estructuras complejas mediante algoritmos iterativos, elementos fundamentales para el diseño asistido por computadora (CAD) y la simulación de fenómenos naturales.

---

## 2.3. Trazo de Líneas Curvas

En la graficación por computadora, las curvas no se representan como una sucesión de píxeles aislados, sino mediante funciones matemáticas paramétricas. Según **Gerald Farin (Curves and Surfaces for CAGD)**, el uso de representaciones paramétricas evita los problemas de pendientes infinitas que presentan las funciones explícitas del tipo $y = f(x)$.

### 2.3.1. Bézier
Las curvas de Bézier son modelos matemáticos fundamentales en el diseño vectorial (como en los formatos SVG o fuentes PostScript). Se definen mediante un conjunto de puntos de control, donde el primero y el último son los puntos finales de la curva, y los puntos intermedios actúan como "imanes" que dirigen la curvatura.

* **Polinomios de Bernstein:** La base matemática de estas curvas son los polinomios de Bernstein. Para una curva de grado $n$, la fórmula general es:
    $$P(t) = \sum_{i=0}^{n} B_{i,n}(t) P_i, \quad t \in [0, 1]$$
* **Propiedades Críticas:**
    1.  **Envolvente Convexa:** La curva siempre se mantiene dentro del polígono formado por sus puntos de control.
    2.  **Variación Disminuida:** La curva es más suave que el polígono que la define; no oscila más que sus puntos de control.
    3.  **Control Global:** Si se mueve un solo punto de control, toda la forma de la curva se ve afectada.



### 2.3.2. B-spline (Basis Splines)
Las B-splines fueron desarrolladas para superar la rigidez del control global de las curvas de Bézier. De acuerdo con **Hughes et al. (Computer Graphics: Principles and Practice)**, las B-splines permiten el **control local**, lo que significa que el desplazamiento de un punto de control solo altera una sección específica de la curva.

* **Vector de Nudos (Knots):** La curva se divide en segmentos definidos por un vector de nudos. La continuidad entre estos segmentos (suavidad) está garantizada matemáticamente.
* **NURBS (Non-Uniform Rational B-Splines):** Es una extensión de las B-splines que permite representar de forma exacta secciones cónicas (círculos, elipses) que las B-splines estándar y las de Bézier solo pueden aproximar.

**Ejercicio: Dibujo de la animación de una curva**
Para animar el trazo de una curva en tiempo real, se utiliza un bucle que incrementa el parámetro $t$ (tiempo/progreso). En cada iteración se calcula la posición $P(t)$ y se dibuja un pequeño segmento desde $P(t - \Delta t)$ hasta $P(t)$. Esto da la impresión de que la curva "crece" sobre el lienzo.

---

## 2.4. Fractales

El término "fractal", acuñado por **Benoit Mandelbrot**, describe estructuras geométricas que poseen una complejidad infinita y un detalle autosimilar a cualquier escala. Mientras que la geometría euclidiana trata con objetos de dimensiones enteras (1D para líneas, 2D para planos), los fractales a menudo tienen **dimensiones fraccionarias**.

### Algoritmos de Generación
1.  **Sistemas de Funciones Iteradas (IFS):** Se aplican transformaciones afines (traslación, rotación, escala) de manera recursiva sobre un conjunto de puntos. El **Helecho de Barnsley** es un ejemplo clásico que imita formas botánicas.
2.  **Conjuntos de Escape (Escape-time fractals):** Se definen mediante una relación de recurrencia en el plano complejo. El **Conjunto de Mandelbrot** se define por la iteración:
    $$z_{n+1} = z_n^2 + c$$
    Si la secuencia no tiende al infinito, el punto pertenece al conjunto.
3.  **Sistemas-L (L-Systems):** Introducidos por Aristid Lindenmayer, son gramáticas formales (reemplazo de cadenas) utilizadas para modelar el crecimiento de plantas y estructuras fractales como la **Curva de Koch**.



### Aplicaciones en Graficación
* **Generación de Terrenos:** Uso de fractales de desplazamiento de punto medio para crear montañas realistas.
* **Texturizado:** Generación de patrones de nubes, mármol o fuego mediante ruido fractal (Perlin Noise).
* **Compresión de Imágenes:** Aprovechamiento de la autosimilitud para almacenar datos de imagen de forma eficiente.
# Unidad 2: Graficación 2D - Tipografía y Gestión de Texto

Este apartado final aborda la integración de elementos alfanuméricos en entornos gráficos, analizando desde la estructura matemática de los glifos hasta los algoritmos de rasterización necesarios para su visualización en dispositivos digitales.

---

## 2.5. Uso y creación de fuentes de texto

En la computación gráfica, el texto no es simplemente una cadena de caracteres, sino una colección de formas geométricas que deben ser procesadas, escaladas y renderizadas con precisión. Según **Donald Knuth (creador de TeX y Metafont)**, el diseño de fuentes es un proceso de ingeniería donde cada letra es una descripción matemática de trazos.

### Clasificación Tecnológica de las Fuentes

De acuerdo con la literatura técnica de **Adobe Systems** y los principios de **Hughes et al. (Computer Graphics: Principles and Practice)**, las fuentes se dividen en dos arquitecturas principales:

#### 1. Fuentes de Mapa de Bits (Bitmap Fonts)
Representan cada carácter como una matriz de puntos o píxeles.
* **Ventajas:** Son extremadamente rápidas de renderizar porque no requieren cálculos matemáticos previos, solo una operación de copia de bloques de bits (BitBlt).
* **Desventajas:** No son escalables. Al aumentar su tamaño, se produce un efecto de pixelación ("aliasing") severo. Además, se requiere un archivo distinto para cada tamaño y estilo (negrita, cursiva).
* **Uso actual:** Sistemas embebidos con recursos limitados, terminales de consola y estéticas de "pixel art".

#### 2. Fuentes de Contorno o Vectoriales (Outline Fonts)
Son descripciones matemáticas de la forma de cada carácter. Utilizan primitivas gráficas como líneas y, fundamentalmente, **curvas de Bézier**.
* **Formatos Estándar:** TrueType (.ttf), OpenType (.otf) y PostScript.
* **Escalabilidad:** Al ser fórmulas matemáticas, pueden escalarse a cualquier resolución sin perder nitidez.
* **Proceso de Renderizado:** El motor de fuentes lee las coordenadas de los nodos, genera el contorno y luego aplica un algoritmo de "scan-conversion" para rellenar los píxeles interiores.

---

### Elementos de Diseño y Creación de Fuentes

Para crear una fuente desde cero, el graficista debe definir métricas específicas que garanticen la legibilidad:

1.  **Cuerpo de la fuente (Em-square):** El espacio cuadrangular imaginario donde se dibuja cada glifo.
2.  **Línea de Base (Baseline):** La línea invisible sobre la que se asientan las letras.
3.  **Altura de X (X-height):** La altura de las letras minúsculas (como la 'x'), que determina en gran medida la legibilidad de la fuente.
4.  **Ascendentes y Descendentes:** Las partes de las letras que sobresalen hacia arriba (como en la 'b') o hacia abajo (como en la 'p').

#### Conceptos Avanzados de Tipografía Digital

* **Kerning (Interletraje):** Ajuste del espacio entre pares de letras específicos para evitar huecos visuales incómodos (por ejemplo, entre la 'A' y la 'V').
* **Hinting (Sugerencias de trazado):** Instrucciones adicionales incluidas en el archivo de la fuente que ayudan al rasterizador a alinear los bordes de las letras con la rejilla de píxeles del monitor. Esto es crucial en tamaños pequeños para evitar que las letras se vean borrosas.
* **Ligaduras:** La unión de dos o más caracteres en un solo glifo para mejorar la estética (ejemplo: 'fi', 'fl').

---

### Ejemplo de Estructura de un Glifo Vectorial

Si estuviéramos diseñando una letra "L" mayúscula en un sistema vectorial, la definición matemática (en pseudocódigo de comandos gráficos) sería:

```text
BEGIN_GLYPH "L"
  MOVETO(10, 100)    # Punto superior izquierdo
  LINETO(10, 10)     # Trazo vertical descendente
  LINETO(60, 10)     # Trazo horizontal base
  LINETO(60, 20)     # Grosor de la base
  LINETO(20, 20)     # Esquina interior
  LINETO(20, 100)    # Cierre del trazo vertical
  CLOSE_PATH
  FILL_COLOR(0,0,0)  # Relleno negro
END_GLYPH
```
### Bibliografía de Referencia
  **Farin, G.** *Curves and Surfaces for CAGD: A Practical Guide*. Morgan Kaufmann. (Fundamentos de Bézier y B-splines).
  
  **Mandelbrot, B. B.** *The Fractal Geometry of Nature*. W. H. Freeman. (Teoría original de fractales).
  
 **Rogers, D. F., & Adams, J. A.** *Mathematical Elements for Computer Graphics*. McGraw-Hill. (Cálculo de curvas paramétricas).
 
 **Prusinkiewicz, P., & Lindenmayer, A.** *The Algorithmic Beauty of Plants*. Springer-Verlag. (L-Systems y modelado biológico).
 
 **Watt, A.** *3D Computer Graphics*. Addison-Wesley. (Algoritmos de subdivisión y curvas). Knuth, D. E. Digital Typography. CSLI Publications. (Análisis profundo sobre la creación matemática de tipos).
 
Bringhurst, R. The Elements of Typographic Style. Hartley & Marks. (Estándar de la industria para el diseño y métricas).

Felici, J. The Complete Manual of Typography. Peachpit Press. (Enfoque en la gestión digital y software de fuentes).

Foley, J. D. Computer Graphics: Principles and Practice. Addison-Wesley. (Capítulo sobre algoritmos de rasterización de caracteres).

FreeType Project Documentation. The Design of FreeType. (Referencia técnica sobre cómo los motores modernos procesan archivos .ttf y .otf).
