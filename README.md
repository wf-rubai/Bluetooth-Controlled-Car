# Bluetooth-Controlled-Car
---
## Arduino code
Copy this code and paste it into your arduino IDE
```bash
// Pins for the motor driver
const int motorA1 = 5; // Motor A forward
const int motorA2 = 6; // Motor A backward
const int motorB1 = 9; // Motor B forward
const int motorB2 = 10; // Motor B backward

int speedA = 0; // Speed for Motor A
int speedB = 0; // Speed for Motor B

void setup() {
  pinMode(motorA1, OUTPUT);
  pinMode(motorA2, OUTPUT);
  pinMode(motorB1, OUTPUT);
  pinMode(motorB2, OUTPUT);

  // Start serial communication
  Serial.begin(9600);
}

void loop() {
  if (Serial.available()) {
    char command = Serial.read();
    Serial.println(command); // For debugging

    // Movement commands
    if (command == 'F') { // Forward
      setMotors(speedA, speedB, 1, 0, 1, 0);
    } else if (command == 'B') { // Backward
      setMotors(speedA, speedB, 0, 1, 0, 1);
    } else if (command == 'L') { // Turn left
      setMotors(speedA / 2, speedB, 0, 1, 1, 0);
    } else if (command == 'R') { // Turn right
      setMotors(speedA, speedB / 2, 1, 0, 0, 1);
    } else if (command == 'S') { // Stop
      setMotors(0, 0, 0, 0, 0, 0);
    } 
    // Speed control using map
    else if (command >= '0' && command <= '9') {
      int newSpeed = map(command - '0', 0, 9, 0, 255); // Map '0'-'9' to 0-255
      speedA = speedB = newSpeed;
      Serial.print("Mapped Speed: ");
      Serial.println(newSpeed);
    }
  }
}

void setMotors(int speedA, int speedB, int dirA1, int dirA2, int dirB1, int dirB2) {
  analogWrite(motorA1, speedA * dirA1);
  analogWrite(motorA2, speedA * dirA2);
  analogWrite(motorB1, speedB * dirB1);
  analogWrite(motorB2, speedB * dirB2);
}
```
