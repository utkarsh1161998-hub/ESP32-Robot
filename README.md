#include <WiFi.h>
#include <WebServer.h>

// Define Motor Driver Pins (Example: TB6612FNG or L298N)
const int motor1_pin1 = 12;
const int motor1_pin2 = 13;
const int motor2_pin1 = 22;
const int motor2_pin2 = 23;

WebServer server(80);

void moveForward() {
  digitalWrite(motor1_pin1, HIGH);   digitalWrite(motor1_pin2, LOW);
  digitalWrite(motor2_pin1, HIGH);   digitalWrite(motor2_pin2, LOW);
  server.send(200, "text/plain", "Moving Forward");
}

void stopRobot() {
  digitalWrite(motor1_pin1, LOW);    digitalWrite(motor1_pin2, LOW);
  digitalWrite(motor2_pin1, LOW);    digitalWrite(motor2_pin2, LOW);
  server.send(200, "text/plain", "Stopped");
}

void setup() {
  Serial.begin(115200);
  
  pinMode(motor1_pin1, OUTPUT);  pinMode(motor1_pin2, OUTPUT);
  pinMode(motor2_pin1, OUTPUT);  pinMode(motor2_pin2, OUTPUT);

  // Set up ESP32 as a Wi-Fi Access Point
  WiFi.softAP("ESP32_Robot_AP", "12345678");
  Serial.println("Access Point Started.");
  Serial.print("IP Address: ");
  Serial.println(WiFi.softAPIP());

  // Define routing endpoints for browser commands
  server.on("/forward", moveForward);
  server.on("/stop", stopRobot);
  
  server.begin();
}

void loop() {
  server.handleClient(); // Keep listening for incoming Wi-Fi control requests
}
