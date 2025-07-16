/**
 * @file gps_gsm_tracker.ino
 * @brief A simple GPS tracker that sends its location via SMS on startup.
 * @description This sketch acquires a GPS location, displays it on an I2C LCD,
 * and sends a Google Maps link to a predefined phone number using a GSM module.
 * The entire process runs once in the setup() function.
 */

// --- Include Libraries ---
#include <SoftwareSerial.h> // For creating software-based serial ports
#include <TinyGPS++.h>      // For parsing GPS data
#include <LiquidCrystal_I2C.h> // For controlling the I2C LCD

// --- Constants & Pin Definitions ---
#define GSM_TX_PIN 2
#define GSM_RX_PIN 3
#define GPS_TX_PIN 8
#define GPS_RX_PIN 9

#define I2C_ADDR 0x27 // I2C address of your LCD
#define LCD_COLS 16   // LCD number of columns
#define LCD_ROWS 2    // LCD number of rows

// IMPORTANT: Replace with the destination phone number
const char* DESTINATION_PHONE = "+919121072793";

// --- Object Declarations ---
LiquidCrystal_I2C lcd(I2C_ADDR, LCD_COLS, LCD_ROWS);
TinyGPSPlus gps;
SoftwareSerial gsmSerial(GSM_TX_PIN, GSM_RX_PIN);
SoftwareSerial gpsSerial(GPS_TX_PIN, GPS_RX_PIN);

// --- Global Variables ---
float latitude = 0.0;
float longitude = 0.0;

//====================================================================================
// SETUP: Runs once at the beginning
//====================================================================================
void setup() {
  // Start the hardware serial port for debugging
  Serial.begin(9600);
  Serial.println(F("System Booting Up..."));

  // Initialize LCD
  lcd.init();
  lcd.backlight();
  lcd.setCursor(0, 0);
  lcd.print(F("GPS Tracker Init"));

  // Start the software serial ports
  gpsSerial.begin(9600);
  gsmSerial.begin(9600);

  // --- Main Logic ---
  // This sequence runs only once. For continuous tracking, move these calls to loop().
  acquireGpsLocation();
  displayLocationOnLcd(latitude, longitude);
  sendLocationSms(latitude, longitude);

  Serial.println(F("Process complete. Entering idle state."));
  lcd.setCursor(0, 1);
  lcd.print(F("SMS Sent!"));
}

//====================================================================================
// LOOP: Runs repeatedly after setup (currently empty)
//====================================================================================
void loop() {
  // This is intentionally left blank to match the original code's behavior.
  // To make the tracker send updates continuously, you could move the function
  // calls from setup() here, perhaps with a delay.
}

//====================================================================================
// --- Custom Functions ---
//====================================================================================

/**
 * @brief Waits for a valid GPS signal and updates global location variables.
 */
void acquireGpsLocation() {
  Serial.println(F("Searching for GPS signal..."));
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print(F("Acquiring GPS..."));

  // Loop indefinitely until a valid location is found
  while (true) {
    // Feed data from the GPS module to the TinyGPS++ object
    while (gpsSerial.available() > 0) {
      gps.encode(gpsSerial.read());
    }

    // If the location has been updated, store it and exit the loop
    if (gps.location.isUpdated()) {
      latitude = gps.location.lat();
      longitude = gps.location.lng();
      Serial.println(F("GPS Lock Acquired!"));
      break; // Exit the while(true) loop
    }
  }
}

/**
 * @brief Displays the latitude and longitude on the Serial Monitor and LCD.
 * @param lat The latitude to display.
 * @param lon The longitude to display.
 */
void displayLocationOnLcd(float lat, float lon) {
  // Print to Serial Monitor for debugging
  Serial.print(F("LATTITUDE= ")); Serial.println(lat, 6);
  Serial.print(F("LONGITUDE=")); Serial.println(lon, 6);

  // Print to LCD
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print(F("LAT: ")); lcd.print(lat, 6);
  lcd.setCursor(0, 1);
  lcd.print(F("LON: ")); lcd.print(lon, 6);
  delay(1000);
}

/**
 * @brief Sends the location coordinates via SMS using the GSM module.
 * @param lat The latitude to send.
 * @param lon The longitude to send.
 */
void sendLocationSms(float lat, float lon) {
  Serial.println(F("Setting up GSM module to send SMS..."));
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print(F("Sending SMS..."));

  // Set GSM module to Text Mode
  gsmSerial.println("AT+CMGF=1\r");
  delay(1000);

  // Set the recipient's phone number
  gsmSerial.print("AT+CMGS=\"");
  gsmSerial.print(DESTINATION_PHONE);
  gsmSerial.println("\"\r");
  delay(1000);

  // Construct and send the message body
  gsmSerial.print(F("http://maps.google.com/maps?q="));
  gsmSerial.print(lat, 6);
  gsmSerial.print(",");
  gsmSerial.print(lon, 6);
  gsmSerial.print(F("\r\r HELP!!!!")); // Your custom message
  delay(1000);

  // Send the Ctrl+Z character (ASCII 26) to signal the end of the message
  gsmSerial.write(0x1A);
  delay(1000);

  Serial.println(F("SMS send command issued."));
}
