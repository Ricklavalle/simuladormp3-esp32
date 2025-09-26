#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET    -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

// Variables de simulación
int progress = 0;        // progreso %
bool playing = true;     // estado play/pause
int eqHeight[5] = {5,10,15,20,25};  // alturas iniciales "ecualizador"
String song = "Just the Way You Are";
String artist = "Bruno Mars";

void setup() {
  if(!display.begin(SSD1306_SWITCHCAPVCC, 0x3C)) {
    for(;;);
  }
  display.clearDisplay();
  randomSeed(analogRead(0));
}

void loop() {
  display.clearDisplay();

  // ---- Título con scroll si es largo ----
  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  int16_t x1, y1;
  uint16_t w, h;
  display.getTextBounds(song, 0, 0, &x1, &y1, &w, &h);
  static int scrollX = 0;
  if (w > SCREEN_WIDTH) {
    scrollX--;
    if (scrollX < -w) scrollX = SCREEN_WIDTH;
  } else {
    scrollX = 0;
  }
  display.setCursor(scrollX, 0);
  display.println(song);

  // Artista
  display.setCursor(0, 10);
  display.println(artist);

  // ---- Barra de progreso animada ----
  display.drawRect(0, 25, 120, 8, SSD1306_WHITE);
  display.fillRect(0, 25, map(progress, 0, 100, 0, 120), 8, SSD1306_WHITE);

  // ---- Icono Play/Pause ----
  if (playing) {
    // Triángulo play
    display.fillTriangle(110, 45, 110, 60, 120, 52, SSD1306_WHITE);
  } else {
    // Dos barras pause
    display.fillRect(110, 45, 3, 15, SSD1306_WHITE);
    display.fillRect(115, 45, 3, 15, SSD1306_WHITE);
  }

  // ---- Ecualizador animado ----
  for (int i = 0; i < 5; i++) {
    int h = eqHeight[i];
    display.fillRect(10 + i*15, 55 - h, 10, h, SSD1306_WHITE);
    // Nueva altura aleatoria para animación
    eqHeight[i] = random(5, 25);
  }

  display.display();

  // Simula avance de canción
  progress++;
  if(progress > 100) {
    progress = 0;
    playing = !playing; // alterna entre play/pause
  }

  delay(200);
}
