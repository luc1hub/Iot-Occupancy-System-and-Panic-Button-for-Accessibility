# Iot-Occupancy-System-and-Panic-Button-for-Accessibility
The aim of this repository is to share Iot Occupancy System and Panic Button for Accessibility by a Wokwi project

# Sistema de Ocupação e Botão de Pânico para Acessibilidade (IoT - Simulação Wokwi)


---

##  1. Autor e Instituição

* **Luciana Mieko Hassunuma** — FCI / Universidade Presbiteriana Mackenzie (UPM) — 

---


##  2. Breve Descrição e Funcionamento

Este projeto apresenta a simulação de um sistema IoT integrado de detecção de ocupação e botão de pânico emergencial, desenvolvido para garantir acessibilidade e segurança imediata a pessoas com deficiência ou mobilidade reduzida em ambientes de saúde e espaços públicos. A proposta alinha-se diretamente à **Meta 3.8 dos Objetivos de Desenvolvimento Sustentável (ODS) da ONU**, promovendo o acesso universal a serviços de saúde essenciais.

O sistema opera de forma totalmente virtual na plataforma **Wokwi**, atuando em duas frentes simultâneas:

1. **Detecção Automática de Ocupação:** O sensor ultrassônico realiza a leitura constante do ambiente. Quando a presença é identificada no espaço, o ESP32 transmite periodicamente o status de ocupação via protocolo MQTT.

2. **Alerta Emergencial Prioritário:** Em situações de emergência (como quedas ou mal-estar), o acionamento do botão físico (Push-Button) ativa instantaneamente as sinalizações locais simuladas (LED/Giroflex e Buzzer) e envia um sinal de emergência crítico (QoS 1) via TCP/IP/MQTT para a central de monitoramento.

##  3. Guia do Usuário e Operação

### Modos de Operação no Simulador

* **Modo Monitoramento (Ocupação):** Transmissão contínua e não bloqueante do estado do espaço (ocupado/livre). No Wokwi, o usuário pode alterar a distância do sensor ultrassônico clicando no componente HC-SR04 e arrastando o slider de distância.
* **Modo Alerta (Emergência):** Ao clicar no botão de pânico no Wokwi:
  * O **LED / Lâmpada de Emergência** acende imediatamente no ambiente virtual.
  * O **Buzzer / Sirene** emite um alerta sonoro.
  * Uma mensagem JSON prioritária é enviada para o tópico MQTT `sistema/acessibilidade/panico`.

---

##  4. Descrição do Hardware e Componentes Simulados

### Componentes do Wokwi

* **Placa Principal:** ESP32 DevKit V1 (Processador Dual-Core Xtensa LX6, suporte a Wi-Fi nativo na simulação).
* **Sensor de Ocupação:** Sensor Ultrassônico HC-SR04 simulado.
* **Botão de Emergência:** Push-Button virtual com lógica `INPUT_PULLUP`.
* **Atuador Visual:** LED vermelho (representando a lâmpada de emergência / giroflex 12V acionado via relé).
* **Atuador Sonoro:** Buzzer piezoelétrico simulado.

### Tabela de Mapeamento de Pinos (Pinagem no ESP32 - Wokwi)

| Componente Simulado | Função | Pino / GPIO no ESP32 | Tipo de Sinal |
| :--- | :--- | :--- | :--- |
| **Push-Button** | Botão de Pânico | `GPIO 4` | Entrada Digital (`INPUT_PULLUP`) |
| **HC-SR04 (Trig)** | Disparo Ultrassônico | `GPIO 12` | Saída Digital |
| **HC-SR04 (Echo)** | Leitura do Eco | `GPIO 14` | Entrada Digital |
| **Buzzer** | Sirene de Alarme | `GPIO 13` | Saída Digital (PWM / Digital) |
| **LED** | Lâmpada de Emergência | `GPIO 2` | Saída Digital |
| **Alimentação** | VCC / GND | `3.3V` / `GND` | Barramento Elétrico Simulado |



##  5. Interfaces, Comunicação TCP/IP e Protocolo MQTT

O sistema utiliza a pilha **TCP/IP** simulada pelo ESP32 através da rede Wi-Fi virtual do Wokwi (`Wokwi-GUEST`) e executa o protocolo de camada de aplicação **MQTT** (Message Queuing Telemetry Transport) por meio da biblioteca `PubSubClient`.

### Configuração da Rede e Broker

* **Rede Simulada (Wokwi):** `Wokwi-GUEST` (Sem senha necessária)
* **Broker MQTT (Nuvem Publica):** `broker.hivemq.com` 
* **Porta TCP:** `1883`
* **Arquitetura:** Publish / Subscribe

### Estrutura de Tópicos e Payloads (JSON)

#### 1. Alerta de Pânico (Emergência)
* **Tópico:** `sistema/acessibilidade/panico`
* **QoS (Quality of Service):** `1` (At least once — garante a entrega do sinal crítico com confirmação `PUBACK`).
* **Estrutura do Payload (JSON):**
  ```json
  {
    "device_id": "esp32_wokwi_01",
    "event": "panic_alert",
    "status": "ACTIVE",
    "priority": "HIGH",
    "timestamp": 1730828640
  }
  ```

#### 2. Telemetria de Ocupação
* **Tópico:** `sistema/acessibilidade/ocupacao`
* **QoS (Quality of Service):** `0` (At most once — envio regular de dados sem overhead).
* **Estrutura do Payload (JSON):**
  ```json
  {
    "device_id": "esp32_wokwi_01",
    "occupancy": true,
    "distance_cm": 45.2,
    "timestamp": 1730828640
  }
  ```

---

---

##  6. Instruções de Reprodução no Wokwi

1. Acesse o ambiente online [Wokwi](https://wokwi.com).
2. Crie um novo projeto escolhendo a placa **ESP32**.
3. Acesse a pasta: Sistema de ocupação e botão de panico deste diretório (https://github.com/luc1hub/Iot-best-practices/blob/main/Sistema%20de%20ocupa%C3%A7%C3%A3o%20e%20bot%C3%A3o%20de%20p%C3%A2nico%20para%20acessibilidade.zip), em seguida,  Copie o conteúdo da tabela/código do arquivo `diagram.json` para a aba **diagram.json** da simulação.
4. Cole o código C++ acima no arquivo principal `sketch.ino`.
5. Clique no botão **Play / Start Simulation**.
6. **Para testar o pânico:** Clique no botão vermelho (Push-Button). O LED acenderá, o buzzer tocará e a mensagem `sistema/acessibilidade/panico` será transmitida via MQTT.
7. **Para testar a ocupação:** Clique sobre o módulo `HC-SR04` e altere o slider de distância para simular a presença de um usuário no local.
8. Para monitorar as mensagens remotamente, utilize um cliente MQTT como o [HiveMQ Web Client](http://www.hivemq.com/demos/websocket-client/) conectado a `broker.hivemq.com` na porta `8000` (WebSockets) e assine os tópicos `sistema/acessibilidade/panico` e `sistema/acessibilidade/ocupacao`.
# Projeto Wokwi

[![Simular no Wokwi](https://img.shields.io/badge/Wokwi-Simular%20Projeto-blue?style=for-the-badge&logo=arduino)](https://wokwi.com/projects/480068600839514113)

Clique no botão acima ou no player abaixo para abrir a simulação interativa:

[<img src="https://assets.wokwi.com/landing/wokwi-logo.svg" width="300" alt="Abrir Simulação no Wokwi">](https://wokwi.com/projects/480068600839514113)

---
---

## 7. Detalhamento no webClient:
Para testar a recepção das mensagens do ESP32 externamente sem precisar mexer em códigos adicionais, utilizamos um cliente MQTT baseado na web. Como o projeto está conectado ao broker público broker.hivemq.com na porta padrão 1883 (com WebSockets na porta 8000), a forma mais rápida e prática de ver os dados a chegar em tempo real é através do HiveMQ Web Websocket Client.
Seguimos estes passos simples para configurar e testar:
Acessar  o cliente web: Abra o navegador e acesse o site oficial do cliente web de testes do broker (pode pesquisar por HiveMQ Web Websocket Client ou acessar diretamente ao painel web público deles): https://www.hivemq.com/demos/websocket-client/
 	Configurar a Conexão: 
1.	No campo de endereço (Host), mantenha ou insira: broker.hivemq.com
2.	No campo de porta (Port), use a porta de WebSockets: 8000 (ou 8884 para conexão segura wss).
3.	Clique no botão de conectar (Connect).
4.	Subscrever aos tópicos do projeto: Assim que a conexão estiver ativa, procure pela seção de subscrição (Subscriptions) e adicione os dois tópicos que o ESP32 está a monitorizar e publicar:
5.	Para o sensor de ocupação: insira o tópico sistema/acessibilidade/ocupacao e clique em subscrever.
6.	Para o botão de pânico: insira o tópico sistema/acessibilidade/panico e clique em subscrever.
7.	Verificar o funcionamento: Com a simulação a correr no Wokwi e o painel web aberto, sempre que o sensor ultrassônico efetuar uma leitura ou clicar no botão de pânico virtual, os pacotes JSON (contendo as distâncias, estados de ocupação ou alertas de prioridade) vão aparecer imediatamente na tela do cliente web em tempo real.

 ---
 ---
