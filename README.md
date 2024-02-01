const int ledPin = 13;      // LED pin
const int pirPin = 2;       // PIR sensor pin
const int buttonPin = 3;    // Button pin

int ledState = LOW;          // LED state (LOW or HIGH)
unsigned long lastDebounceTime = 0;
const int debounceDelay = 50;
const long ledDuration = 3000;  // LED on duration in milliseconds

void setup() {
  pinMode(ledPin, OUTPUT);
  pinMode(pirPin, INPUT);
  pinMode(buttonPin, INPUT_PULLUP);
  attachInterrupt(digitalPinToInterrupt(pirPin), PIR_motionDetected, RISING);
  Serial.begin(9600);
}

void loop() {
  checkButton();  // Check button state
  updateLED();    // Update LED state
}

void checkButton() {
  int buttonState = digitalRead(buttonPin);

  if (buttonState == LOW && millis() - lastDebounceTime > debounceDelay) {
    ledState = !ledState;
    digitalWrite(ledPin, ledState);
    Serial.println("Button pressed, LED toggled");
    lastDebounceTime = millis();
  }
}

void updateLED() {
  if (ledState == HIGH && millis() - lastDebounceTime >= ledDuration) {
    ledState = LOW;
    digitalWrite(ledPin, ledState);
    Serial.println("LED turned off");
  }
}

void PIR_motionDetected() {
  digitalWrite(ledPin, HIGH);
  Serial.println("Motion detected!");
  delay(3000);  // Consider using millis() for non-blocking behavior
  digitalWrite(ledPin, LOW);
  ledState = LOW;  // Ensure ledState is LOW after motion detection
}
