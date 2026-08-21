# Unidad 03


### 1. Instrumento funcional y publicado

* **URL Pública:** `https://ascendedwolf50.github.io/Simulacion_RetoUnidad3/`
* **Repositorio:** `https://github.com/AscendedWolf50/Simulacion_RetoUnidad3.git`

### 2. Mapa del Sistema

Este es el esquema de dónde vive cada responsabilidad dentro de la arquitectura de la aplicación:

* **Estado Global (State):** Definido en `parameters.js`. Aquí se declaran todos los *uniforms* que comunican la CPU con la GPU (ej. `timeScale`, `returnForce`, `colorTint`).


* **Fuerzas (Compute Shader):** Definidas en `createSimulation.js` dentro del nodo `updateParticles`. Se calculan vectorialmente fuerzas como el viento, la atracción radial, el vórtice, la onda expansiva y el retorno al origen.


* **Integración (Física):** También en `createSimulation.js`. Utiliza el método de Euler semi-implícito: se suman todas las fuerzas, se multiplican por el delta de tiempo (`dt`) para actualizar la velocidad (`v.addAssign(force.mul(dt))`), y luego se actualiza la posición limitando la velocidad máxima.


* **Render (Fragment/Vertex Shader):** Configurado en `createSimulation.js` mediante `SpriteNodeMaterial`. Aquí se define la variación de tamaño multiplicando `params.particleSize` por `params.sizeMultiplier`, y se aplica el tinte de color sobre las ondas senoidales base (`params.colorTint`).


* **Controles e Interpolación:** Manejados en `main.js`. Captura los eventos del teclado (`keydown`/`keyup`) y utiliza un *Frame Loop* para hacer decaimiento (decay) o interpolación lineal (`.lerp()`) de los parámetros antes de enviarlos a la GPU, logrando transiciones suaves.




### 3. Ficha de Fuerzas y Mecánicas del Sistema

En mi instrumento he diseñado y aislado las siguientes fuerzas y comportamientos. Cada una responde a una intención específica para la interpretación de *LesAlpx*:

#### A. Imán Colapsador (Agujero Negro / Tecla C)

* **Descripción direccional:** Sobrescribe la fuerza radial normal para generar una atracción extrema e instantánea hacia el punto del atractor (el puntero).
* **Ecuación base:** `radialForce = radialDirection * radialStrength / distance^2`.


* **Parámetros involucrados:** Se manipulan desde `main.js`. `radialStrength` salta a 150.0 y el `dragCoefficient` sube a 0.2 para frenar un poco las partículas y evitar que salgan disparadas por el exceso de velocidad.


* **Decisión de diseño:** Su función en el *score* es generar acumulación y tensión extrema (buildup). Las partículas se concentran en una esfera densa justo antes de un "drop" musical.

#### B. Onda Expansiva (Shockwave / Tecla E)

* **Descripción direccional:** Fuerza de repulsión que empuja las partículas hacia afuera desde el centro exacto del espacio `(0,0,0)`, perdiendo fuerza a medida que se alejan.
* **Ecuación base:** `shockForce = (p / centerDist) * shockwaveStrength / centerDist`.


* **Parámetros involucrados:** `shockwaveStrength` ajustado a 50.0 para lograr un latigazo violento. Se controla mediante una envolvente (decay) en `main.js`.


* **Decisión de diseño:** Es el impacto visual para los estallidos rítmicos o rupturas de la canción. Trabaja en perfecta sintonía al soltar el Imán Colapsador.

#### C. Retorno al Origen (Recall / Tecla B)

* **Descripción direccional:** Cada partícula calcula un vector de resorte que apunta directamente a su coordenada exacta de nacimiento.
* **Ecuación base:** `returnForce = (originalPos - currentPos) * returnForce`.


* **Parámetros involucrados:** `returnForce` con magnitud de 8.0, gobernado por el interruptor suave `returnEnabled`.


* **Decisión de diseño:** Produce una reestructuración instantánea hacia el bloque cúbico original. Útil para los cortes en seco de la pista, dando la sensación de que el tiempo se rebobina.

#### D. Malla Elástica (Grid / Tecla G)

* **Descripción direccional:** Interrumpe el movimiento libre calculando la intersección de grilla más cercana `(gridTarget)` y aplicando una fuerza de resorte hacia ese punto exacto.
* **Ecuación base:** `displacement = currentPos - gridTarget` | `springForce = displacement * -1.0 * elasticConstant`.


* **Parámetros involucrados:** `elasticConstant` fijado en 3.5 y un `gridSize` de 0.5.


* **Decisión de diseño:** Convierte la nube caótica en un enrejado tridimensional ordenado. Lo utilizo para momentos donde la canción adquiere un patrón rítmico muy marcado y estructurado.

#### E. Turbulencia de Fluido (Tecla T)

* **Descripción direccional:** Fuerza basada en ruido que empuja las partículas en direcciones espirales continuas, calculando senos y cosenos cruzados en los ejes X, Y y Z en función del tiempo.
* **Ecuación base:** `turbForce = vec3(sin(y)*cos(z), sin(z)*cos(x), sin(x)*cos(y))`.


* **Parámetros involucrados:** `turbulenceStrength` (3.5) y `turbulenceFrequency` (0.8).


* **Decisión de diseño:** Mantiene la simulación viva durante las secciones atmosféricas o de intro de la pieza musical, dando una sensación orgánica.

#### F. Modificadores de Estado (No-Físicos)

Aunque no son fuerzas vectoriales en la integración física, diseñé estos modificadores clave para la interpretación:

* **Cámara Lenta (Shift):** Modifica el tiempo del sistema `(timeScale = 0.3)` y aumenta la fricción `(drag = 0.4)` desde la CPU. Suspende el movimiento para enfatizar vacíos armónicos.


* **Pulso (Tecla X) y Paleta de Color (Tecla V):** Alteran el tamaño (`sizeMultiplier`) y el tinte base (`colorTint`) directamente en el paso de renderizado (Fragment/Vertex Shader). Decisión clave de diseño: aislarlos de la física evitó recargar el *Compute Shader*, permitiendo que reaccionen a mi percusión sobre el teclado sin sacrificar rendimiento.


### 4. Registro de Pruebas

* **Pruebas Base (LAB):** Se verificó el funcionamiento aislado usando los botones del panel (1 al 5): Inercia, Viento (+X), Atracción, Repulsión y Vórtice. Todas respondieron según la predicción física.


* **Prueba Específica (El fallo de la GPU):**
* *Hipótesis:* Quería agregar un multiplicador de tamaño (Pulse) en el *Compute Shader* e intenté crear un buffer extra de memoria para no recalcular posiciones originales.
* *Observación:* Al correr la prueba, las partículas desaparecieron por completo.
* *Análisis:* WebGPU falló en silencio. Descubrí que alterar la arquitectura de buffers dentro de los nodos TSL rompía la compilación.
* *Corrección:* Revertí el cálculo físico a su estado funcional. Para solucionar el problema, saqué el cálculo del tamaño y el color de las fuerzas físicas y lo llevé al *Render/Material*, controlando las variaciones (Pulso y ColorTint) desde JavaScript plano en `main.js`.





### 5. Score Visual e Interpretación para *LesAlpx*

*(Sugerencia: Acompaña este texto con un diagrama lineal o dibujo en tu bitácora)*

* **Intención general:** Representar el viaje del track desde una dispersión atmosférica, pasando por una acumulación de energía geométrica, hasta un estallido caótico y un regreso al orden.
* **Tramos y Controles:**
* **0:00 - 1:15 (Atmósfera inicial):** Modo LAB / Base. Velocidad muy baja (`timeScale = 0.3`). Fluido orgánico utilizando Turbulencia (T) suave.
* **1:15 - 2:30 (Entrada del beat y bajo):** Cambio a modo PERFORMANCE (`P`). Golpes rítmicos en la tecla de Pulso (`X`) siguiendo los kicks.
* **2:30 - 4:00 (Acumulación y tensión):** Activación sostenida del Imán Colapsador (`C`) + Vórtice para concentrar la masa en un punto denso.
* **4:00 (El "Drop" principal):** Soltar `C`. Disparo de Onda Expansiva (`E`) + Cambio brusco de paleta de color (`V`) al color rojo fuego.
* **Silencios/Cortes (Micromomentos):** Uso de la tecla de Retorno (`B`) para atrapar las partículas en el aire justo cuando la música hace silencios abruptos, alternando con Cámara Lenta (`Shift`) para suspender el tiempo.



### 6. Bitácora de IA

* **Objetivo de uso:** Asistencia en la formulación de sintaxis TSL para Three.js y estrategias de interpolación de color.
* **Prompts clave:** *"Agrega un atajo para cámara lenta con Shift"*, *"Las partículas desaparecieron tras modificar los buffers, ayúdame a identificar el error en WebGPU"*, *"Añade un multiplicador de color tintable sin tocar el compute shader"*.
* **Cambios aceptados:** La implementación del método `.lerp()` en `main.js` para suavizar el cambio de paleta de colores entre un frame y otro, y la envolvente matemática (decay) que hace que el tamaño retorne a 1.0 después de pulsar la tecla X.


* **Correcciones y Descartes:** Rechacé una propuesta de IA que modificaba la estructura de inicialización de los `instancedArray` (lo cual crasheaba el motor físico). Tomé la decisión de mantener estrictamente la arquitectura base para el motor matemático y operar las variables estéticas puramente como parámetros uniformes de entrada.









