# ESP32 - Proyecto Individual

En el siguiente documento se presenta el proyecto individual para la materia "Sistemas Embebidos", donde se muestra la creación de un comedero para mascotas pequeñas, através de la aplicación Telegram, usando el microcontrolador Nodemcu Esp32 que se conecta por WiFi y controla un servomotor.

## Hardware utilizado en esta práctica

- 1 módulo de desarrollo ESP-32
- 1 cable micro USB.
- 3 cables de puente
- 1 Servomotor
- Recipiente


## Creación

![figura](https://scontent.fntr1-1.fna.fbcdn.net/v/t1.15752-9/298228995_3242181346045288_5904203112982760754_n.jpg?_nc_cat=103&ccb=1-7&_nc_sid=ae9488&_nc_eui2=AeFruiAdQoCwjfPo-DU4eT-Fnrw3qP7orGaevDeo_uisZtby1h5KJD5oYupyUIwnG0jvQ_k8aURNcXzZGaw0DPcY&_nc_ohc=oYSn8Ew2MXYAX8se56a&_nc_ht=scontent.fntr1-1.fna&oh=03_AVJWxMqufgSi_erkabadpR4rN31ETgS0274QJf5UFndZZQ&oe=631F0875)
![figura](https://scontent.fntr1-1.fna.fbcdn.net/v/t1.15752-9/297819804_876591006649503_1161582716574909221_n.jpg?_nc_cat=107&ccb=1-7&_nc_sid=ae9488&_nc_eui2=AeGFDWhPVGD0HXhsTLheYxGhoEFbAEfTeNegQVsAR9N41yzj3tOWW3W6ISGVwwlYnahIC8vajXIdZq6hGaF6M9VT&_nc_ohc=0bWwmynJOdQAX9z9uqI&_nc_ht=scontent.fntr1-1.fna&oh=03_AVLNb6tcv44vKNFGd0dUXqySU44Nm7m-ClJm429h21ao2w&oe=631DA930)
![figura](https://scontent.fntr1-1.fna.fbcdn.net/v/t1.15752-9/298185765_859583595003789_6079588330365081361_n.jpg?_nc_cat=107&ccb=1-7&_nc_sid=ae9488&_nc_eui2=AeGomy04nTqDxmTwdMpTypeXD_UBj4sUgvwP9QGPixSC_JtuLC5KSzgY7w4tlp3OApELiAr5YsVKiKKHBcLqUZSP&_nc_ohc=Oq2qXou89rcAX_VsOk-&_nc_ht=scontent.fntr1-1.fna&oh=03_AVIEakCrkwUtbCgs5AOh0iiuS7nBdhErDfjDJs3eR-sMQA&oe=631F2CE0)

## Diagrama de cableado:
![cableado](https://github.com/electrodeuna/feed-wifi-esp32/blob/main/diagram.png?raw=true)


## ESP32 CODE
 
```c++
#include <WiFi.h>
#include <WiFiClientSecure.h>
#include <Servo.h>
#include <UniversalTelegramBot.h>
#include <ArduinoJson.h>

Servo myservo;  // crea el objeto servo

// GPIO de salida del servo
static const int servoPin = 13;

// Reemplazar con tus credenciales de Wifi
const char* ssid     = "TU-WIFI";
const char* password = "TU-PASSWORD-WIFI";

// Inicializa Bot Telegram
#define BOTtoken "TU-TOKEN-BOT"  // Tu Bot Token (Obtener de Botfather)

#define CHAT_ID "TU-CHAT-ID"

WiFiClientSecure client;

UniversalTelegramBot bot(BOTtoken, client);

void handleNewMessages(int numNewMessages) {

  for (int i=0; i<numNewMessages; i++) {
    // Chat id of the requester
    String chat_id = String(bot.messages[i].chat_id);
    if (chat_id != CHAT_ID){
      bot.sendMessage(chat_id, "Usuario no autorizado", "");
      continue;
    }

    String text = bot.messages[i].text;

    if (text == "/comida") {
      bot.sendMessage(chat_id, "Alimentando", "");
      myservo.write(90);             
      delay(500);                       
      myservo.write(0);              
    }
  }
}

void setup() {
  Serial.begin(115200);

  myservo.attach(servoPin);  // vincula el servo en el servoPin

  // Conecta a red WiFi con SSID y password
  Serial.print("Conectado a ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);
  client.setCACert(TELEGRAM_CERTIFICATE_ROOT);
  
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  // Muestra IP local 
  Serial.println("");
  Serial.println("WiFi conectado.");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());

  bot.sendMessage(CHAT_ID, "Bot iniciado", "");
}

void loop() {
  int numNewMessages = bot.getUpdates(bot.last_message_received + 1);

  while(numNewMessages) {
    handleNewMessages(numNewMessages);
    numNewMessages = bot.getUpdates(bot.last_message_received + 1);
  }
}
```
