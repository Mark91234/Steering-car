#include <Servo.h>
#include<LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27,16,2);     //display type

int servoPin = 11;        // servoo
Servo myservo;

int move_button = 3;     // Move / direction button
int print_button = A2;   // Print button

int servo_potentiometer= A0;      // Servo control potentiometer
int speed_potentiometer = A1;      // Speed control potentiometer

int trigR = 10;
int echoR = 2;
int trigL = 12;
int echoL = 13;

int in1 = 4;        //dcmotor 1 
int in2 = 7;
int in3 = 6;          //dcmotor 2 
int in4 = 5;
int ena = 9;    // speed of dc motor 1
int enb = 3;    // speed of dc motor 2 

int speed_potentiometer_reading;        //potentiometer speed storing unit 
int servo_potentiometer_reading;       //potentiometer servo storing unit 

float dstL;        //ultrasonic storing unit 
float dstR;       //ultrasonic storing unit 

String state;         //state 

float distanceCm;      

float vlc; 

volatile bool direction = true;  

volatile unsigned long lastInterruptTime = 0; // debounce

float ultrasonic(int trigPin, int echoPin);

void forward() {              // forward 
  digitalWrite(in1, HIGH);   
  digitalWrite(in2, LOW);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
}

void back() {                // backwards   
  digitalWrite(in1, LOW);   
  digitalWrite(in2, HIGH);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
}

void stop() {                //stop     
  digitalWrite(in1, LOW);   
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
  analogWrite(ena, 0);    // speed is zero 
  analogWrite(enb, 0);
}

float ultrasonic (int trigPin, int echoPin) {    
  digitalWrite(trigPin, LOW);  
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(2);
  digitalWrite(trigPin, LOW);

  long duration = pulseIn(echoPin, HIGH);
  float distanceCm = (duration * 0.0343) / 2;
  return distanceCm;
}

void toggleDirection() {
  unsigned long currentTime = millis();
  if (currentTime - lastInterruptTime > 200) {
    direction = !direction;
    lastInterruptTime = currentTime;

  }

}
void screenDisplay(){
lcd.setCursor(2,0);  
lcd.print("DIR: ");
lcd.print(state);
delay(5000);

lcd.setCursor(2,0);
lcd.print("V: ");
lcd.print(vlc);
delay(5000);

lcd.setCursor(2,0);  
lcd.print("DistR: ");
lcd.print(dstR);
delay(5000);

lcd.setCursor(2,0); 
lcd.print("DistL: ");
lcd.print(dstL);
delay(5000);
}


void setup() {
  Serial.begin(9600);

lcd.init();
lcd.clear();
lcd.backlight();

pinMode(move_button, INPUT_PULLUP);
pinMode(print_button, INPUT_PULLUP);

myservo.attach(servoPin);   

pinMode(trigR, OUTPUT);
pinMode(echoR, INPUT);
pinMode(trigL, OUTPUT);
pinMode(echoL, INPUT);

pinMode(in1, OUTPUT);
pinMode(in2, OUTPUT);
pinMode(in3, OUTPUT);
pinMode(in4, OUTPUT);
pinMode(ena, OUTPUT);
pinMode(enb, OUTPUT);

  attachInterrupt(digitalPinToInterrupt(move_button), toggleDirection, FALLING);  //interrupt linking 
}


void loop() {

if (digitalRead(move_button) == LOW){
  back();
  }

  servo_potentiometer_reading = analogRead(servo_potentiometer); 
  speed_potentiometer_reading = analogRead(speed_potentiometer);

  int angle = map(servo_potentiometer_reading, 0, 1023, 0, 180);    //Servo_poteniometer 
  myservo.write(angle);

  int RPM = map(speed_potentiometer_reading, 0, 1023, 0, 600);  //RPM
  float velocity = 2 * 3.14159 * 3.25 * (RPM / 60.0); // cm/s
  int speedVal = map(speed_potentiometer_reading, 0, 1023, 0, 255);  //speed_potentiometer

  dstR = ultrasonic(trigR, echoR);
  dstL = ultrasonic(trigL, echoL);


  if (dstR <= 30 || dstL <= 30) {    //car read ultrasonic sensor
    stop();           
    delay(500);
    back();
    delay(1000);
    forward();
    state = " inp";
   }                                
  else {
    analogWrite(ena, speedVal);
    analogWrite(enb, speedVal);

    if (direction == true) {
      forward();   
    state = "Frd ";
    } else {
      back();
    state = " Rvs " ;
    }
  }

  if (digitalRead(print_button) == LOW) {
  Serial.println(velocity);              //cm/sec 
  Serial.println (state); 
  Serial.println(distanceCm);            //in cm 
  Serial.println(dstR);
  Serial.println(dstL);
  }

 if (digitalRead(print_button) == LOW) {
screenDisplay();
 }

  delay(50);
}

