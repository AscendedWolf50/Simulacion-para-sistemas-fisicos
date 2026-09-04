# Unidad 04


https://editor.p5js.org/builesjuanjo10/sketches/vF0st3f6p

## Descripción del Proyecto
*Ecos de Arrakis* es una experiencia audiovisual interactiva diseñada para la Web mediante p5.js y p5.sound. Funciona como un instrumento performativo (estilo *groovebox* o *Incredibox*) donde la organización temporal no está dictada por un secuenciador rígido ni un reloj global, sino que **emerge en tiempo real** gracias a la interacción de un sistema de agentes acoplados matemáticamente. 

El proyecto toma el universo de ciencia ficción de *Dune* (Frank Herbert) no solo como capa estética, sino como metáfora sistémica: la fricción, la resonancia y la comunicación a través del desierto actúan como el medio físico que acopla a las entidades sonoras.

## El Modelo de Kuramoto en el Diseño
El núcleo del proyecto es una extensión del **Modelo de Kuramoto**. Cada agente (entidad sonora) se calcula bajo la siguiente lógica:

*   **Fase ($\theta_i$):** Representa el ciclo interno del agente. Cuando la fase completa un ciclo ($2\pi$), el agente emite su sonido característico  y produce un destello visual en la arena.
*   **Frecuencia Natural ($\omega_i$):** Es el *tempo* base al que el agente "quiere" oscilar si estuviera aislado. En la interfaz, esta variable se controla directamente mediante la posición vertical (Eje Y) del agente en el canvas.
*   **Fuerza de Acoplamiento ($K$):** Representa la conductividad acústica o la tensión de la arena. Si $K$ es 0, cada agente suena a su propio ritmo (caos). Al aumentar $K$, los agentes se "escuchan" entre sí y obligan matemáticamente a sus fases a alinearse, creando ritmos unificados.
*   **Topología Espacial Extendida ($d_{ij}$):** Se introdujo una modificación al modelo original integrando la distancia euclidiana entre agentes. El acoplamiento local es inversamente proporcional a la distancia: $K_{local} = \frac{K_{global}}{1 + d_{ij} \times 0.005}$. Esto permite crear sub-grupos rítmicos arrastrando los nodos por la pantalla.

## Manual de Performance (Controles)
El sistema está diseñado para ser "tocado" en vivo. Los agentes inician silenciados para permitir la construcción progresiva de la capa sonora.

*   **`Click` o Teclas `1` al `8`:** Activa / Silencia (*Mute/Unmute*) a un agente. Al encenderlo, entra a negociar su fase con la red activa.
*   **`Arrastrar (Mouse)`:** Modifica la posición del agente. El eje **Y** altera su frecuencia natural ($\omega_i$), acelerándolo o frenándolo. El eje **X** modifica su paneo estéreo y sus distancias topológicas.
*   **`Flecha Arriba ↑` / `Flecha Abajo ↓`:** Modifica la tensión global de la arena ($K$). Úsalo para forzar transiciones entre desorden, polirritmias parciales y sincronía total.
*   **`Z` / `X`:** Disminuye / Aumenta el multiplicador global de velocidad ($\omega_{global}$).
*   **`SHIFT` + `1` al `8`:** Inyecta una perturbación individual a un agente activo, aleatorizando su fase y velocidad abruptamente.
*   **`Barra Espaciadora`:** Desata la *Tormenta de Coriolis*. Es un mecanismo de perturbación global que destruye la coherencia del sistema esparciendo las fases de todos los agentes.
*   **`H`:** Oculta/Muestra la interfaz HUD (Head-Up Display).

## Personalidades Audiovisuales
El sistema cuenta con 8 agentes divididos en 4 personalidades, cada una con un rol funcional en el espectro sonoro (diseñado mediante síntesis de ondas puras y ruido) y una representación geométrica clara:

1.  **El Martillador (Bombo / Kick):** Geometría cuadrada (Rojo). Onda Senoidal con *pitch drop* rápido. Marca las frecuencias subgraves fundamentales.
2.  **El Fremen (Shaker / Percusión aguda):** Geometría circular con partículas orbitales (Azul). Ruido blanco con envolvente muy corta. Aporta la fricción rítmica de alta frecuencia.
3.  **La Cosechadora (Bajo Sintético):** Geometría rectangular pesada (Gris/Arena). Onda de Sierra (*Sawtooth*). Su frecuencia base escala dinámicamente según el nivel de sincronía del sistema.
4.  **El Ornitóptero (Pluck / Melódico):** Geometría triangular (Amarillo). Onda Triangular. Ejecuta arpegios sobre una escala pentatónica, determinada por su posición en el eje Y.

## Estados Colectivos y Feedback
El sistema comunica su estado emergente mediante el cálculo del **parámetro de orden ($r$)**. 
*   **Desorden ($r \approx 0$):** Los agentes suenan asíncronos. El desierto está opaco y estático.
*   **Organización Parcial:** Empiezan a surgir patrones de llamada y respuesta. Se forman conexiones lumínicas visibles entre los agentes cercanos.
*   **Sincronía Estable ($r > 0.85$):** *Clímax*. Todos los agentes disparan al unísono. El cielo se oscurece, las dunas vibran agresivamente, y emerge un **Drone de Sub-Bajo** ambiental continuo que indica que el ecosistema ha alcanzado la resonancia pura.








