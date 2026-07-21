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
