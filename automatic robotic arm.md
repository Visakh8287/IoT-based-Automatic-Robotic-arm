#include <Arduino.h>
#include "HX711.h"
#include <Servo.h>
#define BLYNK_TEMPLATE_ID "TMPL3zsV3N37s"
#define BLYNK_TEMPLATE_NAME "IOT Based Robotic Hand"
#define BLYNK_AUTH_TOKEN "HRWnkSHuj9MC85I477G2CIs4nAvL6hzP"
#define BLYNK_PRINT Serial
#include <ESP8266WiFi.h>  
#include <BlynkSimpleEsp8266.h>

// HX711 circuit wiring
const int LOADCELL_DOUT_PIN = 14;
const int LOADCELL_SCK_PIN = 12;
int ledPin = 13;

HX711 scale;


Servo myservo1; 
Servo myservo2;
Servo myservo3;// create servo object to control a servo
// twelve servo objects can be created on most boards

int pos = 0;    // variable to store the servo position
int pos1 = 0;
int pos2 = 0;

char auth[] = BLYNK_AUTH_TOKEN;
char ssid[] = "realme5Pro+";  // Enter your Wifi Username
char pass[] = "12345678";  // Enter your Wifi password

Servo s1;
Servo s2;
Servo s3;
Servo s4;
int am;

BLYNK_WRITE(V5)
{
  s1.write(param.asInt());
}

BLYNK_WRITE(V6)
{
  s2.write(param.asInt());
}

BLYNK_WRITE(V3)
{
  s3.write(param.asInt());
}

BLYNK_WRITE(V4)
{
  s4.write(param.asInt());
}
BLYNK_WRITE(V0)
  {
  am=param.asInt();
  Blynk.virtualWrite(V0,am);
  }


void setup() {

  Serial.begin(9600);

  Blynk.begin(auth, ssid, pass);

  s1.attach(2,700,2300); // D4
  s2.attach(16,700,2300); // D0
  s3.attach(5,700,2300); // D1
  s4.attach(4,700,2300); // D2
  pinMode(ledPin, OUTPUT);
  Serial.begin(57600);
  Serial.println("HX711 Demo");
  Serial.println("Initializing the scale");

  scale.begin(LOADCELL_DOUT_PIN, LOADCELL_SCK_PIN);

  Serial.println("Before setting up the scale:");
  Serial.print("read: \t\t");
  Serial.println(scale.read());      // print a raw reading from the ADC

  Serial.print("read average: \t\t");
  Serial.println(scale.read_average(20));   // print the average of 20 readings from the ADC

  Serial.print("get value: \t\t");
  Serial.println(scale.get_value(5));   // print the average of 5 readings from the ADC minus the tare weight (not set yet)

  Serial.print("get units: \t\t");
  Serial.println(scale.get_units(5), 1);  // print the average of 5 readings from the ADC minus tare weight (not set) divided
            // by the SCALE parameter (not set yet)
            
  scale.set_scale(-459.542);
  //scale.set_scale(-471.497);                      // this value is obtained by calibrating the scale with known weights; see the README for details
  scale.tare();               // reset the scale to 0

  Serial.println("After setting up the scale:");

  Serial.print("read: \t\t");
  Serial.println(scale.read());                 // print a raw reading from the ADC

  Serial.print("read average: \t\t");
  Serial.println(scale.read_average(20));       // print the average of 20 readings from the ADC

  Serial.print("get value: \t\t");
  Serial.println(scale.get_value(5));   // print the average of 5 readings from the ADC minus the tare weight, set with tare()

  Serial.print("get units: \t\t");
  Serial.println(scale.get_units(5), 1);        // print the average of 5 readings from the ADC minus tare weight, divided
            // by the SCALE parameter set with set_scale

  Serial.println("Readings:");
  myservo1.attach(4);
  myservo2.attach(5);
  myservo3.attach(16);// attaches the servo on pin 9 to the servo object  
}

void loop() {
  Blynk.run();
  Serial.print("one reading:\t");
  Serial.print(scale.get_units(), 1);
  Serial.print("\t| average:\t");
  Serial.println(scale.get_units(10), 5);
  Blynk.virtualWrite(V1,scale.get_units());
  Serial.println(am);
  
  if (am==0)
  {
   Manu(); 
  }
  else
  {
     if (scale.get_units()> 30) 
    {
      digitalWrite(ledPin, HIGH);
      left();
    }
    else
    {
      digitalWrite(ledPin, LOW);
    }
  }

delay(100);
}

//*************left**********

void left()
{
  for (pos = 0; pos <= 100; pos += 1) { // goes from 0 degrees to 180 degrees
    // in steps of 1 degree
    myservo1.write(pos);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  delay(100);
  for (pos1 = 0; pos1 <= 120; pos1 += 1) { // goes from 0 degrees to 180 degrees
    // in steps of 1 degree
    myservo2.write(pos1);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  delay(100);

    for (pos2 = 0; pos2 <= 60; pos2 += 1) { // goes from 0 degrees to 180 degrees
    // in steps of 1 degree
    myservo3.write(pos2);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  delay(100);
  
  for (pos1 = 90; pos1 >= 0; pos1 -= 1) { // goes from 180 degrees to 0 degrees
    myservo2.write(pos1);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  
  delay(100);

  for (pos = 90; pos >= 0; pos -= 1) { // goes from 180 degrees to 0 degrees
    myservo1.write(pos);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  for (pos1 = 0; pos1 <= 120; pos1 += 1) { // goes from 0 degrees to 180 degrees
    // in steps of 1 degree
    myservo2.write(pos1);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  
  
  for (pos2 = 60; pos2 >= 0; pos2 -= 1) { // goes from 180 degrees to 0 degrees
    myservo3.write(pos2);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  for (pos1 = 100; pos1 >= 0; pos1 -= 1) { // goes from 180 degrees to 0 degrees
    myservo2.write(pos1);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  
}

//*************Right*************

void Right()

{
  for (pos = 0; pos <= 100; pos += 1) { // goes from 0 degrees to 180 degrees
    // in steps of 1 degree
    myservo1.write(pos);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  delay(100);
  for (pos1 = 0; pos1 <= 120; pos1 += 1) { // goes from 0 degrees to 180 degrees
    // in steps of 1 degree
    myservo2.write(pos1);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  delay(100);

    for (pos2 = 0; pos2 <= 60; pos2 += 1) { // goes from 0 degrees to 180 degrees
    // in steps of 1 degree
    myservo3.write(pos2);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  delay(100);
  
  for (pos1 = 90; pos1 >= 0; pos1 -= 1) { // goes from 180 degrees to 0 degrees
    myservo2.write(pos1);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  
  delay(100);

  for (pos = 90; pos >= 0; pos -= 1) { // goes from 180 degrees to 0 degrees
    myservo1.write(pos);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  for (pos1 = 0; pos1 <= 120; pos1 += 1) { // goes from 0 degrees to 180 degrees
    // in steps of 1 degree
    myservo2.write(pos1);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  
  
  for (pos2 = 60; pos2 >= 0; pos2 -= 1) { // goes from 180 degrees to 0 degrees
    myservo3.write(pos2);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  for (pos1 = 100; pos1 >= 0; pos1 -= 1) { // goes from 180 degrees to 0 degrees
    myservo2.write(pos1);              // tell servo to go to position in variable 'pos'
    delay(15);                       // waits 15ms for the servo to reach the position
  }
  
}

//*************Manual*************

void Manu()
{
  Blynk.run();
}
