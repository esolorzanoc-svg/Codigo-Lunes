 Visualización interactiva de interiorismo
// ============================================================

int estacion = 0;
int tiempoCambio;
int duracionEstacion = 8000;

float apertura = 0;
boolean cambioAutomatico = true;

// Paleta general
color madera;
color maderaClara;
color crema;
color verde;
color verdeOscuro;
color terracota;

// ============================================================
// SETUP
// ============================================================

void setup() {
  size(1000, 700);
  smooth(8);

  tiempoCambio = millis();

  madera = color(105, 72, 48);
  maderaClara = color(166, 125, 82);
  crema = color(239, 234, 222);
  verde = color(82, 130, 82);
  verdeOscuro = color(42, 82, 57);
  terracota = color(160, 86, 67);
}


// ============================================================
// DRAW
// ============================================================

void draw() {

  actualizarEscena();

  float luz = map(mouseY, height, 0, 25, 155);
  luz = constrain(luz, 25, 155);

  // Fondo interior
  dibujarInterior();

  // Ventanal
  dibujarPaisaje();

  // Luz natural
  dibujarLuz(luz);

  // Plantas interiores
  dibujarPlantasInterior();

  // Cortinas
  dibujarCortinas();

  // Marco del ventanal
  dibujarMarco();

  // Mobiliario
  dibujarMueble();

  // Persona
  dibujarPersona();

  // Decoración
  dibujarMesaCafe();

  // Información
  dibujarInformacion(luz);
}


// ============================================================
// INTERIOR
// ============================================================

void dibujarInterior() {

  // Pared
  noStroke();
  fill(232, 226, 214);
  rect(0, 0, width, 570);

  // Piso
  fill(139, 103, 72);
  rect(0, 570, width, 130);

  // Líneas del piso
  stroke(115, 82, 58, 70);
  strokeWeight(2);

  for (int x = 0; x < width; x += 70) {
    line(x, 570, x + 25, 700);
  }

  // Zócalo
  noStroke();
  fill(105, 75, 55);
  rect(0, 560, width, 12);

  // Lámparas decorativas
  fill(120, 85, 55);
  rect(85, 0, 4, 75);
  rect(915, 0, 4, 75);

  fill(235, 190, 110, 180);
  ellipse(87, 90, 45, 45);
  ellipse(917, 90, 45, 45);
}


// ============================================================
// ACTUALIZAR ESCENA
// ============================================================

void actualizarEscena() {

  if (cambioAutomatico &&
      millis() - tiempoCambio > duracionEstacion) {

    estacion++;

    if (estacion > 3) {
      estacion = 0;
    }

    tiempoCambio = millis();
  }

  // Cortinas controladas con mouse
  float objetivo = map(mouseX, 0, width, 0, 285);

  apertura = lerp(apertura, objetivo, 0.07);
}


// ============================================================
// PAISAJE
// ============================================================

void dibujarPaisaje() {

  // Cielo degradado
  for (int y = 100; y < 460; y++) {

    float cantidad = map(y, 100, 460, 0, 1);

    color cieloArriba;
    color cieloAbajo;

    if (estacion == 0) {
      cieloArriba = color(140, 190, 215);
      cieloAbajo = color(225, 235, 220);

    } else if (estacion == 1) {
      cieloArriba = color(65, 155, 220);
      cieloAbajo = color(190, 225, 215);

    } else if (estacion == 2) {
      cieloArriba = color(190, 165, 145);
      cieloAbajo = color(225, 210, 190);

    } else {
      cieloArriba = color(165, 190, 205);
      cieloAbajo = color(220, 225, 225);
    }

    stroke(lerpColor(cieloArriba, cieloAbajo, cantidad));
    line(170, y, 830, y);
  }

  noStroke();

  // Sol
  if (estacion != 3) {

    fill(255, 220, 130, 210);
    ellipse(710, 150, 60, 60);

    fill(255, 220, 130, 35);
    ellipse(710, 150, 120, 120);
  }

  // Montañas lejanas
  fill(150, 175, 180);

  triangle(170, 390, 320, 220, 485, 390);
  triangle(390, 390, 600, 185, 830, 390);

  // Montañas cercanas
  fill(74, 110, 92);

  triangle(170, 420, 340, 270, 510, 420);
  triangle(420, 420, 650, 245, 830, 420);

  // Vegetación / suelo
  if (estacion == 3) {

    fill(215, 225, 225);

  } else if (estacion == 2) {

    fill(157, 113, 67);

  } else {

    fill(76, 130, 75);
  }

  rect(170, 350, 660, 110);

  // Camino
  fill(190, 160, 112, 180);

  beginShape();
  vertex(465, 350);
  vertex(550, 350);
  vertex(750, 460);
  vertex(260, 460);
  endShape(CLOSE);

  // Árboles
  dibujarArboles();

  // Detalles estacionales

  if (estacion == 0) {

    dibujarFlores();

  } else if (estacion == 2) {

    dibujarHojasCayendo();

  } else if (estacion == 3) {

    dibujarNieve();
  }

  dibujarPasto();
}


// ============================================================
// ÁRBOLES
// ============================================================

void dibujarArboles() {

  float[] posiciones = {
    215, 310, 410, 510, 610, 705, 785
  };

  for (int i = 0; i < posiciones.length; i++) {

    float x = posiciones[i];

    float escala = 0.60 + (i % 3) * 0.13;

    dibujarArbol(x, 410, escala, i);
  }
}


void dibujarArbol(float x, float base,
                   float escala, int numero) {

  float alturaTronco = 105 * escala;
  float copaY = base - alturaTronco - 25 * escala;

  // Sombra
  fill(40, 45, 35, 45);
  ellipse(x, base + 5,
          115 * escala,
          20 * escala);

  // Tronco
  fill(78, 50, 32);

  rect(x - 8 * escala,
       base - alturaTronco,
       16 * escala,
       alturaTronco);

  // Luz del tronco
  fill(125, 80, 45);

  rect(x - 3 * escala,
       base - alturaTronco,
       5 * escala,
       alturaTronco);

  // Ramas
  stroke(75, 48, 30);
  strokeWeight(max(2, 5 * escala));

  line(x,
       base - alturaTronco * 0.55,
       x - 28 * escala,
       base - alturaTronco * 0.85);

  line(x,
       base - alturaTronco * 0.48,
       x + 30 * escala,
       base - alturaTronco * 0.78);

  line(x,
       base - alturaTronco * 0.32,
       x - 24 * escala,
       base - alturaTronco * 0.52);

  noStroke();

  // Invierno
  if (estacion == 3) {

    stroke(75, 48, 30);
    strokeWeight(max(2, 4 * escala));

    line(x,
         base - alturaTronco * 0.75,
         x - 38 * escala,
         base - alturaTronco - 35 * escala);

    line(x,
         base - alturaTronco * 0.72,
         x + 38 * escala,
         base - alturaTronco - 25 * escala);

    noStroke();

    return;
  }

  // Color de hojas
  color colorHojas;

  if (estacion == 0) {

    colorHojas = color(100, 175, 92);

  } else if (estacion == 1) {

    colorHojas = color(45, 125, 62);

  } else {

    colorHojas = color(210, 105 + numero * 8, 35);
  }

  // Copa principal
  fill(colorHojas, 245);

  ellipse(x,
          copaY,
          78 * escala,
          68 * escala);

  ellipse(x - 30 * escala,
          copaY + 18 * escala,
          58 * escala,
          50 * escala);

  ellipse(x + 30 * escala,
          copaY + 18 * escala,
          58 * escala,
          50 * escala);

  ellipse(x,
          copaY - 25 * escala,
          55 * escala,
          50 * escala);

  // Parte iluminada
  if (estacion == 0) {

    fill(165, 220, 130, 140);

  } else if (estacion == 1) {

    fill(100, 185, 90, 120);

  } else {

    fill(250, 180, 70, 120);
  }

  ellipse(x - 18 * escala,
          copaY - 18 * escala,
          30 * escala,
          24 * escala);
}


// ============================================================
// FLORES
// ============================================================

void dibujarFlores() {

  for (int i = 0; i < 30; i++) {

    float x = 185 + ((i * 67) % 635);
    float y = 390 + ((i * 31) % 55);

    if (i % 2 == 0) {
      fill(245, 145, 175);
    } else {
      fill(255, 220, 90);
    }

    ellipse(x, y, 8, 8);

    fill(75, 120, 65);
    rect(x - 1, y + 3, 2, 9);
  }
}


// ============================================================
// HOJAS
// ============================================================

void dibujarHojasCayendo() {

  for (int i = 0; i < 25; i++) {

    float x = 185 + ((i * 73) % 630);
    float y = 150 + ((i * 49) % 235);

    if (i % 2 == 0) {
      fill(225, 130, 35);
    } else {
      fill(170, 70, 30);
    }

    pushMatrix();
    translate(x, y);
    rotate(0.4);
    ellipse(0, 0, 13, 8);
    popMatrix();
  }
}


// ============================================================
// NIEVE
// ============================================================

void dibujarNieve() {

  for (int i = 0; i < 45; i++) {

    float x = 180 + ((i * 97) % 630);
    float y = 120 + ((i * 53) % 260);

    fill(255, 245);

    ellipse(x, y, 7, 7);
  }
}


// ============================================================
// PASTO
// ============================================================

void dibujarPasto() {

  strokeWeight(2);

  for (int i = 0; i < 100; i++) {

    float x = 175 + ((i * 73) % 650);
    float y = 410 + ((i * 31) % 45);

    if (estacion == 3) {

      stroke(175, 190, 195);

    } else if (estacion == 2) {

      stroke(125, 85, 35);

    } else {

      stroke(45, 115, 55);
    }

    line(x, y, x + 3, y - 12);
  }

  noStroke();
}


// ============================================================
// LUZ NATURAL
// ============================================================

void dibujarLuz(float luz) {

  noStroke();

  // Haz principal
  fill(255, 225, 145, luz);

  quad(
    170, 100,
    830, 100,
    920, 570,
    80, 570
  );

  // Segundo haz
  fill(255, 235, 175, luz * 0.25);

  quad(
    280, 100,
    650, 100,
    800, 570,
    200, 570
  );

  // Reflejo sobre el piso
  fill(255, 245, 190, luz * 0.35);

  ellipse(650, 520, 380, 90);
}


// ============================================================
// PLANTAS INTERIORES
// ============================================================

void dibujarPlantasInterior() {

  // Planta izquierda
  dibujarPlanta(105, 535, 1.0);

  // Planta derecha
  dibujarPlanta(890, 535, 0.85);
}


void dibujarPlanta(float x, float y, float escala) {

  // Maceta
  fill(155, 105, 72);

  beginShape();
  vertex(x - 28 * escala, y - 55 * escala);
  vertex(x + 28 * escala, y - 55 * escala);
  vertex(x + 20 * escala, y);
  vertex(x - 20 * escala, y);
  endShape(CLOSE);

  // Tierra
  fill(75, 50, 35);
  ellipse(x, y - 55 * escala,
          52 * escala,
          12 * escala);

  // Tallos
  stroke(55, 105, 60);
  strokeWeight(4 * escala);

  for (int i = 0; i < 7; i++) {

    float angulo = -1.2 + i * 0.4;

    line(
      x,
      y - 55 * escala,
      x + cos(angulo) * 55 * escala,
      y - 115 * escala
    );
  }

  noStroke();

  // Hojas
  fill(65, 125, 70);

  for (int i = 0; i < 7; i++) {

    float angulo = -1.2 + i * 0.4;

    float hojaX =
      x + cos(angulo) * 55 * escala;

    float hojaY =
      y - 115 * escala;

    ellipse(
      hojaX,
      hojaY,
      32 * escala,
      16 * escala
    );
  }
}


// ============================================================
// CORTINAS
// ============================================================

void dibujarCortinas() {

  float izquierda = 170;
  float derecha = 830;

  float arriba = 100;
  float abajo = 460;

  float anchoBase = 330;

  float anchoActual =
    anchoBase - apertura;

  // Sombra detrás
  fill(40, 20, 25, 30);

  rect(
    izquierda,
    arriba,
    anchoActual,
    abajo - arriba
  );

  rect(
    derecha - anchoActual,
    arriba,
    anchoActual,
    abajo - arriba
  );

  // Tela
  fill(155, 80, 76, 225);

  rect(
    izquierda,
    arriba,
    anchoActual,
    abajo - arriba
  );

  rect(
    derecha - anchoActual,
    arriba,
    anchoActual,
    abajo - arriba
  );

  // Pliegues
  for (int i = 0; i < 12; i++) {

    float xIzquierda =
      izquierda +
      i * anchoActual / 11;

    float xDerecha =
      derecha -
      anchoActual +
      i * anchoActual / 11;

    fill(105, 42, 45, 100);

    rect(
      xIzquierda,
      arriba,
      9,
      abajo - arriba
    );

    rect(
      xDerecha,
      arriba,
      9,
      abajo - arriba
    );

    fill(220, 130, 120, 65);

    rect(
      xIzquierda + 9,
      arriba,
      5,
      abajo - arriba
    );

    rect(
      xDerecha + 9,
      arriba,
      5,
      abajo - arriba
    );
  }

  // Sujetadores
  fill(220, 175, 90);

  ellipse(
    izquierda + anchoActual,
    285,
    22,
    22
  );

  ellipse(
    derecha - anchoActual,
    285,
    22,
    22
  );

  // Barra superior
  stroke(100, 70, 50);
  strokeWeight(9);

  line(145, 88, 855, 88);

  stroke(225, 190, 120);
  strokeWeight(3);

  line(150, 84, 850, 84);

  noStroke();
}


// ============================================================
// MARCO DEL VENTANAL
// ============================================================

void dibujarMarco() {

  noFill();

  stroke(70, 62, 52);
  strokeWeight(12);

  rect(155, 85, 690, 390);

  // Divisiones
  strokeWeight(7);

  line(500, 90, 500, 470);
  line(160, 280, 840, 280);

  noStroke();
}


// ============================================================
// SOFÁ
// ============================================================

void dibujarMueble() {

  // Sombra
  fill(45, 35, 30, 55);

  ellipse(
    500,
    595,
    370,
    38
  );

  // Base
  fill(105, 77, 58);

  rect(
    340,
    505,
    320,
    70,
    12
  );

  // Respaldo
  fill(142, 108, 82);

  rect(
    360,
    450,
    280,
    105,
    16
  );

  // Brazo izquierdo
  fill(120, 88, 67);

  rect(
    340,
    475,
    45,
    105,
    15
  );

  // Brazo derecho
  rect(
    615,
    475,
    45,
    105,
    15
  );

  // Cojines
  fill(176, 135, 103);

  rect(
    395,
    465,
    95,
    55,
    12
  );

  rect(
    505,
    465,
    95,
    55,
    12
  );

  // Cojines decorativos
  fill(190, 150, 110);

  ellipse(425, 492, 35, 35);
  ellipse(570, 492, 35, 35);

  // Patas
  fill(70, 52, 40);

  rect(375, 555, 14, 42);
  rect(610, 555, 14, 42);
}


// ============================================================
// MESA DE CAFÉ
// ============================================================

void dibujarMesaCafe() {

  // Mesa
  fill(120, 82, 52);

  ellipse(
    735,
    555,
    115,
    35
  );

  // Patas
  stroke(85, 58, 40);
  strokeWeight(6);

  line(700, 555, 710, 610);
  line(770, 555, 760, 610);

  noStroke();

  // Taza
  fill(235, 228, 211);

  ellipse(
    735,
    545,
    32,
    22
  );

  fill(75, 45, 30);

  ellipse(
    735,
    544,
    23,
    13
  );

  // Asa
  noFill();
  stroke(235, 228, 211);
  strokeWeight(4);

  arc(
    752,
    545,
    14,
    13,
    -HALF_PI,
    HALF_PI
  );

  noStroke();

  // Vapor
  stroke(220, 220, 205, 100);
  strokeWeight(2);

  noFill();

  beginShape();
  curveVertex(728, 530);
  curveVertex(728, 530);
  curveVertex(724, 520);
  curveVertex(730, 510);
  curveVertex(726, 500);
  curveVertex(726, 500);
  endShape();

  noStroke();
}


// ============================================================
// PERSONA
// ============================================================

void dibujarPersona() {

  float anchoBase = 330;

  float anchoActual =
    anchoBase - apertura;

  float bordeCortina =
    830 - anchoActual;

  float personaX =
    bordeCortina + 105;

  personaX =
    constrain(personaX, 610, 900);

  float movimiento =
    sin(millis() * 0.004) * 3;

  float y = 430 + movimiento;

  // Sombra
  fill(45, 35, 35, 55);

  ellipse(
    personaX,
    515,
    115,
    24
  );

  // Cabeza
  fill(70, 55, 45);

  ellipse(
    personaX,
    y - 150,
    42,
    42
  );

  // Cabello
  fill(45, 35, 30);

  arc(
    personaX,
    y - 158,
    43,
    35,
    PI,
    TWO_PI
  );

  // Cuello
  fill(218, 165, 120);

  rect(
    personaX - 10,
    y - 125,
    20,
    18
  );

  // Cuerpo
  fill(65, 68, 72);

  rect(
    personaX - 27,
    y - 112,
    54,
    105,
    15
  );

  // Piernas
  fill(48, 50, 53);

  rect(
    personaX - 24,
    y - 12,
    18,
    90,
    8
  );

  rect(
    personaX + 6,
    y - 12,
    18,
    90,
    8
  );

  // Zapatos
  fill(40, 35, 32);

  ellipse(
    personaX - 16,
    y + 78,
    30,
    12
  );

  ellipse(
    personaX + 15,
    y + 78,
    30,
    12
  );

  // Brazo
  stroke(65, 68, 72);
  strokeWeight(13);

  float hombroX =
    personaX - 20;

  float hombroY =
    y - 88;

  float codoX =
    bordeCortina + 55;

  float codoY =
    y - 48;

  float manoX =
    bordeCortina + 5;

  float manoY =
    220 + movimiento;

  line(
    hombroX,
    hombroY,
    codoX,
    codoY
  );

  line(
    codoX,
    codoY,
    manoX,
    manoY
  );

  noStroke();

  // Mano
  fill(225, 175, 130);

  ellipse(
    manoX,
    manoY,
    16,
    16
  );
}


// ============================================================
// INFORMACIÓN
// ============================================================

void dibujarInformacion(float luz) {

  // Panel superior
  fill(35, 45, 38, 210);

  rect(
    25,
    20,
    245,
    75,
    12
  );

  fill(245, 240, 225);

  textSize(24);
  textAlign(LEFT, TOP);

  String nombreEstacion = "";

  if (estacion == 0) {
    nombreEstacion = "PRIMAVERA";
  } else if (estacion == 1) {
    nombreEstacion = "VERANO";
  } else if (estacion == 2) {
    nombreEstacion = "OTOÑO";
  } else {
    nombreEstacion = "INVIERNO";
  }

  text(
    "OM CAFÉ",
    42,
    35
  );

  textSize(15);

  fill(215, 225, 205);

  text(
    nombreEstacion,
    42,
    66
  );

  // Instrucciones inferiores
  fill(40, 45, 40, 210);

  rect(
    25,
    625,
    950,
    50,
    12
  );

  fill(245);

  textSize(13);

  text(
    "MOUSE ← →  Cortinas",
    45,
    642
  );

  text(
    "MOUSE ↑ ↓  Intensidad de luz",
    285,
    642
  );

  text(
    "ESPACIO  Cambiar estación",
    600,
    642
  );

  text(
    "Luz: " + int(luz / 155 * 100) + "%",
    825,
    642
  );
}


// ============================================================
// CAMBIAR ESTACIÓN CON ESPACIO
// ============================================================

void keyPressed() {

  if (key == ' ') {

    estacion++;

    if (estacion > 3) {
      estacion = 0;
    }

    tiempoCambio = millis();
  }
}
