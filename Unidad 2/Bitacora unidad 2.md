# Unidad 01

## Actividad 05 (Reto de diseño)

### Concepto
#### Tension
Quiero explorar la tensión entre la Estabilidad Organica (Homeostasis) y el Consumo Parasitario (Entropia).

#### Manifestación en el comportamiento
Es una simulacion de ecosistema celular donde una estructura de tejidos estables es infiltrada por parasitos. El sistema intentará defenderse mediante el uso de anticuerpos, pero si el parásito consume demasiado, el entorno colapsa en un tejido muerto que altera el movimiento de todos los demás.

### Ecosistema de Partículas (4 Tipos)
**Células Base (Azules):** Forman el "tejido". Tienen alta atracción mutua a media distancia para agruparse, pero se repelen a muy corta distancia para mantener volumen orgánico.

**Parásitos (Magenta):** Entidades hiperactivas y veloces. Se atraen fuertemente a las Células Base para absorberlas, pero se repelen entre sí para dispersar la infección.

**Anticuerpos (Cian):** Los protectores del sistema. Orbitan cerca de las Células Base (atracción leve) y son atraídos agresivamente por los Parásitos para neutralizarlos.

**Tejido Necrótico / Basura (Gris):** Células Base corrompidas. Pierden toda velocidad (fricción altísima) y repelen fuertemente a las Células vivas y a los Anticuerpos, dañando la geometría del sistema.

### Justificacion
- Seleccione una relación asimétrica entre Parásito y Célula porque quiero hacer perceptible la "depredacion". Espero que produzca órbitas de persecución destructiva.

- Seleccione la población de Tejido Necrótico con fricción máxima porque quiero hacer perceptible la degradación del entorno. Espero que produzca barreras físicas inservibles que aíslen a los sobrevivientes.

  ### Codigo
```js
let particles = [];
let numCelulas = 90;
let numParasitos = 40;   // Aumentado para generar una invasión real
let numAnticuerpos = 20; // Reducido para que la defensa esté en desventaja

function setup() {
  createCanvas(windowHeight, windowHeight);
  colorMode(HSB, 360, 100, 100, 100);
  
  // Inicializar poblaciones
  for (let i = 0; i < numCelulas; i++) {
    particles.push(new Particle(random(width), random(height), "celula"));
  }
  for (let i = 0; i < numParasitos; i++) {
    particles.push(new Particle(random(width), random(height), "parasito"));
  }
  for (let i = 0; i < numAnticuerpos; i++) {
    particles.push(new Particle(random(width), random(height), "anticuerpo"));
  }
}

function draw() {
  blendMode(BLEND);
  background(240, 80, 5, 20); // Fondo oscuro con rastro suave
  
  // Capa 1: Dibujar conexiones moleculares solo entre Células Base vivas
  blendMode(SCREEN);
  for (let i = 0; i < particles.length; i++) {
    if (particles[i].tipo === "celula") {
      particles[i].dibujarEnlaces(particles);
    }
  }
  
  // Capa 2: Calcular interacciones cruzadas (Matriz Particle Life) y actualizar
  for (let p of particles) {
    p.aplicarMatrizFuerzas(particles);
    p.update();
    p.checkEdges();
    p.show();
  }
}

class Particle {
  constructor(x, y, tipo) {
    this.position = createVector(x, y);
    this.velocity = p5.Vector.random2D().mult(random(0.5, 1.5));
    this.acceleration = createVector(0, 0);
    this.tipo = tipo;
    this.configurarPropiedades();
  }

  configurarPropiedades() {
    // Configuración física y visual según su rol en la contradicción
    if (this.tipo === "celula") {
      this.topSpeed = 2.0;
      this.maxForce = 0.15;
      this.hue = 200; // Azul orgánico
      this.radius = 6;
    } else if (this.tipo === "parasito") {
      this.topSpeed = 4.5; // Muy rápidos
      this.maxForce = 0.35;
      this.hue = 330; // Magenta parasitario
      this.radius = 8;
    } else if (this.tipo === "anticuerpo") {
      this.topSpeed = 3.5;
      this.maxForce = 0.25;
      this.hue = 160; // Cian inmunológico
      this.radius = 5;
    } else if (this.tipo === "necrotico") {
      this.topSpeed = 0.2; // Casi inmóviles, representan muerte/entropía
      this.maxForce = 0.05;
      this.hue = 0; // Gris/Sin matiz saturado
      this.radius = 7;
    }
  }

  update() {
    this.velocity.add(this.acceleration);
    this.velocity.limit(this.topSpeed);
    this.position.add(this.velocity);
    this.acceleration.mult(0); // Limpiar fuerzas
  }

  applyForce(force) {
    this.acceleration.add(force);
  }

  aplicarMatrizFuerzas(poblacion) {
    let fuerzaTotal = createVector(0, 0);
    
    for (let otra of poblacion) {
      if (otra === this) continue;
      
      let d = p5.Vector.dist(this.position, otra.position);
      let radioInteraccion = 100;
      let nucleoExclusion = 20; // Evita que se superpongan de forma irreal
      
      if (d < radioInteraccion) {
        let dir = p5.Vector.sub(otra.position, this.position);
        dir.normalize();
        
        // 1. Núcleo duro de exclusión (Física básica para todos)
        if (d < nucleoExclusion) {
          let fuerzaRepulsion = map(d, 0, nucleoExclusion, -this.topSpeed * 2, 0);
          fuerzaTotal.add(dir.copy().mult(fuerzaRepulsion));
          
          // DETECCION DE COLISIÓN CONCEPTUAL (Infección y Defensa)
          this.evaluarColisiones(otra);
          continue; 
        }
        
        // 2. Fuerzas de la Matriz Compleja (Zonas externas)
        let factorFuerza = 0;
        
        if (this.tipo === "celula") {
          if (otra.tipo === "celula") factorFuerza = 0.8;    // Atracción mutua (Cohesión)
          if (otra.tipo === "parasito") factorFuerza = -1.5; // Huida extrema (Pánico)
          if (otra.tipo === "necrotico") factorFuerza = -0.5;// Evitan el tejido muerto
        } 
        else if (this.tipo === "parasito") {
          if (otra.tipo === "celula") factorFuerza = 2.0;    // Atracción predatoria alta
          if (otra.tipo === "parasito") factorFuerza = -0.6; // Repulsión mutua (Dispersión)
          if (otra.tipo === "anticuerpo") factorFuerza = -1.2;// Huyen de su cazador
        } 
        else if (this.tipo === "anticuerpo") {
          if (otra.tipo === "parasito") factorFuerza = 2.5;  // Atracción agresiva al enemigo
          if (otra.tipo === "celula") factorFuerza = 0.4;    // Orbitan el tejido vivo
        }
        else if (this.tipo === "necrotico") {
          factorFuerza = -0.2; // Indiferentes, repelen sutilmente a todo lo vivo
        }
        
        // Atenuación de la fuerza según la distancia
        let intensidad = map(d, nucleoExclusion, radioInteraccion, factorFuerza, 0);
        fuerzaTotal.add(dir.mult(intensidad));
      }
    }
    
    fuerzaTotal.limit(this.maxForce);
    this.applyForce(fuerzaTotal);
  }

  evaluarColisiones(otra) {
    // Si un parásito muerde una célula viva, la corrompe en tejido necrótico
    if (this.tipo === "parasito" && otra.tipo === "celula") {
      if (random(1) < 0.01) { 
        otra.tipo = "necrotico";
        otra.configurarPropiedades();
      }
    }
    // Si un anticuerpo toca a un parásito, tiene probabilidad de destruirlo
    if (this.tipo === "anticuerpo" && otra.tipo === "parasito") {
      // Balanceo: Reducido de 0.02 a 0.01 para que los parásitos vivan más tiempo
      if (random(1) < 0.01) {
        otra.tipo = "celula"; // Vuelve a ser célula limpia
        otra.configurarPropiedades();
      }
    }
  }

  dibujarEnlaces(poblacion) {
    let rangoEnlace = 50;
    for (let otra of poblacion) {
      if (otra.tipo === "celula" && otra !== this) {
        let d = p5.Vector.dist(this.position, otra.position);
        if (d < rangoEnlace) {
          let alpha = map(d, 0, rangoEnlace, 60, 0);
          stroke(this.hue, 80, 100, alpha);
          line(this.position.x, this.position.y, otra.position.x, otra.position.y);
        }
      }
    }
  }

  checkEdges() {
    if (this.position.x < 0) this.position.x = width;
    if (this.position.x > width) this.position.x = 0;
    if (this.position.y < 0) this.position.y = height;
    if (this.position.y > height) this.position.y = 0;
  }

  show() {
    noStroke();
    let flick = noise(frameCount * 0.05 + this.position.x);
    
    if (this.tipo === "celula") {
      fill(this.hue, 80, 90, 70);
      circle(this.position.x, this.position.y, this.radius);
    } 
    else if (this.tipo === "parasito") {
      // Apariencia amenazante
      push();
      translate(this.position.x, this.position.y);
      rotate(this.velocity.heading());
      fill(this.hue, 95, 100, 80 + flick * 20);
      triangle(this.radius * 1.5, 0, -this.radius * 0.8, -this.radius * 0.6, -this.radius * 0.8, this.radius * 0.6);
      pop();
    } 
    else if (this.tipo === "anticuerpo") {
      fill(this.hue, 90, 100, 90);
      push();
      translate(this.position.x, this.position.y);
      rotate(QUARTER_PI);
      rect(-this.radius/2, -this.radius/2, this.radius, this.radius);
      pop();
    }
    else if (this.tipo === "necrotico") {
      fill(0, 0, 30, 50);
      rect(this.position.x - this.radius/2, this.position.y - this.radius/2, this.radius, this.radius);
    }
  }
}


```

https://editor.p5js.org/builesjuanjo10/sketches/oNSpaMFAf

### Autoevaluación - Unidad 2: Reto de diseño

| Criterio | Peso | Valoración | Aporte |
| :--- | :---: | :---: | :---: |
| **La intención es clara y perceptible en el comportamiento.** | 20% | 100% | 20% |
| **Los tipos, cantidades, matriz y parámetros están justificados desde la intención.** | 25% | 100% | 25% |
| **Comprendo y puedo modificar el funcionamiento técnico del sistema.** | 20% | 100% | 20% |
| **El sistema produce variaciones con una identidad reconocible.** | 15% | 100% | 15% |
| **Experimenté, comparé, seleccioné y descarté con criterios claros.** | 10% | 100% | 10% |
| **Puedo distinguir y sustentar lo diseñado y lo emergente.** | 10% | 100% | 10% |
| **PUNTAJE TOTAL** | **100%** | | **100%** |





