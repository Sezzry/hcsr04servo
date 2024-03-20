#include <Servo.h>

Servo myServo;
bool manualMode = false;

const int trigPin = 10; // Trig-pin för HC-SR04
const int echoPin = 11; // Echo-pin för HC-SR04

void setup() {
    Serial.begin(9600); // Starta serieporten med baud rate 9600
    myServo.attach(9); // Koppla servo till pin 9
    pinMode(trigPin, OUTPUT); // Ställ in trig-pin som output
    pinMode(echoPin, INPUT); // Ställ in echo-pin som input
}

void controlServo(int degrees) {
    myServo.write(degrees); // Skicka vinkel till servo
}

float getDistance() {
    digitalWrite(trigPin, LOW);
    delayMicroseconds(2);
    digitalWrite(trigPin, HIGH);
    delayMicroseconds(10);
    digitalWrite(trigPin, LOW);

    // Beräkna pulsbredden och omvandla till avstånd i centimeter
    float duration = pulseIn(echoPin, HIGH);
    float distance = duration / 58.0; // Omvandla till centimeter

    // Om avståndet är större än 400 cm, sätt det till 400 cm
    if (distance > 400) {
        distance = 400;
    }

    return distance;
}

void loop() {
    if (Serial.available() > 0) {
        String command = Serial.readStringUntil('\n'); // Läs in en rad från serieporten
        if (command.startsWith("servo")) {
            if (command.equals("servo auto")) {
                manualMode = true;
                for (int i = 0; i <= 180; ++i) {
                    controlServo(i); // Skicka vinkel till servo
                    delay(20); // Vänta 20ms mellan varje steg
                    Serial.print("Distance: ");
                    Serial.print(getDistance()); // Skriv ut avståndet
                    Serial.println(" cm");
                }
                for (int i = 180; i >= 0; --i) {
                    controlServo(i); // Skicka vinkel till servo
                    delay(20); // Vänta 20ms mellan varje steg
                    Serial.print("Distance: ");
                    Serial.print(getDistance()); // Skriv ut avståndet
                    Serial.println(" cm");
                }
                manualMode = false; // Återställ manualMode till false efter att den automatiska sekvensen är klar
            } else {
                if (!manualMode) { // Endast manuell styrning om inte manualMode är aktivt
                    int degrees = command.substring(6).toInt(); // Extrahera gradantal från kommandot
                    controlServo(degrees); // Skicka vinkel till servo
                }
            }
        } else {
            Serial.println("Unknown command");
        }
    } else {
        // Mäta avstånd kontinuerligt om inget kommando mottas
        Serial.print("Distance: ");
        Serial.print(getDistance()); // Skriv ut avståndet
        Serial.println(" cm");
        delay(100); // Vänta en kort stund innan nästa mätning
    }
}
