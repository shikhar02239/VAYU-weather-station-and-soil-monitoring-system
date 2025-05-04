# VAYU-weather-station-and-soil-monitoring-system
IoT Based weather monitoring and soil monitoring system
VAYU is an innovative system that monitors weather conditions and soil health in real time, designed to help farmers make smarter decisions. Using sensors like DHT11 for temperature and humidity, BMP180 for pressure, and soil moisture sensors, it collects vital data about the environment. The data is stored on an SD card and shared via Bluetooth for easy access.

This project is impactful because it provides actionable insights, improving crop management, irrigation, and yield, making it a step forward in sustainable agriculture.

# code for weather station
#include <Wire.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BMP085_U.h>
#include <LiquidCrystal_I2C.h>
#include <DHT.h>

#define DHTPIN 2
#define DHTTYPE DHT11

// Initialize BMP180 and LCD
Adafruit_BMP085_Unified bmp = Adafruit_BMP085_Unified(10085);
LiquidCrystal_I2C lcd(0x27, 16, 2); // Change address if needed (0x3F or 0x27)
DHT dht(DHTPIN, DHTTYPE);

// Sensor Pins
const int mq135Pin = 34;               // Analog pin for ESP32
const int soilMoistureDigitalPin = 25; // D25 for soil sensor digital pin
const int rainSensorDigitalPin = 26;   // D26 for rain sensor digital pin

void setup() {
  Serial.begin(115200);
  
  lcd.begin(16, 2);
  lcd.backlight();
  
  dht.begin();
  
  if (!bmp.begin()) {
    Serial.println("BMP180 sensor not detected.");
    while (1);
  }

  pinMode(soilMoistureDigitalPin, INPUT);
  pinMode(rainSensorDigitalPin, INPUT);
}

void loop() {
  // Read MQ-135 air quality
  int airQuality = analogRead(mq135Pin);

  // Read Soil and Rain Sensors
  int soilStatus = digitalRead(soilMoistureDigitalPin);
  int rainStatus = digitalRead(rainSensorDigitalPin);

  // Read DHT11
  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();

  // Read BMP180 pressure
  float pressure;
  bmp.getPressure(&pressure);

  // Serial Monitor Output
  Serial.print("Air Quality: "); Serial.println(airQuality);
  Serial.print("Soil: "); Serial.println(soilStatus == HIGH ? "Dry" : "Wet");
  Serial.print("Rain: "); Serial.println(rainStatus == HIGH ? "No Rain" : "Rain Detected");
  Serial.print("Temp: "); Serial.print(temperature); Serial.println(" C");
  Serial.print("Humidity: "); Serial.print(humidity); Serial.println(" %");
  Serial.print("Pressure: "); Serial.print(pressure); Serial.println(" Pa");

  // LCD Output
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("T:"); lcd.print(temperature);
  lcd.print(" H:"); lcd.print(humidity);

  lcd.setCursor(0, 1);
  lcd.print("S:"); lcd.print(soilStatus == HIGH ? "Dry" : "Wet");
  lcd.print(" R:"); lcd.print(rainStatus == HIGH ? "No" : "Yes");

  delay(2000);
}
