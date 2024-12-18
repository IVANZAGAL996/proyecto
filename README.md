# proyecto
## CODIGO DE PROYECTO
```
//PROGRAMA PARA MONITOREAR NIVEL DE RESINA PLASTICA EN CONTENEDOR DE "ALIMENTACION" 
//MONITOREA NIVEL Y TEMPERATURA DEL "SECADOR" DE RESINA ANTES DE ENTRAR A PROCESO DE INYECCION
#include <LiquidCrystal_I2C.h> //Libreria de LCD
#define I2C1_ADDR    0x27
#define I2C2_ADDR    0x20
#define LCD1_COLUMNS 20
#define LCD1_LINES   4
#define LCD2_COLUMNS 20
#define LCD2_LINES   4
#include "DHTesp.h" //Libreria de DHT
const int Trigger1 = 4;   //Pin digital 4 para el Trigger del sensor 1
const int Echo1 = 2;   //Pin digital 2 para el Echo del sensor 1
const int Trigger2 = 17;   //Pin digital 17 para el Trigger del sensor 2
const int Echo2 = 16;   //Pin digital 16 para el Echo del sensor 2
const int DHT_PIN = 15; //pin del sensor de temperatura
DHTesp dhtSensor;
LiquidCrystal_I2C lcd1(I2C1_ADDR, LCD1_COLUMNS, LCD1_LINES);
LiquidCrystal_I2C lcd2(0X20, LCD2_COLUMNS, LCD2_LINES);

void setup() {

  Serial.begin(115200);
  dhtSensor.setup(DHT_PIN, DHTesp::DHT22);
  lcd1.init();
  lcd1.backlight();
   
  lcd2.init();
  lcd2.backlight();
  pinMode(Trigger1, OUTPUT); //pin como salida
  pinMode(Echo1, INPUT);  //pin como entrada
  pinMode(Trigger2, OUTPUT); //pin como salida
  pinMode(Echo2, INPUT);  //pin como entrada
  digitalWrite(Trigger1, LOW);//Inicializamos el pin con 0
  digitalWrite(Trigger2, LOW);//Inicializamos el pin con 0
}

void loop() {

  long t; //timepo que demora en llegar el eco
  long d1; //distancia en centimetros
  long d2; //distancia en centimetros
  
  digitalWrite(Trigger1, HIGH);
  delayMicroseconds(10);          //Enviamos un pulso de 10us
  digitalWrite(Trigger1, LOW);
  
  t = pulseIn(Echo1, HIGH); //obtenemos el ancho del pulso
  d1 = t/59;             //escalamos el tiempo a una distancia en cm

  digitalWrite(Trigger2, HIGH);
  delayMicroseconds(10);          //Enviamos un pulso de 10us
  digitalWrite(Trigger2, LOW);

  t = pulseIn(Echo2, HIGH); //obtenemos el ancho del pulso
  d2 = t/59;             //escalamos el tiempo a una distancia en cm

 
  
  TempAndHumidity  data = dhtSensor.getTempAndHumidity();
  Serial.println("Temp: " + String(data.temperature, 1) + "°C");
  Serial.println("Humidity: " + String(data.humidity, 1) + "%");
  Serial.println("---");
  //delay(2000); 
  Serial.print("NIVEL DE ALIMENTADOR: ");
  Serial.print(d1);      //Enviamos serialmente el valor de la distancia
  Serial.print("cm");
  Serial.println();
  Serial.println("---");
  Serial.print("NIVEL SECADOR: ");
  Serial.print(d2);      //Enviamos serialmente el valor de la distancia
  Serial.print("cm");
  Serial.println("---");
  delay(2000);          //Hacemos una pausa de 200ms

 lcd1.clear(); 
  lcd1.setCursor(0, 0);
  lcd1.print("  Temp: " + String(data.temperature, 1) + "\xDF"+"C  ");
  lcd1.setCursor(0, 1);
  lcd1.print(" Humidity: " + String(data.humidity, 1) + "% ");
  delay(2000);

   if (data.temperature>=-40 && data.temperature<=49 )
{
lcd1.clear();  
lcd1.setCursor(0, 0);
lcd1.print("ALARMA X BAJA  ");
lcd1.setCursor(3, 1);
lcd1.print(" TEMPERATURA ");
delay(2000);
}
else if(data.temperature>=61 && data.temperature<=80) 
{
  lcd1.clear();  
lcd1.setCursor(0, 0);
lcd1.print("ALARMA X ALTA ");
lcd1.setCursor(3, 1);
lcd1.print(" TEMPERATURA ");
delay(2000);
}

else
{
  
}

     if (d2>=0 && d2<=40)
{
lcd1.clear();  
lcd1.setCursor(0, 0);
lcd1.print("NIVEL DE SECADOR ");
lcd1.setCursor(6, 1);
lcd1.print(" 90% ");
delay(2000);
}
else if(d2>=41 && d2<=100) 
{
  lcd1.clear();  
lcd1.setCursor(0, 0);
lcd1.print("NIVEL DE SECADOR ");
lcd1.setCursor(6, 1);
lcd1.print(" 75% ");
delay(2000);
}
else if(d2>=101 && d2<=200) 
{
  lcd1.clear();  
lcd1.setCursor(0, 0);
lcd1.print("NIVEL DE SECADOR ");
lcd1.setCursor(6, 1);
lcd1.print(" 50% ");
delay(2000); 
}
else if(d2>=201 && d2<=280) 
{
 lcd1.clear();  
lcd1.setCursor(0, 0);
lcd1.print("NIVEL DE SECADOR ");
lcd1.setCursor(6, 1);
lcd1.print(" 30% ");
delay(2000); 
}
else
{
  lcd1.clear();
      lcd1.setCursor(0, 0);
      lcd1.print("ALARMA POR NIVEL ");
      lcd1.setCursor(0, 1);
      lcd1.print("BAJO SECADOR");
      delay(2000);
}

    if (d1>=0 && d1<=390)
    {
      lcd2.clear();
      lcd2.setCursor(0, 0);
      lcd2.print("NIVEL ALIMENTADOR");
      lcd2.setCursor(5, 1);
      lcd2.print("ESTABLE");
      delay(2000);
    }
    else 
    {
      lcd2.clear();
      lcd2.setCursor(0, 0);
      lcd2.print("ALARMA POR NIVEL ");
      lcd2.setCursor(0, 1);
      lcd2.print("BAJO ALIMENTADOR");
      delay(2000);
    }
  

}
 
 ```
