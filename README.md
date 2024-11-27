# Arduino Bluetooth-Controlled Car 🚗💨

This project creates a two-motor car controlled by an Arduino Uno and a Bluetooth app. The car uses the L298N motor driver for controlling motor speed and direction. 

---

## **Wiring Diagram**

| L298N Pin  | Arduino Pin | Purpose                  |
|------------|-------------|--------------------------|
| **ENA**    | Always HIGH | Enable Motor A           |
| **IN1**    | Pin 5       | Motor A - Forward        |
| **IN2**    | Pin 6       | Motor A - Backward       |
| **IN3**    | Pin 10       | Motor B - Forward        |
| **IN4**    | Pin 11      | Motor B - Backward       |
| **ENB**    | Always HIGH | Enable Motor B           |
| **GND**    | Arduino GND | Shared ground            |
| **VCC**    | External 12V| Motor power              |
| **5V**     | Arduino 5V  | Optional Arduino power   |

---

## **Commands**

| Command | Action                  |
|---------|-------------------------|
| `F`     | Move Forward            |
| `B`     | Move Backward           |
| `L`     | Turn Left               |
| `R`     | Turn Right              |
| `S`     | Stop                    |
| `0-9`   | Set Speed (0 = stop, 9 = full speed) |

---

## **Steps to Build**

1. Connect the **L298N motor driver** to the motors, Arduino, and power supply as described in the wiring diagram.
2. Upload the code (`.ino` file) to the Arduino using the Arduino IDE.
3. Open the serial monitor (baud rate: `9600`) or pair the Arduino's Bluetooth module with your smartphone using a compatible app.
4. Send commands via the app or serial monitor to control the car.
   - Example: Sending `F` will make the car move forward, and sending `5` will set the speed to ~50%.

---

```bash
#define IN1 5   // L298N Motor Driver - Motor A Forward
#define IN2 6   // L298N Motor Driver - Motor A Backward
#define IN3 10  // L298N Motor Driver - Motor B Forward
#define IN4 11  // L298N Motor Driver - Motor B Backward

int command;          // Stores app command state
int speedValue = 204; // Motor speed (0 - 255)

void setup() {
  // Set motor driver pins as output
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);

  // Initialize serial communication
  Serial.begin(9600);
}

void loop() {
  if (Serial.available() > 0) {
    command = Serial.read(); // Read the incoming command
    Serial.println(command); // Debug: Print the received command
    Stop(); // Initialize with motors stopped

    // Handle movement commands
    if (command == 'F') { 
      forward();
    } else if (command == 'B') {
      back();
    } else if (command == 'L') {
      left();
    } else if (command == 'R') {
      right();
    } 
    // Handle speed control using map
    else if (command >= '0' && command <= '9') {
      speedValue = map(command - '0', 0, 9, 100, 255); // Map '0'-'9' to speed range (100-255)
      Serial.print("Speed set to: ");
      Serial.println(speedValue);
    }
  }
}

void forward() {
  analogWrite(IN1, speedValue); // Motor A forward
  analogWrite(IN3, speedValue); // Motor B forward
}

void back() {
  analogWrite(IN2, speedValue); // Motor A backward
  analogWrite(IN4, speedValue); // Motor B backward
}

void left() {
  analogWrite(IN3, speedValue); // Motor B forward
  analogWrite(IN2, speedValue); // Motor A backward
}

void right() {
  analogWrite(IN4, speedValue); // Motor B backward
  analogWrite(IN1, speedValue); // Motor A forward
}

void Stop() {
  analogWrite(IN1, 0); // Stop Motor A forward
  analogWrite(IN2, 0); // Stop Motor A backward
  analogWrite(IN3, 0); // Stop Motor B forward
  analogWrite(IN4, 0); // Stop Motor B backward
}

```

---

## **Notes**

- **Enable Pins (ENA and ENB):**
  - These pins must be set to HIGH. If your L298N module has jumpers, leave them in place for full power. Otherwise, connect to a PWM pin for dynamic speed control.
  
- **Power Supply**:
  - Use an external 12V power source for the motors to avoid overloading the Arduino.
  - Ensure that Arduino and L298N share a common ground.

- **Bluetooth Module**:
  - Pair your Bluetooth module (e.g., HC-05) with the mobile app, and connect its TX/RX pins to the Arduino RX/TX.

---

## **Additional Information**

- The `map()` function maps commands from `0-9` to PWM values (`0-255`) for smooth speed control.
- Adjust motor speed using the `setMotors()` function, which manages speed and direction logic.
- Test your connections before applying a high voltage to ensure proper wiring.

Happy Building! 🚗💨
