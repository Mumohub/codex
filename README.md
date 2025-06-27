// Pins
#define TRIG_PIN 8
#define ECHO_PIN 9

#define RED_LED 2
#define YELLOW_LED 3
#define GREEN_LED 4

#define BUZZER 5

#define PUMP_LIFT_IN1 6
#define PUMP_LIFT_IN2 7

#define PUMP_LOWER_IN3 10
#define PUMP_LOWER_IN4 11

// Distances in cm
#define SHIP_DETECTED_DISTANCE 30

void setup() {
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);

  pinMode(RED_LED, OUTPUT);
  pinMode(YELLOW_LED, OUTPUT);
  pinMode(GREEN_LED, OUTPUT);
  pinMode(BUZZER, OUTPUT);

  pinMode(PUMP_LIFT_IN1, OUTPUT);
  pinMode(PUMP_LIFT_IN2, OUTPUT);
  pinMode(PUMP_LOWER_IN3, OUTPUT);
  pinMode(PUMP_LOWER_IN4, OUTPUT);

  digitalWrite(RED_LED, LOW);
  digitalWrite(YELLOW_LED, LOW);
  digitalWrite(GREEN_LED, HIGH); // Default green on

  Serial.begin(9600);
}

long getDistance() {
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);
  long duration = pulseIn(ECHO_PIN, HIGH);
  return duration * 0.034 / 2; // cm
}

void liftBridge() {
  digitalWrite(PUMP_LIFT_IN1, HIGH);
  digitalWrite(PUMP_LIFT_IN2, LOW);
}

void lowerBridge() {
  digitalWrite(PUMP_LOWER_IN3, HIGH);
  digitalWrite(PUMP_LOWER_IN4, LOW);
}

void stopPumps() {
  digitalWrite(PUMP_LIFT_IN1, LOW);
  digitalWrite(PUMP_LIFT_IN2, LOW);
  digitalWrite(PUMP_LOWER_IN3, LOW);
  digitalWrite(PUMP_LOWER_IN4, LOW);
}

void loop() {
  long distance = getDistance();
  Serial.println(distance);

  if (distance <= SHIP_DETECTED_DISTANCE) {
    // Stop cars
    digitalWrite(GREEN_LED, LOW);
    digitalWrite(RED_LED, HIGH);
    digitalWrite(BUZZER, HIGH);
    delay(2000); // Alert duration
    digitalWrite(BUZZER, LOW);

    // Lift bridge
    liftBridge();
    delay(5000); // Time to lift
    stopPumps();

    // Wait for ship to pass
    delay(5000); // You can adjust or replace with distance logic

    // Signal cars that bridge will lower
    digitalWrite(RED_LED, LOW);
    digitalWrite(YELLOW_LED, HIGH);
    delay(2000);

    // Lower bridge
    lowerBridge();
    delay(5000); // Time to lower
    stopPumps();

    // Let cars pass
    digitalWrite(YELLOW_LED, LOW);
    digitalWrite(GREEN_LED, HIGH);
  }

  delay(500);
}
