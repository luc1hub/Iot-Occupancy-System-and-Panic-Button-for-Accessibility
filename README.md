# Iot-best-practices
The aim of this repository is to share Iot best practices by an article, a project and testing new technologies
# Sistema de Ocupação e Botão de Pânico para Acessibilidade (IoT)

Proposta de sistema embarcado IoT focado em segurança, inclusão e acessibilidade para pessoas com deficiência ou mobilidade reduzida em ambientes de saúde e espaços públicos, alinhado à **Meta 3.8 dos Objetivos de Desenvolvimento Sustentável (ODS) da ONU**.

---

## 👥 Autores
* **Luciana Mieko Hassunuma** - UPM 


**Instituição:** Faculdade de Computação e Informática - Universidade Presbiteriana Mackenzie (UPM)

---

## 📌 Funcionalidades
* **Detecção Automática de Ocupação:** Leitura de presença por sensor ultrassônico HC-SR04.
* **Alerta de Emergência Prioritário:** Interrupção via Push-Button para disparo de socorro imediato.
* **Atuação Local:** Sinalização visual (LED/Giroflex via Relé) e sonora (Buzzer).
* **Telemetria Remota:** Comunicação assíncrona via protocolo MQTT (QoS 1 para emergências).

---

## 🛠️ Hardware e Ferramentas

| Categoria | Item | Descrição |
| :--- | :--- | :--- |
| **Microcontrolador** | ESP32 | Arquitetura Dual-Core Xtensa LX6 com Wi-Fi nativo |
| **Sensores** | HC-SR04 | Sensor Ultrassônico de Ocupação |
| | Push-Button | Botão de Pânico para acionamento manual |
| **Atuadores** | LED / Relé 5V | Sinalizador visual de emergência |
| | Buzzer KPI-2911L | Sinalizador sonoro local |
| **Software / IDE** | Arduino IDE / C++ | Programação do firmware embarcado |
| **Simulador** | Wokwi | Validação lógica e de circuitos virtuais |
| **Protocolo** | MQTT | Biblioteca `PubSubClient` para envio de mensagens JSON |

---

## 📡 Tópicos MQTT e Payloads

### 1. Emergência / Pânico
* **Tópico:** `sistema/acessibilidade/panico`
* **QoS:** `1` (At least once)
* **Payload JSON:**
  ```json
  {
    "device_id": "esp32_acc_01",
    "event": "panic_alert",
    "status": "ACTIVE",
    "timestamp": 1730828640
  }
### 2. Ocupação do Espaço
* **Tópico:** `sistema/acessibilidade/ocupacao`
* **QoS:** `0` (At most once)
* **Payload JSON:**
```json
{
  "device_id": "esp32_acc_01",
  "occupancy": true,
  "distance_cm": 45.2
}
