# 🍷 Vinheria Agnello - Monitoramento Inteligente de Ambiente

---

## 👥 Integrantes

* Pedro Alves
* Luan Felipe
* Caio Maluza
* Rafaella Pazzanese
* Anderson Marcolino
* Kauã Miranda

---

## 📌 Descrição do Projeto

Este projeto foi desenvolvido para monitorar as condições ideais de armazenamento de vinhos na **Vinheria Agnello**, garantindo qualidade e conservação do produto.

O sistema utiliza um **Arduino UNO** com sensores para medir:

* 🌡️ Temperatura
* 💧 Umidade
* 🌗 Luminosidade

E fornece:

* 🔔 Alertas sonoros (buzzer)
* 💡 Sinalização visual (LEDs)
* 🖥️ Exibição de dados em tempo real (Display LCD)

---

## 🎯 Objetivo

Garantir que o ambiente esteja dentro das condições ideais para armazenamento de vinhos:

* 🌡️ Temperatura ideal: **10°C a 15°C**
* 💧 Umidade ideal: **50% a 70%**
* 🌗 Baixa luminosidade (ambiente escuro)

---

## 🧰 Componentes Utilizados

* Arduino UNO
* Sensor de temperatura e umidade **DHT11**
* Sensor de luminosidade (**LDR**)
* Display LCD 16x2
* 3 LEDs (Verde, Amarelo, Vermelho)
* Buzzer
* Resistores (220Ω e 10kΩ)
* Protoboard e jumpers

---

## ⚙️ Funcionamento do Sistema

### 🌗 Luminosidade

| Condição    | LED         | Mensagem             | Buzzer    |
| ----------- | ----------- | -------------------- | --------- |
| Muito claro | 🔴 Vermelho | Ambiente muito claro | Ligado    |
| Meia luz    | 🟡 Amarelo  | Ambiente a meia luz  | Desligado |
| Escuro      | 🟢 Verde    | Ambiente escuro      | Desligado |

---

### 🌡️ Temperatura

| Faixa       | Status | Ação                 |
| ----------- | ------ | -------------------- |
| 10°C – 15°C | OK     | Apenas exibição      |
| > 15°C      | Alta   | LED Amarelo + Buzzer |
| < 10°C      | Baixa  | LED Amarelo + Buzzer |

---

### 💧 Umidade

| Faixa     | Status | Ação                  |
| --------- | ------ | --------------------- |
| 50% – 70% | OK     | Apenas exibição       |
| > 70%     | Alta   | LED Vermelho + Buzzer |
| < 50%     | Baixa  | LED Vermelho + Buzzer |

---

## 🧠 Lógica do Sistema

* O sistema realiza **5 leituras consecutivas** dos sensores
* Calcula a **média dos valores**
* Atualiza os dados a cada **5 segundos**
* Exibe informações no LCD de forma alternada

---

## 🔌 Montagem do Circuito

### 📍 Principais conexões:

* LDR → A0 (divisor de tensão)
* DHT11 → Pino 9
* LEDs → Pinos 11, 12, 13
* Buzzer → Pino 8
* LCD → Pinos 2 a 7

---

## ▶️ Simulação

📸 *Imagem do circuito:*
*img *

🔗 **Link do Tinkercad/Wokwi:**
👉 [link #](https://wokwi.com/projects/463327858697653249)

---

## 💻 Código do Projeto

´´´
// ================= BIBLIOTECAS =================
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <DHT.h>

// ================= LCD =================
LiquidCrystal_I2C lcd(0x27, 16, 2);

// ================= CARACTERES CUSTOM =================

// Taça
byte taca[8] = {
  B00000,
  B10001,
  B10001,
  B10001,
  B01110,
  B00100,
  B01110,
  B00000
};

// X
byte X[8] = {
  B10001,
  B01010,
  B00100,
  B00100,
  B01010,
  B10001,
  B00000,
  B00000
};

// Gota
byte gota[8] = {
  B00100,
  B01110,
  B11111,
  B11111,
  B11111,
  B11111,
  B00100,
  B00000
};

// ================= DHT22 =================
#define DHTPIN 4
#define DHTTYPE DHT22

DHT dht(DHTPIN, DHTTYPE);

// ================= PINOS =================
int ledRed = 11;
int ledYellow = 12;
int ledGreen = 13;

int buzzer = 3;

int ldrPin = A0;

// ================= CONTROLE DE TEMPO =================
unsigned long tempoAnterior = 0;
const long intervalo = 5000;

// ================= VARIÁVEIS =================
float temperatura = 0;
float umidade = 0;

int luminosidade = 0;

// ======================================================
// SETUP
// ======================================================
void setup() {

  Serial.begin(9600);

  pinMode(ledRed, OUTPUT);
  pinMode(ledYellow, OUTPUT);
  pinMode(ledGreen, OUTPUT);

  pinMode(buzzer, OUTPUT);

  lcd.init();
  lcd.backlight();

  // Caracteres personalizados
  lcd.createChar(0, taca);
  lcd.createChar(1, X);
  lcd.createChar(2, gota);

  dht.begin();

  // Tela inicial
  lcd.setCursor(0, 0);
  lcd.print("Vinheria Agnello");
  lcd.setCursor(0, 1);
  lcd.print("Inicializando");
  lcd.setCursor(14, 1);
  lcd.write(byte(0));

  delay(2000);

  lcd.clear();
}

// ======================================================
// LOOP
// ======================================================
void loop() {

  unsigned long agora = millis();

  // Atualiza a cada 5 segundos
  if (agora - tempoAnterior >= intervalo) {

    tempoAnterior = agora;

    // ==================================================
    // MÉDIA DE 5 LEITURAS
    // ==================================================

    float somaTemp = 0;
    float somaUmid = 0;
    int somaLdr = 0;

    for (int i = 0; i < 5; i++) {

      somaTemp += dht.readTemperature();
      somaUmid += dht.readHumidity();

      somaLdr += analogRead(ldrPin);

      delay(100);
    }

    temperatura = somaTemp / 5.0;
    umidade = somaUmid / 5.0;
    luminosidade = somaLdr / 5;

    // ==================================================
    // CONVERTE LUMINOSIDADE
    // ==================================================

    int porcentagemLuz = map(luminosidade, 0, 1023, 100, 0);

    // ==================================================
    // VERIFICA ERRO DHT22
    // ==================================================

    if (isnan(temperatura) || isnan(umidade)) {

      lcd.clear();

      lcd.setCursor(0, 0);
      lcd.print("Erro no DHT22");

      Serial.println("Erro no sensor DHT22");

      return;
    }

    // ==================================================
    // SERIAL MONITOR
    // ==================================================

    Serial.println("========================");

    Serial.print("Temperatura: ");
    Serial.print(temperatura);
    Serial.println(" C");

    Serial.print("Umidade: ");
    Serial.print(umidade);
    Serial.println(" %");

    Serial.print("Luminosidade: ");
    Serial.print(porcentagemLuz);
    Serial.println(" %");

    // ==================================================
    // CONTROLE GERAL
    // ==================================================

    bool alerta = false;

    // buzzer desliga no começo do ciclo
    digitalWrite(buzzer, LOW);

    // reset LEDs
    digitalWrite(ledGreen, LOW);
    digitalWrite(ledYellow, LOW);
    digitalWrite(ledRed, LOW);

    // ==================================================
    // TELA 1 - LUMINOSIDADE
    // ==================================================

    lcd.clear();

    // Ambiente escuro
    if (luminosidade > 700) {

      digitalWrite(ledGreen, HIGH);

      lcd.setCursor(0, 0);
      lcd.print("Ambiente OK");

      lcd.setCursor(0, 1);
      lcd.print("Luz:");
      lcd.print(porcentagemLuz);
      lcd.print("%");
    }

    // Ambiente a meia luz
    else if (luminosidade > 300 && luminosidade <= 700) {

      digitalWrite(ledYellow, HIGH);

      lcd.setCursor(0, 0);
      lcd.print("Ambiente");
      lcd.setCursor(0,1)
      lcd.print("Meia Luz");

      delay(1200);

      lcd.clear();

      lcd.setCursor(0, 0);
      lcd.print("Luminosidade");

      lcd.setCursor(0, 1);
      lcd.print(porcentagemLuz);
      lcd.print("%");
    }

    // Ambiente muito claro
    else {

      digitalWrite(ledRed, HIGH);

      alerta = true;
      digitalWrite(buzzer, HIGH);

      lcd.setCursor(0, 0);
      lcd.print("Ambiente");

      lcd.setCursor(0, 1);
      lcd.print("muito claro");

      lcd.setCursor(13, 1);
      lcd.write(byte(1));
    }

    delay(1200);

    // ==================================================
    // RESET LEDs
    // ==================================================

    digitalWrite(ledGreen, LOW);
    digitalWrite(ledYellow, LOW);
    digitalWrite(ledRed, LOW);

    // ==================================================
    // TELA 2 - TEMPERATURA
    // ==================================================

    lcd.clear();

    // Temperatura ideal
    if (temperatura >= 10 && temperatura <= 15) {
      digitalWrite(ledGreen, HIGH);
      lcd.setCursor(0, 0);
      lcd.print("Temperatura");

      lcd.setCursor(0, 1);
      lcd.print("OK ");
      lcd.print(temperatura, 1);
      lcd.print("C");
    }

    // Temperatura alta
    else if (temperatura > 15) {

      digitalWrite(ledRed, HIGH);

      alerta = true;
      digitalWrite(buzzer, HIGH);

      lcd.setCursor(0, 0);
      lcd.print("Temp. Alta");

      lcd.setCursor(0, 1);
      lcd.print(temperatura, 1);
      lcd.print("C");
    }

    // Temperatura baixa
    else {

      digitalWrite(ledYellow, HIGH);

      alerta = true;
      digitalWrite(buzzer, HIGH);

      lcd.setCursor(0, 0);
      lcd.print("Temp. Baixa");

      lcd.setCursor(0, 1);
      lcd.print(temperatura, 1);
      lcd.print("C");
    }

    delay(1200);

    // ==================================================
    // RESET LEDs
    // ==================================================

    digitalWrite(ledGreen, LOW);
    digitalWrite(ledYellow, LOW);
    digitalWrite(ledRed, LOW);

    // ==================================================
    // TELA 3 - UMIDADE
    // ==================================================

    lcd.clear();

    // Umidade ideal
    if (umidade >= 50 && umidade <= 70) {
      digitalWrite(ledGreen, HIGH);
      lcd.setCursor(0, 0);
      lcd.print("Umidade OK");

      lcd.setCursor(0, 1);
      lcd.print(umidade, 1);
      lcd.print("%");
      lcd.setCursor(13,1);
      lcd.write(byte(2));
    }

    // Umidade alta
    else if (umidade > 70) {

      digitalWrite(ledRed, HIGH);

      alerta = true;
      digitalWrite(buzzer, HIGH);

      lcd.setCursor(0, 0);
      lcd.print("Umidade Alta");

      lcd.setCursor(0, 1);
      lcd.print(umidade, 1);
      lcd.print("%");
      lcd.setCursor(13,1);
      lcd.write(byte(2));
    }

    // Umidade baixa
    else {

      digitalWrite(ledRed, HIGH);

      alerta = true;
      digitalWrite(buzzer, HIGH);

      lcd.setCursor(0, 0);
      lcd.print("Umidade Baixa");

      lcd.setCursor(0, 1);
      lcd.print(umidade, 1);
      lcd.print("%");
      lcd.setCursor(13,1);
      lcd.write(byte(2));
    }

    delay(1200);

    // ==================================================
    // GARANTE DESLIGAMENTO DO BUZZER
    // ==================================================

    if (!alerta) {
      digitalWrite(buzzer, LOW);
    }
  }
}
´´´
---

## 🎥 Vídeo Explicativo

📺 **Link do vídeo:**
👉 link #

---

## 🚧 Desafios Enfrentados

* Integração de múltiplos sensores
* Controle de tempo com `millis()`
* Estabilização das leituras (uso de média)
* Configuração do display LCD

---

