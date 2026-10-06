WeMos D1 Mini Currency Exchange Rate Monitor with I2C LCD
Overview
An IoT-based currency exchange rate display built on the WeMos D1 Mini (ESP8266) that fetches live USD exchange rates from the ExchangeRate-API and displays them on a 16x2 I2C LCD (1602). The device cycles through four informational pages, showing conversion rates and the last update timestamp.

Hardware Requirements
Component Description
WeMos D1 Mini ESP8266-based WiFi microcontroller
16x2 LCD with I2C backpack Typically PCF8574, default address 0x27
Jumper wires For I2C connection
USB power source 5V via micro-USB
Wiring (I2C)
LCD I2C Pin WeMos D1 Mini Pin
GND GND
VCC 5V (or 3.3V depending on module)
SDA D2 (GPIO4)
SCL D1 (GPIO5)
Features
WiFi Connectivity – Connects to a local WiFi network on boot and displays connection status on the LCD.

Live Exchange Rates – Fetches real-time rates from exchangerate-api.com over HTTPS (SSL validation skipped for simplicity).

Multiple Currency Pairs – Displays USD → EUR, GBP, ZAR, and CAD conversion rates.

Auto-Paging Display – Cycles through 4 pages every 5 seconds:

USD → EUR / USD → GBP
USD → ZAR / USD → CAD
Last update date
Last update time
Serial Debugging – Prints HTTP response, JSON parsing errors, and WiFi status to the serial monitor at 115200 baud.

Software Dependencies
Install these via the Arduino IDE Library Manager:

Wire.h (built-in)

LiquidCrystal_I2C (Frank de Brabander)

ESP8266WiFi (ESP8266 board package)

WiFiClientSecure (ESP8266 board package)

ESP8266HTTPClient (ESP8266 board package)

ArduinoJson (Benoit Blanchon, v6.x)

Configuration
WiFi credentials – Create a secrets.h file in the same sketch folder:

const char* ssid = "YourWiFiSSID";
const char* password = "YourWiFiPassword";
API key – Replace the API key in api_url with your own from exchangerate-api.com:

const char* api_url = "https://v6.exchangerate-api.com/v6/YOUR_API_KEY/latest/USD";
LCD I2C address – If the display stays blank, run an I2C scanner sketch. Common addresses are 0x27 or 0x3F. Update:

LiquidCrystal_I2C lcd(0x27, 16, 2);
How It Works
Setup phase – Initializes the LCD, connects to WiFi (showing status messages), then calls fetchExchangeRates() once.

Fetch phase – Sends an HTTPS GET request, parses the JSON response with ArduinoJson, and extracts:

conversion_rates.EUR, .GBP, .ZAR, .CAD

time_last_update_utc (split into date and time strings)

Loop phase – Every 5 seconds, currentPage increments modulo 4, and displayPage() refreshes the LCD with the corresponding content.

Sample LCD Output
Page 1:   USD-EUR: 0.92
          USD-GBP: 0.79

Page 2:   USD-ZAR: 18.15
          USD-CAD: 1.36

Page 3:   Last Update:
          Mon, 25 Nov 2024

Page 4:   Time:
          00:00:01 +0000
Notes & Limitations
Rates are fetched only once at boot. To refresh periodically, move fetchExchangeRates() into the loop() with a timer (e.g., every 30–60 minutes) to respect API rate limits.

SSL validation is disabled (client.setInsecure()) to simplify certificate handling. For production, load proper root certificates.

The updateDate buffer is 20 bytes and truncates the timestamp to 16 characters for the date portion — verify the API response format matches.

The I2C LCD backpack typically needs 5V; confirm your specific module's voltage requirements before connecting to the 3.3V rail.
