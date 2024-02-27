const int soilMoisturePin = A0;  // Analog pin for soil moisture sensor
const int temperaturePin = A1;  // Analog pin for temperature sensor
const int humidityPin = A2;     // Analog pin for humidity sensor
const int lightPin = A3;        // Analog pin for light sensor

void setup() {
  Serial.begin(9600);
}

void loop() {
  // Read sensor values
  int soilMoisture = analogRead(soilMoisturePin);
  int temperature = analogRead(temperaturePin);
  int humidity = analogRead(humidityPin);
  int lightLevel = analogRead(lightPin);

  // Print sensor values to the serial port
  Serial.print("Soil Moisture: ");
  Serial.println(soilMoisture);
  
  Serial.print("Temperature: ");
  Serial.println(temperature);

  Serial.print("Humidity: ");
  Serial.println(humidity);

  Serial.print("Light Level: ");
  Serial.println(lightLevel);
}
