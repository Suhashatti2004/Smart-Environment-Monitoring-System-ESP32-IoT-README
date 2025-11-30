# Smart-Environment-Monitoring-System-ESP32-IoT-README
A lightweight ESP32-based IoT project that reads temperature, humidity (DHT11), and ambient light (LDR) and sends JSON telemetry to a remote HTTP server. It also includes a buzzer alert when the environment becomes dark.

🔧 Features
Reads temperature & humidity from a DHT11 sensor
Reads ambient light using an LDR (photoresistor) on ADC pin
Sends telemetry as JSON via HTTP POST to a configurable server
Visual and audible alert via buzzer when brightness is below threshold
Robust Wi‑Fi connection handling and event callbacks
Uses ArduinoJson for safe JSON serialization

📦 Hardware
ESP32 development board
DHT11 sensor (data pin → GPIO 4)
LDR (voltage divider) connected to ADC pin GPIO 34
Buzzer connected to GPIO 5
Breadboard, jumper wires, pull-down or pull-up resistor for DHT/LDR circuit

Suggested wiring
DHT11 VCC → 3.3V
DHT11 GND → GND
DHT11 DATA → GPIO 4
LDR: form a voltage divider (LDR + fixed resistor). Center tap → GPIO 34 (ADC1_CH6)
Buzzer → GPIO 5 (use transistor + diode if it's an active buzzer that draws significant current)

🧩 Software / Libraries
Arduino core for ESP32
DHT.h (DHT sensor library)
WiFi.h (ESP32 Wi-Fi)
HTTPClient.h (HTTP requests)
ArduinoJson (for JSON serialization)
Install libraries via Library Manager or PlatformIO. The code already includes #include <ArduinoJson.h> to avoid JSON-related errors.

🔢 Configuration
Edit the top of the sketch to set your Wi‑Fi credentials and server endpoint:
char ssid[] = "YOUR_SSID";
char pass[] = "YOUR_PASSWORD";
const char* serverName = "http://YOUR_SERVER/api/v1/your_device/telemetry";

Also confirm pin assignments if you use different wiring:
#define BUZZER_PIN 5
#define LDR_PIN 34
#define DHTPIN 4
#define DHTTYPE DHT11

▶️ How it works (overview)
On setup(), the ESP32 initializes serial, the DHT sensor, configures I/O pins, and attempts to connect to Wi‑Fi.
The code registers Wi‑Fi event callbacks to update wifi_status and reconnect automatically when necessary.
Every 2 seconds (configurable interval), the loop:
Reads DHT11 temperature and humidity
Reads ADC value from the LDR and computes a brightness percentage
Sounds the buzzer when brightness < 20%
If Wi‑Fi is connected, it composes a JSON object and posts it to serverName using HTTPClient.
HTTP responses and errors are printed to the serial console for debugging.

✅ Advantages
Simple, practical IoT telemetry pipeline demonstrating sensor reading → preprocessing → HTTP transmission
Event-driven Wi‑Fi handling increases robustness to disconnects
Uses ArduinoJson to ensure well-formed JSON
Low-latency updates (every 2 seconds) and clear serial debug outputs
Easily extensible: add more sensors, change transport to HTTPS/MQTT, or store locally

⚠️ Disadvantages & Limitations
DHT11 accuracy: DHT11 is inexpensive but less accurate and slower than DHT22 or BME280
Plain HTTP: Data is sent over HTTP — not encrypted. Use HTTPS or MQTT with TLS for production data security
Blocking HTTPClient: HTTPClient calls are blocking; a long server timeout can stall the loop
ADC scaling: ADC range used (0-4095) is correct for default ESP32 ADC attenuation but may vary by board/config
No retry/backoff for failed POSTs: currently skips sending until next interval
Hard-coded Wi‑Fi credentials in the sketch (consider using secure storage or provisioning)

📡 Server API
The sketch sends a JSON payload like:
{
"temperature": "25.50",
"humidity": "45.20",
"brightness": "78.34"
}
Modify the server endpoint to match your backend. In the example the server is http://54.251.153.72:8080/api/v1/bpehxziearrbicgxmt5k/telemetry.

⚙️ How to Deploy
Open the sketch in Arduino IDE or PlatformIO.
Set the correct board to an ESP32 Dev Module.
Install the libraries (DHT sensor library, ArduinoJson).
Update Wi‑Fi credentials and serverName.
Upload to the board and open Serial Monitor (115200 baud).

📸 Debugging Tips
Use the Serial Monitor to view JSON payloads and HTTP response codes
If DHT reads fail often, add a small delay after power-up and ensure wiring is solid
If ADC readings are noisy, add a capacitor across the LDR divider or compute a running average
For Wi‑Fi problems, check SSID/password and Wi‑Fi signal strength

🤝 Collaboration
I built and tested this as a working embedded project. If you want to contribute or help harden it (HTTPS, MQTT, OTA, better sensors, or backend integration) — please open an issue or submit a PR.
