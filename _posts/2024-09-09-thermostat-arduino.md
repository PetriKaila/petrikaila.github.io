---
title: Arduino & Raspberry Pi
description: ESP32-cam testing
categories: [Projects, Thermostat]
date: 2024-09-09 20:57 +0300
---
{% include embed/youtube.html id='ClcseTeFdvY' %}

    //Petri Kaila ATIS21K
    #include <LiquidCrystal.h>
    #include "DHT.h"
    #define DHTPIN 13   
    #define DHTTYPE DHT11 
    #define HEATER 7
    #define COOLER 2
    #define LEDR 8 
    #define LEDG 9
    #define LEDB 10
    #define POT A1
    DHT dht(DHTPIN, DHTTYPE);
    const int rs = 12, en = 11, d4 = 6, d5 = 5, d6 = 4, d7 = 3;
    LiquidCrystal lcd(rs, en, d4, d5, d6, d7);
    int sensorValue = 0;
    
    void setup() {
    	Serial.begin(9600);
      dht.begin();
      lcd.begin(16, 2);
      pinMode(HEATER, OUTPUT);
      pinMode(COOLER, OUTPUT);
      pinMode(LEDR, OUTPUT);
      pinMode(LEDB, OUTPUT);
      pinMode(LEDG, OUTPUT);
    }
    double getTemp() {
    return dht.readTemperature();
    }
    void setThermo(double temp, double setpoint) {
      const double margin = 1.0;
      digitalWrite(HEATER, LOW);
      digitalWrite(COOLER, LOW);
      digitalWrite(LEDR, LOW);
      digitalWrite(LEDG, LOW);
      digitalWrite(LEDB, LOW);
    
    
      if ((setpoint - margin) <= temp && temp <= (setpoint + margin)){
        digitalWrite(LEDG, HIGH);
      }
      else if (temp > (setpoint + margin))
      {
        digitalWrite(LEDR, HIGH);
        if(temp > (setpoint + margin + 1))
        {
          digitalWrite(COOLER, HIGH);
        }
      }
      else if (temp < (setpoint - margin))
      {
        digitalWrite(LEDB, HIGH);
        if(temp < (setpoint - margin))
        {
          digitalWrite(HEATER, HIGH);
        }
      }
    }  
    float scale(float value,int x1,int x2,int y1,int y2) 
    {
      value = (value-x1)/(x2-x1) * (y2-y1) + y1;
      return value;
    }
    
    
    void loop() {
      sensorValue = analogRead(POT);
      double setpoint = map(sensorValue, 0, 1023, 15, 25);
      float setpointScaled = scale(sensorValue,0,1023,15,25);              
    
      double temp = getTemp();
      lcd.setCursor(0, 0);
      lcd.print("Set:");
     lcd.print(setpointScaled);
        lcd.print(" C");
    
    lcd.setCursor(0, 1);
    lcd.print("Now:");
    lcd.print(temp);
    lcd.print(" C");
    
    setThermo(temp, setpoint);
    delay(200); 
    
    }




