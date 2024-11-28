# MQTT Client Library for ESP32

## Descrição

Esta biblioteca implementa um cliente MQTT para dispositivos ESP32 utilizando o ESP-IDF. Ela fornece funcionalidades essenciais para publicar mensagens, se inscrever em tópicos e gerenciar eventos MQTT, tudo isso com uma interface simplificada orientada a objetos.

## Recursos

- **Conexão ao broker MQTT** com base no URI fornecido.
- **Publicação de mensagens** em tópicos específicos.
- **Inscrição em tópicos** para receber mensagens.
- **Callback configurável** para tratar mensagens recebidas.
- **Gerenciamento de eventos MQTT** como conexão, publicação, assinatura e recebimento de dados.


## Uso

### 1. Configuração Inicial

Inclua o cabeçalho da biblioteca no seu código:
```cpp
#include "dev_mqtt_client.hpp"
```

Crie uma instância do cliente MQTT:
```cpp
MQTTClient mqttClient("mqtt://test.mosquitto.org");
```

### 2. Iniciar o Cliente

Inicie o cliente no início do programa:
```cpp
mqttClient.start();
```

### 3. Publicar Mensagens

Para publicar mensagens em um tópico:
```cpp
mqttClient.publish("lock/test", "Hello from ESP32!");
```

### 4. Inscrever-se em Tópicos

Para se inscrever em um tópico, recomenda-se fazer isso no evento de conexão com o broker (`MQTT_EVENT_CONNECTED`). Isso garante que as assinaturas sejam reestabelecidas caso a conexão seja perdida e retomada.

Exemplo:
```cpp
case MQTT_EVENT_CONNECTED:
    ESP_LOGI(TAG, "MQTT_EVENT_CONNECTED");
    mqttClient->subscribe("lock/access/confirmation");
    mqttClient->subscribe("lock/register/start");
    break;
```

### 5. Configurar Callback para Mensagens

Defina um callback para tratar mensagens recebidas:
```cpp
void onMessageReceived(const char *topic, const char *message)
{
    // Adicione if statement para cada topico que quer tratar.
    printf("Mensagem recebida no tópico %s: %s\n", topic, message);
}

mqttClient.setMessageCallback(onMessageReceived);
```

---

## Exemplo Completo

```cpp
#include "dev_mqtt_client.hpp"

MQTTClient mqttClient("mqtt://test.mosquitto.org");

void onMessageReceived(const char *topic, const char *message)
{
    printf("Mensagem recebida no tópico %s: %s\n", topic, message);
}

extern "C" void app_main()
{
    mqttClient.setMessageCallback(onMessageReceived);
    mqttClient.start();

    // Publica uma mensagem de teste
    mqttClient.publish("lock/test", "Olá do ESP32!");
}
```

---

## Estrutura dos Arquivos

- **`dev_mqtt_client.hpp`**: Declaração da classe `MQTTClient` com todos os métodos públicos e a interface do callback.
- **`dev_mqtt_client.cpp`**: Implementação da classe, incluindo o gerenciamento de eventos e a integração com o ESP-IDF.

---

## API

### Métodos Públicos

- **`MQTTClient(const char *uri)`**
  - Construtor para inicializar o cliente com o URI do broker.
  
- **`void start()`**
  - Inicia o cliente MQTT.
  
- **`void publish(const char *topic, const char *data)`**
  - Publica uma mensagem no tópico especificado.
  
- **`void subscribe(const char *topic)`**
  - Inscreve-se no tópico especificado.
  
- **`void setMessageCallback(MessageCallback callback)`**
  - Configura o callback para mensagens recebidas.

---

## Boas Práticas

- **Gerenciar Assinaturas no Evento de Conexão:** Coloque os tópicos a serem assinados no caso do evento `MQTT_EVENT_CONNECTED`. Isso é especialmente importante para manter as assinaturas ativas após reconexões automáticas. 

---

## Log de Eventos

O cliente utiliza as funções de log do ESP-IDF para relatar eventos, como conexão, mensagens publicadas, assinaturas e erros. 

Exemplo de saída:
```
I (12345) MQTTClient: MQTT_EVENT_CONNECTED
I (12350) MQTTClient: Message received on topic: lock/test, data: Hello from ESP32!
```

---

## Dependências

- Configuração adequada do Wi-Fi para conectar ao broker MQTT.

---

