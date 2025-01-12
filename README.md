# VAYU-weather-station-and-soil-monitoring-system
IoT Based weather monitoring and soil monitoring system
VAYU is an innovative system that monitors weather conditions and soil health in real time, designed to help farmers make smarter decisions. Using sensors like DHT11 for temperature and humidity, BMP180 for pressure, and soil moisture sensors, it collects vital data about the environment. The data is stored on an SD card and shared via Bluetooth for easy access.

This project is impactful because it provides actionable insights, improving crop management, irrigation, and yield, making it a step forward in sustainable agriculture.
code for the project:

#include <Wire.h>
#include <Adafruit_Sensor.h>
#include <Adafruit_BMP085_U.h>
#include <DHT.h>
#include <SoftwareSerial.h>  // Include SoftwareSerial for Bluetooth communication

#define DHTPIN 2          // Pin for DHT11 sensor
#define DHTTYPE DHT11     // DHT 11

// Initialize BMP180
Adafruit_BMP085_Unified bmp = Adafruit_BMP085_Unified(10085);
DHT dht(DHTPIN, DHTTYPE);

// Sensor Pins
const int mq135Pin = A0;             // MQ-135 air quality sensor analog pin
const int soilMoistureDigitalPin = 3; // Soil moisture sensor digital pin
const int rainSensorDigitalPin = 4;   // Rain sensor digital pin

// Bluetooth Serial Communication
SoftwareSerial btSerial(10, 11);  // RX, TX pins for Bluetooth communication (HC-05/HC-06)

void setup() {
  Serial.begin(9600);    // Begin Serial Monitor
  btSerial.begin(9600);  // Begin Bluetooth communication

  // Initialize DHT11
  dht.begin();

  // Initialize BMP180
  if (!bmp.begin()) {
    Serial.println("BMP180 sensor not detected. Please check wiring.");
    while (1);
  }

  // Set soil moisture and rain sensor pins as input
  pinMode(soilMoistureDigitalPin, INPUT);
  pinMode(rainSensorDigitalPin, INPUT);
  
  // Initial message
  Serial.println("Weather Station Initialized");
  btSerial.println("Weather Station Initialized");
}

void loop() {
  // Read MQ-135 sensor for air quality
  int airQuality = analogRead(mq135Pin);

  // Read Soil Moisture Sensor (Digital)
  int soilStatus = digitalRead(soilMoistureDigitalPin);

  // Read Rain Sensor (Digital)
  int rainStatus = digitalRead(rainSensorDigitalPin);

  // Read DHT11 sensor for temperature and humidity
  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();

  // Read BMP180 sensor for pressure
  float pressure;
  bmp.getPressure(&pressure); // Pass the address of 'pressure' to store the reading

  // Display data on Serial Monitor
  Serial.println("Weather Data:");
  btSerial.println("Weather Data:");

  Serial.print("Air Quality (Analog): ");
  Serial.println(airQuality);
  btSerial.print("Air Quality (Analog): ");
  btSerial.println(airQuality);

  Serial.print("Soil Status (Digital): ");
  Serial.println(soilStatus == HIGH ? "Dry" : "Wet");
  btSerial.print("Soil Status (Digital): ");
  btSerial.println(soilStatus == HIGH ? "Dry" : "Wet");

  Serial.print("Rain Status (Digital): ");
  Serial.println(rainStatus == HIGH ? "No Rain" : "Rain Detected");
  btSerial.print("Rain Status (Digital): ");
  btSerial.println(rainStatus == HIGH ? "No Rain" : "Rain Detected");

  Serial.print("Humidity: ");
  Serial.print(humidity);
  Serial.println(" %");
  btSerial.print("Humidity: ");
  btSerial.print(humidity);
  btSerial.println(" %");

  Serial.print("Temperature: ");
  Serial.print(temperature);
  Serial.println(" *C");
  btSerial.print("Temperature: ");
  btSerial.print(temperature);
  btSerial.println(" *C");

  Serial.print("Pressure: ");
  Serial.print(pressure);
  Serial.println(" Pa");
  btSerial.print("Pressure: ");
  btSerial.print(pressure);
  btSerial.println(" Pa");

  Serial.println("------------------------------------");
  btSerial.println("------------------------------------");

  delay(2000); // Delay for 2 seconds before reading again
}
