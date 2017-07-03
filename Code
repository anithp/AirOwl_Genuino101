/*  Copyright (C)  2017  Anith Patel and Sohil Patel.
    Permission is granted to copy, distribute and/or modify this document
    under the terms of the GNU Free Documentation License, Version 1.3
    or any later version published by the Free Software Foundation;
    with no Invariant Sections, no Front-Cover Texts, and no Back-Cover Texts.
    A copy of the license is included in the section entitled "GNU
    Free Documentation License". */



#include<BlynkSimpleCurieBLE.h>
#include<CurieBLE.h>

#define BLYNK_SERIAL Print //writing data for blynk

byte data[24];//refer datasheet of the dust sensor

unsigned int PM1 = 0; //initializing values for the particulate matter
unsigned int PM25 = 0;
unsigned int PM10 = 0;
unsigned int count = 0;

const int PIN1 = 3;
const int PIN2 = 5;
const int PIN3 = 6;

char auth[] = "Auth key"; //paste auth key of Blynk

BLEPeripheral blePeripheral;


void setup() 
{
  blePeripheral.setLocalName("Airowl");     //setting BLE for blynk
  blePeripheral.setDeviceName("Airowl");
  blePeripheral.setAppearance(384);
  Blynk.begin(blePeripheral, auth);
  blePeripheral.begin();
  Serial1.begin(9600);                      //initialization of uart communication 
  Serial.begin(9600);
  pinMode(PIN1, OUTPUT);                   //led setup
  pinMode(PIN2, OUTPUT);
  pinMode(PIN3, OUTPUT);
}


void loop() 
{
 Blynk.run();                               //calls blynk
 blePeripheral.poll();
 Serial.println("Concentration of PM1 is:"); //prints in serial monitor
 Serial.println(PM1/count);
 Serial.println("Concentration of PM25 is:");
 Serial.println(PM25/count);
 Serial.println("Concentration of PM10 is:");
 Serial.println(PM10/count);
 delay(500);
 WriteData();                                //calls writedata function
 if ((PM1+PM25+PM10)/3 <= 100){
  LedColor(0, 255, 0);
  delay(500);
  }
 else if ((PM1+PM25+PM10)/3 <= 300 && (PM1+PM25+PM10)/3 > 300){
  LedColor(255, 0, 0);
  delay(500);
  } 
 else{
  LedColor(0, 0, 255);
  delay(500);
  }                                          //logic for the led data
}

void LedColor(int red, int blue, int green){
  analogWrite(PIN1, red);
  analogWrite(PIN2, blue);
  analogWrite(PIN3, green);
  }

void getWinsenData(void)                //function for getting data from sensor
{
  byte i = 0;
  int checksum = 0;
  Serial1.flush();
  while (Serial1.available()) {
    data[i] = Serial1.read();                //reads serial data from sensor
    if (i <= 21)                            //condition for the sensor reading data
    {
      checksum += data[i];
    }
    if (i == 23) {
      if (checksum == ((256 * data[22]) + data[23]))
      {
        if (data[0] == 66)
        {
          if (data[1] == 77)
          {
            if (i == 23)
            {
              PM1 += ((data[4] * 256) + data[5]);  
              //retrives and stores data for PM1
              PM25 += ((data[6] * 256) + data[7]); 
              //retrives and stores data for PM2.5
              PM10 += ((data[8] * 256) + data[9]);
              //retrives and stores data for PM10
              count++; 
              Serial1.flush(); //waits for serial data to transmit
              break;
            }
          }
        }
      }
      else
      {
        break;
      }
    }
    i++;
    delay(10);
  }
 } 



void WriteData()                              //sending data to blynk 
{
  getWinsenData();                            //calls getWinsendata function
  Blynk.virtualWrite(V1, PM1/count);          //write data to Blynk
  Blynk.virtualWrite(V2, PM25/count);         
  Blynk.virtualWrite(V3, PM10/count);
  delay(1000);
  
  }
