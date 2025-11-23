EXP 11 RETAINED MSG AND CLEAN SESSION


    mosquitto_pub -h localhost -t lab/retained -m "Hello retained" -r
    mosquitto_sub -h localhost -t lab/retained -c -i "clean-client"
    mosquitto_sub -h localhost -t lab/retained -q 1 --disable-clean-session -i "persist-client"
    mosquitto_pub -h localhost -t lab/retained -n -r
    mosquitto_sub -h localhost -t lab/retained



EXP 10 SENDING AND RECEIVING MQTT MSG

1.  mosquitto_sub -h localhost -t test/topic
2.  mosquitto_pub -h localhost -t test/topic -m "MOHIT"


EXP 9 (Bluetooth Home automation)

    const int LIGHT_PIN = 8;
    const int FAN_PIN   = 9;
    
    void setup() {
      Serial.begin(9600);
      pinMode(LIGHT_PIN, OUTPUT);
      pinMode(FAN_PIN, OUTPUT);
      digitalWrite(LIGHT_PIN, LOW);
      digitalWrite(FAN_PIN, LOW);
      Serial.println("Home Automation Ready. Send commands: LON, LOFF, FON, FOFF");
    }
    
    void loop() {
      if (Serial.available() > 0) {
       
      String input = Serial.readStringUntil('\n');
      input.trim();           
      input.toUpperCase();   
      Serial.print("Received: ");
      Serial.println(input);
      
      if (input == "LON") {
        digitalWrite(LIGHT_PIN, HIGH);
        Serial.println("Light ON");
      } else if (input == "LOFF") {
        digitalWrite(LIGHT_PIN, LOW);
        Serial.println("Light OFF");
      } else if (input == "FON") {
        digitalWrite(FAN_PIN, HIGH);
        Serial.println("Fan ON");
      } else if (input == "FOFF") {
        digitalWrite(FAN_PIN, LOW);
        Serial.println("Fan OFF");
      } else if (input.length() == 0) {
        
      } else {
        Serial.println("Unknown command. Use LON, LOFF, FON, FOFF");
      }
      }
    }



<img width="1386" height="729" alt="image" src="https://github.com/user-attachments/assets/385e8765-e323-4eaa-badf-0572f0ac64fc" />


EXP 8 (L293D)

    const int motorPWM = 9;
    const int motorIn1 = 8;
    const int motorIn2 = 7;
    
    int pwmValues[] = {0, 50, 100, 150, 200, 255};
    const int maxRPM = 3000;
    
    void setup() {
      pinMode(motorPWM, OUTPUT);
      pinMode(motorIn1, OUTPUT);
      pinMode(motorIn2, OUTPUT);
      Serial.begin(9600);
    
      Serial.println("PWM\tDuty(%)\tRPM\t\tBehavior\tDirection");
      Serial.println("-----------------------------------------------------------");
    }
    
    void loop() {
      runMotor("Clockwise");
      runMotor("Anticlockwise");
    }
    
    void runMotor(String direction) {
      if (direction == "Clockwise") {
        digitalWrite(motorIn1, HIGH);
        digitalWrite(motorIn2, LOW);
      } else {
        digitalWrite(motorIn1, LOW);
        digitalWrite(motorIn2, HIGH);
      }
    
      for (int i = 0; i < 6; i++) {
        int pwm = pwmValues[i];
        float duty = (pwm / 255.0) * 100;
        float rpm = (duty / 100.0) * maxRPM;
        String behavior = (pwm > 0) ? "ON" : "OFF";
    
        analogWrite(motorPWM, pwm);
        delay(1000);
        printRow(pwm, duty, rpm, behavior, direction);
      }
    }
    
    void printRow(int pwm, float duty, float rpm, String behavior, String direction) {
      Serial.print(pwm);
      Serial.print("\t");
    
      if (duty < 10) Serial.print(" ");
      Serial.print(duty, 1);
      Serial.print("%\t");
    
      if (rpm < 1000) Serial.print(" ");
      Serial.print(rpm, 0);
      Serial.print(" RPM\t");
    
      Serial.print(behavior);
      Serial.print("\t\t");
    
      Serial.println(direction);
    }



<img width="1226" height="689" alt="image" src="https://github.com/user-attachments/assets/83fbfef3-f4b1-4598-803b-f440fe896438" />


EXP 7 (LCD interfacing)

    #include <LiquidCrystal.h>
    int sensor=A0;
    int raw=0;
    float voltage=0;
    float tempc_lm35=0;
    float tempc_tmp36=0;
    float tempf=0;
    float Vref=5.0;
    
    LiquidCrystal lcd(12,11,5,4,3,2);
    
    void setup(){
      lcd.begin(16,2);
      Serial.begin(9600);
    }
    
    void loop(){
      raw=analogRead(sensor);
      voltage = raw * Vref / 1023.0;
      tempc_lm35 = voltage * 100.0;
      tempc_tmp36 = (voltage - 0.5) * 100.0;
      tempf = tempc_lm35 * 1.8 + 32.0;
    
      lcd.setCursor(0,0);
      lcd.print("C=");
      lcd.print(tempc_lm35,1);
      if(tempc_lm35<10.0) lcd.print("   "); else if(tempc_lm35<100.0) lcd.print("  ");
      lcd.setCursor(8,0);
      lcd.print("F=");
      lcd.print(tempf,1);
      if(tempf<10.0) lcd.print("   "); else if(tempf<100.0) lcd.print("  ");
    
      lcd.setCursor(0,1);
     
    
      
      Serial.print("  C=");
      Serial.print(tempc_tmp36,2);
      Serial.print("  F=");
      Serial.println(tempf,2);
    
      delay(500);
    }

<img width="1036" height="697" alt="image" src="https://github.com/user-attachments/assets/500ce640-7ad0-421e-8a43-8fa983a4bede" />



EXP 6 (Soil Moisture)

    int moistureSensorPin = A0; 
    int relayPin = 12;               // Relay control pin
    int moistureThreshold = 500; 
    
    int redPin = 3;
    int greenPin = 5;
    int bluePin = 6;
    
    void setup() {
      Serial.begin(9600);
      
      pinMode(relayPin, OUTPUT);
      digitalWrite(relayPin, HIGH); // Relay OFF initially (assuming active LOW relay)
    
      pinMode(redPin, OUTPUT);
      pinMode(greenPin, OUTPUT);
      pinMode(bluePin, OUTPUT);
    }
    
    void loop() {
      int moistureValue = analogRead(moistureSensorPin);
      Serial.print("Soil Moisture: ");
      Serial.println(moistureValue);
    
      if (moistureValue < moistureThreshold) {
        digitalWrite(relayPin, LOW);  // Relay ON (pump ON)
        Serial.println("Pump ON - Soil is dry");
        setRGB(255, 0, 0);            // Red LED
      } else {
        digitalWrite(relayPin, HIGH); // Relay OFF (pump OFF)
        Serial.println("Pump OFF - Soil is wet");
        setRGB(0, 255, 0);            // Green LED
      }
    
      delay(1000);
    }
    
    void setRGB(int redVal, int greenVal, int blueVal) {
      analogWrite(redPin, redVal);
      analogWrite(greenPin, greenVal);
      analogWrite(bluePin, blueVal);
    }


<img width="1182" height="697" alt="image" src="https://github.com/user-attachments/assets/d0ac51fc-a9ac-4caf-a3ca-81a1e42783d4" />



EXP 5 (TEMP Sensor tmp 36)


    int baselineTemp = 40; // baseline temperature threshold
    float celsius = 0;
    float fahrenheit = 0;
    
    void setup() {
      Serial.begin(9600);
      pinMode(2, OUTPUT);
      pinMode(3, OUTPUT);
      pinMode(4, OUTPUT);
    }
    
    void loop() {
      int sensorValue = analogRead(A0);
      float voltage = sensorValue * (5.0 / 1023.0);
      celsius = (voltage - 0.5) * 100.0;
      fahrenheit = ((celsius * 9) / 5) + 32;
    
      Serial.print(celsius);
      Serial.print(" C, ");
      Serial.print(fahrenheit);
      Serial.println(" F");
    
      if (celsius < baselineTemp) {
        digitalWrite(2, LOW);
        digitalWrite(3, LOW);
        digitalWrite(4, LOW);
      }
      else if (celsius >= baselineTemp && celsius < baselineTemp + 10) {
        digitalWrite(2, HIGH);
        digitalWrite(3, LOW);
        digitalWrite(4, LOW);
      }
      else if (celsius >= baselineTemp + 10 && celsius < baselineTemp + 20) {
        digitalWrite(2, HIGH);
        digitalWrite(3, HIGH);
        digitalWrite(4, LOW);
      }
      else if (celsius >= baselineTemp + 20) {
        digitalWrite(2, HIGH);
        digitalWrite(3, HIGH);
        digitalWrite(4, HIGH);
      }
    
      delay(1000);
    }


<img width="1025" height="722" alt="image" src="https://github.com/user-attachments/assets/14af761c-3030-41ff-be7f-bce8c59ac6fd" />



EXP 4 (Relay SPDT ->Single pole double transfer)


    int photoResistorSensor = 0;
    int relayPinOutput = 4;
    void setup(){
    	pinMode(A0,INPUT);
      	pinMode(relayPinOutput,OUTPUT);
      	Serial.begin(9600);	
      	
    }
    
    void loop(){
    	int photoResistorSensor = analogRead(A0);
      	Serial.println(photoResistorSensor);
      	if(photoResistorSensor > 400){
      		Serial.println("Sufficient Light Outside");
          	digitalWrite(relayPinOutput,LOW);
        }
      else
      {
      		Serial.println("In Sufficient Light Outside");
          	digitalWrite(relayPinOutput,HIGH);
      }
    }

<img width="1230" height="631" alt="image" src="https://github.com/user-attachments/assets/16a456be-f4ef-4fbf-8527-10aa7de415e4" />


EXP 3 (Ultrasonic Sensor)

    int trigPin=10;
    int echoPin=9;
    unsigned long t;
    float distance;
    float prevDistance=-1.0;
    
    void setup(){
      pinMode(trigPin,OUTPUT);
      pinMode(echoPin,INPUT);
      Serial.begin(9600);
    }
    
    void loop(){
      digitalWrite(trigPin,LOW);
      delayMicroseconds(2);
      digitalWrite(trigPin,HIGH);
      delayMicroseconds(10);
      digitalWrite(trigPin,LOW);
    
      t=pulseIn(echoPin,HIGH,30000UL);
      if(t==0){
        distance=-1.0;
      } else {
        distance=t*0.0343/2.0;
      }
    
      if ( (distance<0 && prevDistance!=distance) || (distance>=0 && fabs(distance-prevDistance) > 0.5) ) {
        if(distance<0){
          Serial.println("Out of range");
        } else {
          Serial.print("Distance: ");
          Serial.print(distance);
          Serial.println(" cm");
        }
        prevDistance=distance;
      }
    
      delay(200);
    }
<img width="1239" height="642" alt="image" src="https://github.com/user-attachments/assets/98fc1281-4b77-4981-a8ab-891291749032" />



EXP 2 (LDR & PIR -> Pssive Infrared)


    #define LED  9  // choose the pin for the RELAY
    #define PIR 4 
    int ldr=0;
    int pir=LOW;
    //int val;
    void setup()
    {    
      Serial.begin(9600);
      pinMode(LED, OUTPUT); 
      pinMode(PIR,INPUT);
       
    }
    void loop() 
    {
      ldr = analogRead(A1);
     // val=(255.0/1023.0)*ldr;
      pir = digitalRead(PIR); // read input value
      Serial.println(ldr);
     // Serial.println(val);
      Serial.println(pir);
    
     if((ldr<=300) && ( pir==HIGH) ){
           digitalWrite(LED,HIGH);  // Turn ON the light
           delay(250);
           //pir=HIGH; 
           
     
    }
    else {
      
           digitalWrite(LED,LOW); // Turn OFF the light
          // pir=LOW;
    }
     }


<img width="1197" height="523" alt="image" src="https://github.com/user-attachments/assets/a53fb511-fa11-42a2-9124-0647a486b770" />

EXP 1 (LED blinking)


    int ledPin=8; 
    void setup()
    {
        pinMode(ledPin,OUTPUT);    
    }
    void loop()
    {  
        digitalWrite(ledPin,HIGH); 
        delay(1000);               
        digitalWrite(ledPin,LOW);  
        delay(1000);               
    } 

<img width="1109" height="654" alt="image" src="https://github.com/user-attachments/assets/f404bd7a-c7f8-4c51-9332-b01a10ed0642" />
