# Projeto de Monitoramento de Temperatura e Poluição nos Oceanos

## Integrantes
- Milena Codinhoto da Silva - RM 554682. 
- Evellyn Valencia - RM 557929.

## Descrição
Para a solução desse problema, pensamos em criar um sensor que mede a temperatura da água, da poluição e da quantidade de plásticos nos oceanos. Além disso, criaremos um site onde essas informações estarão sempre atualizadas.

## Componentes Utilizados
- **Arduino**: Plataforma de prototipagem eletrônica de código aberto baseada em hardware e software flexíveis e fáceis de usar.
- **DHT22**: Sensor que faz a medição de temperatura e umidade com alta precisão.
- **Sensor NTC**: Usado para medir a temperatura do mar.
- **LCD 16x2**: Display utilizado para exibir informações em duas linhas com 16 caracteres cada.
- **Breadboard**: Placa de circuito temporária usada para prototipagem e teste de circuitos eletrônicos.

## Código

```cpp
#include <LiquidCrystal_I2C.h>
#include <Wire.h>
#include <DHT.h>

// Define pino do sensor DHT e tipo de sensor
#define DHTPIN 6
#define DHTTYPE DHT22
#define ThermistorPin A0 // Pino para o termistor no microcontrolador Arduino

const float BETA = 3950; // Deve corresponder ao coeficiente Beta do termistor

// Cria objeto DHT
DHT dht(DHTPIN, DHTTYPE);

// Define o endereço e o tipo do LCD
LiquidCrystal_I2C lcd(0x27, 16, 2); 

// Variável Array para criar símbolo do grau
byte grau[8] = {
  B00001100,  
  B00010010,
  B00010010,
  B00001100,
  B00000000,
  B00000000,
  B00000000,
  B00000000
};

void setup() {
  // Configurações iniciais
  pinMode(ThermistorPin, INPUT);
  lcd.begin(16, 2); // Configura o LCD com 16 colunas e 2 linhas
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("     ME O20   ");
  delay(2000);
  lcd.clear();
  Serial.begin(9600); // Inicia a comunicação serial a 9600 bps
  lcd.init(); // Inicializa o LCD
  lcd.backlight(); // Liga a luz de fundo do LCD

  // Cria o caractere com o símbolo do grau
  lcd.createChar(0, grau);
  dht.begin(); // Inicializa o sensor DHT
}

void loop() {
  // Leitura do termistor
  int analogValue = analogRead(ThermistorPin);
  float celsius = 1 / (log(1 / (1023.0 / analogValue - 1)) / BETA + 1.0 / 298.15) - 273.15;
  Serial.print("Temperatura da água: ");
  Serial.print(celsius);
  Serial.println(" ℃");

  // Exibe a temperatura da água no LCD
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("   Temp AGUA");
  lcd.setCursor(5, 1);
  lcd.print(celsius, 1);
  lcd.write(0); // Exibe o símbolo de grau
  lcd.print("C");
  delay(4000);

  // Lê a umidade e a temperatura do ar do sensor DHT
  float p = dht.readHumidity();
  float t = dht.readTemperature();
  // Calcula o índice de calor
  float hic = dht.computeHeatIndex(t, p, false);

  // Exibe a temperatura do ar no LCD
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Temp ar:");
  lcd.setCursor(9, 0);
  lcd.print(t, 1);
  lcd.write(0); // Exibe o símbolo de grau
  lcd.print("C");

  // Exibe a umidade do ar no LCD
  lcd.setCursor(0, 1);
  lcd.print("Umid: ");
  lcd.setCursor(7, 1);
  lcd.print(p, 1);
  lcd.print("%");

  // Envia os valores para o monitor serial
  Serial.print("Temperatura do ar: ");
  Serial.print(t);
  Serial.println("C");
  Serial.print("Umidade: ");
  Serial.print(p);
  Serial.println("%");

  delay(4000);

  // Exibe o índice de calor no LCD
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Indice Calor:");
  lcd.setCursor(5, 1);
  lcd.print(hic, 1);
  lcd.write(0); // Exibe o símbolo de grau
  lcd.print("C");

  // Envia o índice de calor para o monitor serial
  Serial.print("Indice de Calor: ");
  Serial.print(hic);
  Serial.println("C");

  delay(4000);
}
```

## Instruções
1. Monte o circuito conforme o diagrama.
2. Carregue o código acima no seu Arduino.
3. Utilize o monitor serial para visualizar as leituras de temperatura e umidade.
4. As informações também serão exibidas no display LCD 16x2.

## Futuro Desenvolvimento
Desenvolveremos um site para exibir as informações coletadas pelos sensores em tempo real, permitindo o monitoramento contínuo da saúde dos oceanos.
