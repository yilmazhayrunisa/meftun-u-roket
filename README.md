#define WHEEL_DIAMETER 0.065   // metre cinsinden (6.5 cm örnek)
#define ENCODER_PPR 20         // Enkoder 1 turda kaç pulse üretiyor
#define TARGET_DISTANCE 50.0   // Gitmesini istediğimiz mesafe (metre)

// ====== AYARLAR ======
#define ENCODER_PIN 2
#define MOTOR_IN1 8
#define MOTOR_IN2 9
#define MOTOR_ENA 10
#define WHEEL_DIAMETER 0.065   // metre
#define ENCODER_PPR 20
#define TARGET_DISTANCE 50.0   // metre

volatile long encoderCount = 0;

// ====== ENCODER KESMESİ ======
void encoderISR() {
  encoderCount++;
}

void setup() {
  pinMode(ENCODER_PIN, INPUT);
  pinMode(MOTOR_IN1, OUTPUT);
  pinMode(MOTOR_IN2, OUTPUT);
  pinMode(MOTOR_ENA, OUTPUT);

  attachInterrupt(digitalPinToInterrupt(ENCODER_PIN), encoderISR, RISING);

  Serial.begin(9600);

  // Motor ileri
  digitalWrite(MOTOR_IN1, HIGH);
  digitalWrite(MOTOR_IN2, LOW);
  analogWrite(MOTOR_ENA, 180); // hız (0–255)
}

void loop() {
  float wheelCircumference = 3.1416 * WHEEL_DIAMETER;
  float rotations = (float)encoderCount / ENCODER_PPR;
  float distanceTravelled = rotations * wheelCircumference;

  Serial.print("Gidilen Mesafe: ");
  Serial.println(distanceTravelled);

  if (distanceTravelled >= TARGET_DISTANCE) {
    // Motor dur
    analogWrite(MOTOR_ENA, 0);
    digitalWrite(MOTOR_IN1, LOW);
    digitalWrite(MOTOR_IN2, LOW);

    Serial.println("50 metreye ulaşıldı. Motor durdu.");
    while (1); // tamamen dur
  }
}
