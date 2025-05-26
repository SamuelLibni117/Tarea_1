#include <Arduino.h>
#include <Adafruit_NeoPixel.h>

#define PIN        5         // Pin de control de LEDs
#define NUM_LEDS   8         // Número de LEDs en la tira

Adafruit_NeoPixel strip(NUM_LEDS, PIN, NEO_GRB + NEO_KHZ800);

// Modos de animación
enum Modo {
  MODO_ARCOIRIS,
  MODO_STOP,
  MODO_INTERMITENTES,
  MODO_GIRO_IZQ,
  MODO_GIRO_DER,
  MODO_PARPADEO
};

volatile Modo modoActual = MODO_ARCOIRIS;

// Efectos
void animacionArcoiris() {
  static uint16_t j = 0;
  for (int i = 0; i < NUM_LEDS; i++) {
    strip.setPixelColor(i, strip.ColorHSV((i * 65536 / NUM_LEDS + j) % 65536));
  }
  strip.show();
  j += 256;
  vTaskDelay(100 / portTICK_PERIOD_MS);
}

void animacionStop() {
  for (int i = 0; i < NUM_LEDS; i++) {
    strip.setPixelColor(i, strip.Color(255, 0, 0));  // rojo
  }
  strip.show();
  vTaskDelay(500 / portTICK_PERIOD_MS);
}

void animacionIntermitentes() {
  static bool on = false;
  uint32_t color = on ? strip.Color(255, 255, 0) : strip.Color(0, 0, 0);
  for (int i = 0; i < NUM_LEDS; i++) {
    strip.setPixelColor(i, color);
  }
  strip.show();
  on = !on;
  vTaskDelay(500 / portTICK_PERIOD_MS);
}

void animacionGiroIzq() {
  for (int i = 0; i < NUM_LEDS / 2; i++) {
    strip.clear();
    strip.setPixelColor(i, strip.Color(255, 165, 0)); // naranja
    strip.show();
    vTaskDelay(200 / portTICK_PERIOD_MS);
  }
}

void animacionGiroDer() {
  for (int i = NUM_LEDS - 1; i >= NUM_LEDS / 2; i--) {
    strip.clear();
    strip.setPixelColor(i, strip.Color(255, 165, 0)); // naranja
    strip.show();
    vTaskDelay(200 / portTICK_PERIOD_MS);
  }
}

void animacionParpadear() {
  static bool on = false;
  uint32_t color = on ? strip.Color(0, 0, 255) : strip.Color(0, 0, 0);
  for (int i = 0; i < NUM_LEDS; i++) {
    strip.setPixelColor(i, color);
  }
  strip.show();
  on = !on;
  vTaskDelay(300 / portTICK_PERIOD_MS);
}

// Tarea FreeRTOS principal
void tareaLuces(void *pvParameters) {
  while (true) {
    switch (modoActual) {
      case MODO_ARCOIRIS: animacionArcoiris(); break;
      case MODO_STOP: animacionStop(); break;
      case MODO_INTERMITENTES: animacionIntermitentes(); break;
      case MODO_GIRO_IZQ: animacionGiroIzq(); break;
      case MODO_GIRO_DER: animacionGiroDer(); break;
      case MODO_PARPADEO: animacionParpadear(); break;
    }
  }
}

void setup() {
  Serial.begin(115200);
  strip.begin();
  strip.show();

  xTaskCreate(
    tareaLuces,
    "LucesAuto",
    2048,
    NULL,
    1,
    NULL
  );

  Serial.println("🚗 Sistema de luces del automóvil iniciado.");
}

void loop() {
  // Cambiar automáticamente el modo cada 10 segundos (simulación)
  static unsigned long lastChange = 0;
  if (millis() - lastChange > 10000) {
    modoActual = (Modo)((modoActual + 1) % 6);  // Cambia entre los 6 modos
    Serial.print("➡️ Modo cambiado a: ");
    Serial.println(modoActual);
    lastChange = millis();
  }
}
