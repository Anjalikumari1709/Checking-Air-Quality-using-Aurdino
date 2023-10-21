# Checking-Air-Quality-using-Aurdino
Air Quality Sensing and Improving Device
#include "DHTesp.h"
#include "MQ135.h"
#include <Wire.h>
#include <ESP8266WiFi.h>
#include <ThingSpeak.h>
#include <WiFiClient.h> 
#include "Adafruit_GFX.h"
#include "Adafruit_SSD1306.h"



#define SCREEN_WIDTH 128    // OLED display width, in pixels
#define SCREEN_HEIGHT 64 
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT,&Wire, -1);
 


const char *ssid =  "vivo 1907";     // Enter your WiFi Name
const char *pass =  "aashujha"; // Enter your WiFi Password
WiFiClient client; 
unsigned long myChannelNumber =   1742248; //Your channel Id
const char * myWriteAPIKey = "XEA86HIHNHR6LQDR"; //Write api key

const int sensorPin= 0;     // dht11
DHTesp dht;



int air_quality;         // mq135



void setup() 
{
       Serial.begin(115200);           //dht11
       Serial.println();
        display.begin(SSD1306_SWITCHCAPVCC, 0x3C); //initialize with the I2C addr 0x3C (128x64)
        display.clearDisplay();
        delay(10);
       Serial.println("Status\t\tHumidity (%)\t\tTemperature (C)\t");
       dht.setup(D4, DHTesp::DHT11); 
       delay(10);

       
  display.clearDisplay();
  display.setCursor(0,0);  
  display.setTextSize(1);
  display.setTextColor(WHITE);
  display.println("Connecting to ");
  display.setTextSize(2);
  display.print(ssid);
  display.display();
       
       Serial.println("Connecting to ");
       Serial.println(ssid);
       WiFi.begin(ssid, pass);
       while (WiFi.status() != WL_CONNECTED) 
     {
            delay(550);
            Serial.print(".");
     }
      Serial.println("");
      Serial.println("WiFi connected");


       display.clearDisplay();
    display.setCursor(0,0);  
    display.setTextSize(1);
    display.setTextColor(WHITE);
    display.print("WiFi connected");
    display.display();
    delay(4000);

      Serial.println(WiFi.localIP()); 
      ThingSpeak.begin(client); 
 }
void loop() 
{
     //delay(dht.getMinimumSamplingPeriod() + 100);
delay(1500);
float humidity = dht.getHumidity();
float temperature = dht.getTemperature();
Serial.print("Status: ");
Serial.print(dht.getStatusString());
Serial.print("\t");
Serial.print("Humidity (%): ");
Serial.print(humidity, 1);
Serial.print("\t");
Serial.print("Temperature (C): ");
Serial.print(temperature, 1);
Serial.print("\t");
Serial.println();
                             ThingSpeak.writeField(myChannelNumber, 2, humidity , myWriteAPIKey); 
                             ThingSpeak.writeField(myChannelNumber, 3, temperature , myWriteAPIKey);

    display.clearDisplay();
    display.setCursor(0,0);  //oled display
    display.setTextSize(1);
    display.setTextColor(WHITE);
    display.println("humidity & Temp");
    
  
    display.setCursor(0,20);  //oled display
    display.setTextSize(2);
    display.setTextColor(WHITE);
    display.print(temperature);
    display.setTextSize(1);
    display.setTextColor(WHITE);
    display.println(" c");
    display.display();          
         


        
 MQ135 gasSensor = MQ135 (A0); 
air_quality = gasSensor.getPPM();
Serial.print ("Air Quality: ");

Serial.print (air_quality); 
Serial.println(" PPM (Parts Per Million)") ;    



      display.clearDisplay();
    display.setCursor(0,0);  //oled display
    display.setTextSize(1);
    display.setTextColor(WHITE);
    display.println("Air Quality ");
    
  
    display.setCursor(0,20);  //oled display
    display.setTextSize(2);
    display.setTextColor(WHITE);
    display.print(air_quality);
    display.setTextSize(1);
    display.setTextColor(WHITE);
    display.println(" PPM");
    display.display();
          
          Serial.println("Waiting...");
          ThingSpeak.writeField(myChannelNumber,4,air_quality ,myWriteAPIKey);
    delay(10000);
}
