#include <Wire.h>
#include <Adafruit_BMP085.h>
#include <DHT.h>
#include <LiquidCrystal_I2C.h>

#define DHTPIN 2
#define DHTTYPE DHT11

#define MQ135_PIN A0
#define LDR_PIN A1

DHT dht(DHTPIN, DHTTYPE);
Adafruit_BMP085 bmp;

LiquidCrystal_I2C lcd(0x27,16,2);

void setup()
{
  Serial.begin(9600);

  dht.begin();

  lcd.init();
  lcd.backlight();

  if (!bmp.begin())
  {
    lcd.clear();
    lcd.print("BMP180 Error");
    while(1);
  }

  lcd.clear();
  lcd.print("Weather System");
  delay(2000);
}

void loop()
{
  float temperature = dht.readTemperature();
  float humidity = dht.readHumidity();

  int airQuality = analogRead(MQ135_PIN);
  int lightValue = analogRead(LDR_PIN);

  float pressure = bmp.readPressure() / 100.0;

  if (isnan(temperature) || isnan(humidity))
  {
    lcd.clear();
    lcd.print("DHT11 Error");
    delay(2000);
    return;
  }

  // Screen 1
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("T:");
  lcd.print(temperature,1);
  lcd.print("C");

  lcd.setCursor(0,1);
  lcd.print("H:");
  lcd.print(humidity,0);
  lcd.print("%");

  delay(3000);

  // Screen 2
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("P:");
  lcd.print(pressure,0);
  lcd.print("hPa");

  lcd.setCursor(0,1);
  lcd.print("Air:");
  lcd.print(airQuality);

  delay(3000);

  // Screen 3
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Light:");
  lcd.print(lightValue);

  lcd.setCursor(0,1);

  if(lightValue < 300)
    lcd.print("Dark");
  else if(lightValue < 700)
    lcd.print("Normal");
  else
    lcd.print("Bright");

  delay(3000);

  // Serial Monitor Output
  Serial.print("Temp: ");
  Serial.print(temperature);
  Serial.print(" C  Hum: ");
  Serial.print(humidity);
  Serial.print(" %  Pressure: ");
  Serial.print(pressure);
  Serial.print(" hPa  Air: ");
  Serial.print(airQuality);
  Serial.print("  Light: ");
  Serial.println(lightValue);
}
