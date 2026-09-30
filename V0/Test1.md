We are testing **ESP8266 + TB6612FNG + external battery + 2 motors**.

For this test, **disconnect the MPU6050 and HC-SR04**.

### Wiring

Use the same pins:

| TB6612FNG | ESP8266 |
| --------- | ------- |
| VCC       | 3.3V    |
| GND       | GND     |
| STBY      | 3.3V    |
| PWMA      | D5      |
| AIN1      | D6      |
| AIN2      | D7      |
| PWMB      | D0      |
| BIN1      | D3      |
| BIN2      | D4      |

Battery:

| Battery | TB6612FNG |
| ------- | --------- |
| +       | **VM**    |
| −       | **GND**   |

Motors:

* A01/A02 → Left motor
* B01/B02 → Right motor

**Keep the robot's wheels lifted off the ground for the first test.**

### Test code

```cpp
// ESP8266 + TB6612FNG + 2 Motors Test

// LEFT MOTOR
#define PWMA D5
#define AIN1 D6
#define AIN2 D7

// RIGHT MOTOR
#define PWMB D0
#define BIN1 D3
#define BIN2 D4

// Standby
#define STBY D8

void setup() {

  Serial.begin(115200);

  pinMode(PWMA, OUTPUT);
  pinMode(AIN1, OUTPUT);
  pinMode(AIN2, OUTPUT);

  pinMode(PWMB, OUTPUT);
  pinMode(BIN1, OUTPUT);
  pinMode(BIN2, OUTPUT);

  pinMode(STBY, OUTPUT);

  // Enable TB6612
  digitalWrite(STBY, HIGH);

  stopMotors();

  Serial.println();
  Serial.println("==============================");
  Serial.println("TB6612FNG MOTOR TEST");
  Serial.println("==============================");
}

void loop() {

  // -------------------------
  // BOTH MOTORS FORWARD
  // -------------------------
  Serial.println("Both motors FORWARD");

  leftMotor(150);
  rightMotor(150);

  delay(2000);

  stopMotors();
  delay(1000);


  // -------------------------
  // BOTH MOTORS REVERSE
  // -------------------------
  Serial.println("Both motors REVERSE");

  leftMotor(-150);
  rightMotor(-150);

  delay(2000);

  stopMotors();
  delay(1000);


  // -------------------------
  // LEFT MOTOR ONLY
  // -------------------------
  Serial.println("Left motor FORWARD");

  leftMotor(150);
  rightMotor(0);

  delay(2000);

  stopMotors();
  delay(1000);


  // -------------------------
  // RIGHT MOTOR ONLY
  // -------------------------
  Serial.println("Right motor FORWARD");

  leftMotor(0);
  rightMotor(150);

  delay(2000);

  stopMotors();
  delay(1000);


  // -------------------------
  // TURN LEFT
  // -------------------------
  Serial.println("Turning LEFT");

  leftMotor(-150);
  rightMotor(150);

  delay(1500);

  stopMotors();
  delay(1000);


  // -------------------------
  // TURN RIGHT
  // -------------------------
  Serial.println("Turning RIGHT");

  leftMotor(150);
  rightMotor(-150);

  delay(1500);

  stopMotors();
  delay(2000);
}


// =================================
// LEFT MOTOR
// =================================

void leftMotor(int speed) {

  speed = constrain(speed, -255, 255);

  if (speed > 0) {

    digitalWrite(AIN1, HIGH);
    digitalWrite(AIN2, LOW);

    analogWrite(PWMA, speed);

  } 
  else if (speed < 0) {

    digitalWrite(AIN1, LOW);
    digitalWrite(AIN2, HIGH);

    analogWrite(PWMA, -speed);

  } 
  else {

    analogWrite(PWMA, 0);

    digitalWrite(AIN1, LOW);
    digitalWrite(AIN2, LOW);
  }
}


// =================================
// RIGHT MOTOR
// =================================

void rightMotor(int speed) {

  speed = constrain(speed, -255, 255);

  if (speed > 0) {

    digitalWrite(BIN1, HIGH);
    digitalWrite(BIN2, LOW);

    analogWrite(PWMB, speed);

  } 
  else if (speed < 0) {

    digitalWrite(BIN1, LOW);
    digitalWrite(BIN2, HIGH);

    analogWrite(PWMB, -speed);

  } 
  else {

    analogWrite(PWMB, 0);

    digitalWrite(BIN1, LOW);
    digitalWrite(BIN2, LOW);
  }
}


// =================================
// STOP MOTORS
// =================================

void stopMotors() {

  analogWrite(PWMA, 0);
  analogWrite(PWMB, 0);

  digitalWrite(AIN1, LOW);
  digitalWrite(AIN2, LOW);

  digitalWrite(BIN1, LOW);
  digitalWrite(BIN2, LOW);
}
```

### Expected sequence

The robot will do:

**2 sec forward → stop → 2 sec reverse → stop → left motor → right motor → turn left → turn right → repeat**

If the two motors physically rotate in opposite directions during **"Both motors FORWARD"**, don't worry yet. Because the motors are mounted on opposite sides of the robot, their electrical directions may need to be reversed in software.

Yes. If you have **two 3.7 V, 2200 mAh rechargeable batteries**, you can connect them **in series** for your BO motors.

### 🔋 2 batteries in series

```text
        Battery 1              Battery 2
      ┌───────────┐          ┌───────────┐
      │  3.7V     │          │  3.7V     │
      │  2200mAh  │          │  2200mAh  │
      └───────────┘          └───────────┘
       -         +────────────-         +
       │                              │
       │                              │
       ▼                              ▼
   TB6612 GND                      TB6612 VM
```

### What you'll get

| Specification   |          Result |
| --------------- | --------------: |
| Nominal voltage |       **7.4 V** |
| Fully charged   |       **8.4 V** |
| Capacity        |    **2200 mAh** |
| Configuration   | **2S (series)** |

So yes, this is in the voltage range you were looking for.

### Connect to your TB6612FNG

```text
Battery 1 (-) ──────────────── GND
Battery 1 (+) ─── Battery 2 (-)

Battery 2 (+) ──────────────── VM
```

And separately:

```text
ESP8266 3.3V ─────────────── TB6612 VCC
ESP8266 GND  ─────────────── TB6612 GND
```

**Do not connect the 7.4/8.4 V battery directly to the ESP8266.**

### One important thing

Check the **BO motor's rated voltage** before powering it. If your motors are rated **6 V**, an 8.4 V freshly charged 2S pack can be too high for continuous operation.

If you tell me the **voltage printed on your BO motors** (or send a photo), I can tell you whether this **2 × 3.7 V setup is suitable** and whether you should use a regulator/PWM limit.

