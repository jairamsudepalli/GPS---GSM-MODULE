# GPS---GSM-MODULE
# 🛰️ Arduino GPS + GSM Location Tracker

A simple and effective real-time location tracker built with an Arduino Uno, a NEO-6M GPS module, and a SIM800L GSM module. This project periodically acquires GPS coordinates and sends them as an SMS to a predefined phone number.

It's an ideal starting point for projects involving:

  * Asset tracking and monitoring
  * Vehicle location systems
  * Personal safety devices

## ✨ Features

  * **Real-Time Tracking:** Fetches live latitude and longitude data.
  * **SMS Alerts:** Transmits location coordinates directly to your mobile phone.
  * **Low-Cost Components:** Built with widely available and affordable modules.
  * **Customizable:** Easily adaptable for different use cases and alert conditions.

## 🛠️ Hardware Required

  * Arduino Uno (or any compatible board)
  * NEO-6M GPS Module
  * SIM800L GSM/GPRS Module
  * SIM Card (with credit/SMS plan)
  * Dedicated Power Supply (e.g., LiPo battery or a 5V 2A buck converter)
  * Jumper Wires

## 📚 Software & Libraries

  * **[Arduino IDE](https://www.arduino.cc/en/software)**
  * **[TinyGPS++ Library](https://www.google.com/search?q=http://arduiniana.org/libraries/tinygpsplus/)** by Mikal Hart
      * This is a powerful library for parsing NMEA sentences from GPS modules.

#### To Install the `TinyGPS++` Library:

1.  Open the Arduino IDE.
2.  Go to `Sketch` \> `Include Library` \> `Manage Libraries...`.
3.  In the search box, type `TinyGPS++` and press Enter.
4.  Find the library by Mikal Hart and click **Install**.

## 🔌 Wiring Diagram

This project uses `SoftwareSerial` to create two separate serial ports, allowing the Arduino to communicate with both the GPS and GSM modules simultaneously.

| Module          | Module Pin | Arduino Pin | Notes                             |
| --------------- | ---------- | ----------- | --------------------------------- |
| **GPS Module** | `TX`       | `D4`        | GPS Transmit to Arduino Receive   |
|                 | `RX`       | `D3`        | Arduino Transmit to GPS Receive   |
| **GSM Module** | `TX`       | `D7`        | GSM Transmit to Arduino Receive   |
|                 | `RX`       | `D8`        | Arduino Transmit to GSM Receive   |

## 🚀 Getting Started

Follow these steps to get your tracker up and running.

### 1\. Assemble the Hardware

Connect the components according to the wiring diagram above. Ensure all connections are secure.

### 2\. Configure the Code

In your Arduino sketch, you **must** update the destination phone number inside the `sendSMS()` function.

```cpp
// Function to send SMS with location data
void sendSMS() {
  Serial.println("Initializing SMS...");
  
  // Replace +91XXXXXXXXXX with the destination mobile number
  gsmSerial.println("AT+CMGS=\"+91XXXXXXXXXX\""); 
  
  delay(100);
  
  // Your SMS message content
  String smsMessage = "Location Update: http://maps.google.com/maps?q=";
  smsMessage += String(latitude, 6);
  smsMessage += ",";
  smsMessage += String(longitude, 6);

  gsmSerial.print(smsMessage);
  delay(100);
  
  gsmSerial.write(26); // Send Ctrl+Z to end the message
  delay(1000);
  
  Serial.println("SMS Sent!");
}
```

### 3\. Upload the Sketch

Upload the configured sketch to your Arduino Uno.

### 4\. Deploy

Power up the system and wait for the modules to initialize. Place the tracker in a location where it can receive a satellite signal.

## ⚠️ Important Notes

> ### **Power Supply is Critical**
>
> The **SIM800L GSM module is power-hungry** and can draw up to **2A** in short bursts when connecting to the network.
>
>   * **Do NOT power the SIM800L directly from the Arduino's 5V pin.** This will cause the system to constantly reset.
>   * Use a dedicated power source like a **1S LiPo battery (3.7V)** or a **buck converter capable of delivering 5V at 2A**.
>   * The GPS module requires less power (\~20mA) and can be safely powered by the Arduino's 5V output.

> ### **Getting a GPS Fix**
>
>   * For the initial satellite lock, place the GPS module near a window or outdoors.
>   * It may take several minutes to get the first valid location coordinates. A blinking LED on the GPS module usually indicates it has a fix.
