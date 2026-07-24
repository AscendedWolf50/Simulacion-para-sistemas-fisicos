# Unidad 01

## Actividad 03

### En tus propias palabras, ¿cuál es la diferencia entre una distribución uniforme y una no uniforme de números aleatorios?
En una distribucion uniforme, todos los resultados posibles tienen exactamente la misma probabilidad de ocurrir. En una distribucion no uniforme, ciertos resultados tienen mayor probabilidad de salir que otros.

## Actividad 04
``` js
function setup() {
  createCanvas(640, 240);
  background(255);
}

function draw() {
  // Distribucion en X (media en el centro, desviacion de 40)
  let x = randomGaussian(width / 2, 40);
  
  // Distribucion en Y (media en el centro, desviacion de 40)
  let y = randomGaussian(height / 2, 40);
  
  // Distribucion para el tamaño del círculo (media 15, desviación 5)
  let r = randomGaussian(15, 5);

  noStroke();
  
  fill(0, 150, 200, 30);
  
  circle(x, y, r);
}
```

## Actividad 05

``` js
let walker;

function setup() {
  createCanvas(640, 240);
  walker = new Walker();
  background(255);
}

function draw() {
  walker.step();
  walker.show();
}

class Walker {
  constructor() {
    this.x = width / 2;
    this.y = height / 2;
  }

  show() {
    stroke(0, 150);
    strokeWeight(2);
    point(this.x, this.y);
  }

  step() {
    let stepX;
    let stepY;
    let r = random(1);
    
    // 1% de probabilidad de dar un salto grande (Levy flight)
    if (r < 0.01) {
      stepX = random(-50, 50);
      stepY = random(-50, 50);
    } else {
      stepX = random(-2, 2);
      stepY = random(-2, 2);
    }

    this.x += stepX;
    this.y += stepY;
    
    this.x = constrain(this.x, 0, width);
    this.y = constrain(this.y, 0, height);
  }
}
```
### Explica por qué usaste esta técnica y qué resultados esperabas obtener.
Me pareció la forma mas clara y funcional de aplicar el concepto del Levy flight sin complicar demasiado la lógica del caminante.
Quería evitar que el caminante se quedara dando vueltas en el mismo sitio. Esperaba ver que el caminante abarcara mas zonas del lienzo.

## Actividad 06
``` js
let t = 0;

function setup() {
  createCanvas(640, 240);
}

function draw() {
  background(20);
  
  stroke(50, 255, 100); 
  strokeWeight(2);
  noFill();

  beginShape();
  let xoff = t;
  
  // Dibujamos la línea de izquierda a derecha
  for (let x = 0; x < width; x += 5) {
    // Mapeamos el ruido Perlin a la altura de la pantalla (eje Y)
    let y = map(noise(xoff), 0, 1, 50, height - 20);
    vertex(x, y);
    
    // Avanzamos en el espacio del ruido para el siguiente vértice
    xoff += 0.02; 
  }
  endShape();

  // Avanzamos en el tiempo general para que el terreno se desplace
  t += 0.01; 
}
```
### Explica por qué lo visualizaste de esa manera y qué resultados esperabas obtener.
En vez de mover un solo objeto, usé el ruido Perlin para generar un terreno 2D infinito. Use los valores de noise() para las posiciones Y de una línea de vertices, y usé una variable de tiempo global para que el terreno se desplace de forma automática hacia un lado.
Queria ver bien la diferencia con la aleatoriedad normal. Si usaba random(), la linea iba a ser puros picos locos y caoticos. Con el noise(), esperaba lograr lo que dice el documento: curvas suaves y conectadas que parecen colinas mas naturales y organicas.

## Actividad 07
``` js
let particles = [];

function setup() {
  let w = windowHeight * (9/16);
  createCanvas(w, windowHeight);
  background(15);
  
  for (let i = 0; i < 150; i++) {
    particles.push(new Particle());// Ciclo que crea particulas y las agrega al arreglo
  }
}

function draw() {
  background(15, 40); // Rastro visual tipo pantalla retro
  
  for (let p of particles) {
    p.update(); // Calcular la posicion nueva de cada una de las particulas del arreglo
    p.show(); // Dibujar en la pantalla
  }
}

class Particle {
  constructor() { // Donde nacen las particulas
    // 1. Normalidad: Aparecen concentradas en la parte superior
    this.x = randomGaussian(width / 2, width / 8);
    this.y = randomGaussian(height / 6, height / 10);
    this.tx = random(1000);
    this.ty = random(10000);
  }

  update() {
    // 2. Posibilidad:Caminata aleatoria
    let stepX = random(-1, 1);
    let stepY = random(-1, 1); // Paso al azar entre -1 y 1 para ambos ejes

    
    let probJump = 0.005; // 0.5% de probabilidad de hacer un salto
    let moveY = map(noise(this.ty), 0, 1, 0, 2); // Se hace el movimiento hacia abajo usando perlin noise

    // 3. Influencia: El usuario invierte el comportamiento del sistema
    if (mouseIsPressed) {
      probJump = 0.04; // Mas probabilidad de salto
      moveY = map(noise(this.ty), 0, 1, -2, 0); // Invierten el movimiento, se muevebn hacia arriba
    }

    // 4. Tendencia: El ruido Perlin suaviza la caminata aleatoria
    stepX += map(noise(this.tx), 0, 1, -1.5, 1.5);
    stepY += moveY;

    // 5. Excepción: Salto brusco (Lévy flight)
    if (random(1) < probJump) { // Genera un numero entre 0 a 1, si cae dentro la probabilidad del paso la particual hace un salto brusco
      stepX *= random(10, 25);
      stepY *= random(10, 25);
    }

    this.x += stepX;
    this.y += stepY;
    this.tx += 0.01;
    this.ty += 0.01;

    // Si salen de la pantalla, vuelven a su estado de "Normalidad"
    if (this.x < 0 || this.x > width || this.y < 0 || this.y > height) {
      this.x = randomGaussian(width / 2, width / 8);
      this.y = randomGaussian(height / 6, height / 10);
    }
  }

  show() {
    noStroke();
    fill(50, 255, 100, 200); // Verde neón
    rect(this.x, this.y, 4, 4); // Cuadrados tipo 8-bits
  }
}
```

https://editor.p5js.org/builesjuanjo10/sketches/J-4dNAtdp

