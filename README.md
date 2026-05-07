# 🍷 Vinheria Agnello - Monitoramento Inteligente de Ambiente

---

## 👥 Integrantes

* Pedro Alves
* Felipe Luan
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

## 📸 Simulação
 <div align="center"><img width="657" height="657" alt="circuito" src="https://github.com/user-attachments/assets/bde9af6d-82f7-42b9-b65c-dd9ae79bc652" /> </div>

🔗 **Link do Tinkercad/Wokwi:**
👉 [link #](https://wokwi.com/projects/463327858697653249)

---
## 💻 Código do Projeto dentro do arquivo .ino "Vinharia_Agnello.ino"
---

## 🎥 Vídeo Explicativo

📺 **Link do vídeo:**
👉 link #

---

## 🚧 Desafios Enfrentados

* Integração de múltiplos sensores
* Estabilização das leituras (uso de média)
* Configuração do display LCD

---

