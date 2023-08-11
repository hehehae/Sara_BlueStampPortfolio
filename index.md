# Plant Monitor
<!--- Replace this text with a brief description (2-3 sentences) of your project. This description should draw the reader in and make them interested in what you've built. You can include what the biggest challenges, takeaways, and triumphs from completing the project were. As you complete your portfolio, remember your audience is less familiar than you are with all that your project entails! -->

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Sara N. | Los Gatos High School | Astronautical Engineering | Incoming Junior

<!--- **Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.** -->

![Headstone Image](logo.svg)
  
<!--- # Final Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.** 

<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE



# Second Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/y3VAmNlER5Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone -->

# First Milestone

<!--- **Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/CaCazFBhYKs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe> -->

&nbsp; My goal is to make a plant monitor that can be controlled by one's phone. To do this, the first main step was to make the Arduino Uno Rev3 board connect to Blynk through WiFi. Blynk is a software that can be used on mobile devices and enables its users to access their devices such as the Arduino thanks to a WiFi connection; in this project, Blynk will be used to check and use the plant monitor. But, before I could do that, I had to make the Arduino and ESP8266 connect to my WiFi. I configured the ESP8266 by wiring it with a breadboard to the Arduino and writing code in Arduino IDE. Once I successfully made my circuit and connected it to the same WiFi as Blynk, I was able to connect it to Blynk and finish my first milestone.

# Schematics 
<!--- Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resources to create professional schematic diagrams, though BSE recommends Tinkercad because it can be done easily and for free in the browser. -->

# Code

```c++
#include <SoftwareSerial.h>
SoftwareSerial espSerial(2, 3); //Rx,Tx

// Template ID, Device Name and Auth Token are provided by the Blynk.Cloud (mine are private)
// See the Device Info tab, or Template settings
#define BLYNK_TEMPLATE_ID "*****"
#define BLYNK_TEMPLATE_NAME "*****"
#define BLYNK_AUTH_TOKEN "*****"

#define BLYNK_PRINT Serial

#include <ESP8266_Lib.h>
#include <BlynkSimpleShieldEsp8266.h>

char auth[] = BLYNK_AUTH_TOKEN;

// WiFi credentials (mine are private)
char ssid[] = "*****";
char pass[] = "*****";

#include <SoftwareSerial.h>
SoftwareSerial EspSerial(2, 3); // RX, TX

#define ESP8266_BAUD 9600

ESP8266 wifi(&EspSerial);

#include <dht.h>
dht DHT;
#define DHT11_PIN 4
#define lightPin A0
#define moisturePin A1
#define pumpA 8

double roomHumidity = 0;
double roomTemperature = 0;

BlynkTimer timer;

BLYNK_WRITE(V0)
{
  if (param.asInt() == 1) {
    digitalWrite(pumpA, HIGH);
  } else {
    digitalWrite(pumpA, LOW);
  }
}

int readMoisture() {
  return analogRead(moisturePin);
}

int readLight() {
  return analogRead(lightPin);
}

bool readDHT() {
  int chk = DHT.read11(DHT11_PIN);
  switch (chk)
  {
    case DHTLIB_OK:
      roomHumidity = DHT.humidity;
      roomTemperature = DHT.temperature;
      return true;
    case DHTLIB_ERROR_CHECKSUM:
      break;
    case DHTLIB_ERROR_TIMEOUT:
      break;
    default:
      break;
  }
  return false;
}

void myTimerEvent()
{
  bool chk = readDHT();
  int light = readLight();
  int moisture = readMoisture();
  if (chk == true) {
    Blynk.virtualWrite(V4, roomHumidity);
    Blynk.virtualWrite(V5, roomTemperature);
  }
  Blynk.virtualWrite(V6, light);
  Blynk.virtualWrite(V7, moisture);
}

void setup()
{
  // Debug console
  Serial.begin(115200);

  // Set ESP8266 baud rate
  EspSerial.begin(ESP8266_BAUD);
  delay(10);

  Blynk.begin(auth, wifi, ssid, pass);
  // You can also specify server:
  //Blynk.begin(auth, wifi, ssid, pass, "blynk.cloud", 80);
  //Blynk.begin(auth, wifi, ssid, pass, IPAddress(192,168,1,100), 8080);

  timer.setInterval(1000L, myTimerEvent);

  pinMode(pumpA, OUTPUT);
}

void loop()
{
  Blynk.run();
  timer.run(); // Initiates BlynkTimer
}
```

# Bill of Materials

| **Part** | **Note** | **Price** | **Link**|
|:--:|:--:|:-:|:-:|
| Arduino Uno Rev3 | It is the main part of the circuit; a programable board that takes in commands from code. |
| Breadboard | It connects different components of the circuit together. |
| Wires | It connects different components of the circuit together. |
| ESP8266 Chip | It allows the circuit to connect to WiFi. |
| Photoresistor | It measures the intensity of light. |
| Resistor | It regulates the flow of the electrical current in a circuit. |
| DHTT Humidity Sensor | It measures the humidity and temperature of its environment. |
| Soil Moisture Module | It measures the amount of moisture in its environment. |
| Mini Water Pump & Tubing | It pumps water through the tubing and waters the plant. | $2.75/piece | https://www.amazon.com/Sipytoph-Submersible-Flexible-Aquariums-Hydroponics/dp/B097F4576N/ref=sr_1_10?crid=26A14MLYTSWWB&keywords=DC+mini+water+pump+DC&qid=1691594612&sprefix=dc+mini+water+pump+dc%2Caps%2C134&sr=8-10 |
