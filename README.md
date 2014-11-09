Pastillero
==========

Copia código

///////////////////////Librerias//////////////////////////////

#include <Wire.h>  
#define DS1307_I2C_ADDRESS 0x68 
#include <Stepper.h>

/*************************************************************
*********************** Definiciones *************************
*************************************************************/
int Ldr = 0;
int Led = 37;
int Alarma = 8;
int ValorLDR = 0;
const int stepsPerRevolution = 295;  // Revoluciones
Stepper Motor1(stepsPerRevolution, 47, 49, 51, 53);//Inicializa los pines del motor
Stepper Motor2(stepsPerRevolution, 39, 41, 43, 45);
Stepper Motor3(stepsPerRevolution, 46, 48, 50, 52);
Stepper Motor4(stepsPerRevolution, 38, 40, 42, 44);

/***************************************************************
********************* Reloj ************************************
***************************************************************/

byte decToBcd(byte val)
{
  return ( (val/10*16) + (val%10) );
}
byte bcdToDec(byte val)
{
  return ( (val/16*10) + (val%16) );
}
void setDateDs1307(byte second,        // 0-59
                   byte minute,        // 0-59
                   byte hour,          // 1-23
                   byte dayOfWeek,     // 1-7
                   byte dayOfMonth,    // 1-28/29/30/31
                   byte month,         // 1-12
                   byte year)          // 0-99
{
    Wire.beginTransmission(DS1307_I2C_ADDRESS);
   Wire.write(0);
   Wire.write(decToBcd(second));    
   Wire.write(decToBcd(minute));
   Wire.write(decToBcd(hour));      
   Wire.write(decToBcd(dayOfWeek));
   Wire.write(decToBcd(dayOfMonth));
   Wire.write(decToBcd(month));
   Wire.write(decToBcd(year));
   Wire.endTransmission();
}

// Establece la fecha y la hora del reloj
void getDateDs1307(byte *second,
          byte *minute,
          byte *hour,
          byte *dayOfWeek,
          byte *dayOfMonth,
          byte *month,
          byte *year)
{
  // Resetea el registro puntero
  Wire.beginTransmission(DS1307_I2C_ADDRESS);
  Wire.write(0);
  Wire.endTransmission();

  Wire.requestFrom(DS1307_I2C_ADDRESS, 7);

  // Alguno de estos necesitan enmascarar porque ciertos bits son bits de control    

 *second     = bcdToDec(Wire.read () & 0x7f);
  *minute     = bcdToDec(Wire.read ());
  *hour       = bcdToDec(Wire.read () & 0x3f);  
  *dayOfWeek  = bcdToDec(Wire.read ());
  *dayOfMonth = bcdToDec(Wire.read ());
  *month      = bcdToDec(Wire.read ());
  *year       = bcdToDec(Wire.read ());
}




  
/*********************************************************************
**************************** Comienza el Setup ***********************
*********************************************************************/

void setup() {
  pinMode (Ldr, INPUT);
  pinMode (Led, OUTPUT);
  pinMode (Alarma, OUTPUT);
  //delay(100);
 // lcd.init();                  // Inicializacion LCD
  //lcd.backlight();             // Iluminacion LCD
  Serial.begin(9600);          // Puerto serie configurado a 9600 baudios.
  byte second, minute, hour, dayOfWeek, dayOfMonth, month, year;
  Wire.begin();
  // Se pone en fecha y hora, se manda al arduino, se comenta hasta la linea
  // setDateDs1307 y se vuelva a mandar
 /*second = 15;
  minute = 13;
  hour = 9;
  dayOfWeek = 6;
  dayOfMonth = 1;
  month = 11;
  year = 14;
  
  setDateDs1307(second, minute, hour, dayOfWeek, dayOfMonth, month, year);*/


  // set the speed at 60 rpm:
  Motor1.setSpeed(10);
  Motor2.setSpeed(10);
  Motor3.setSpeed(10);
  Motor4.setSpeed(10);
  // initialize the serial port:
  Serial.begin(9600);
}


/*******************************************************************
******************* Comienza el Loop *******************************
*******************************************************************/

void loop() {
  
  
  ValorLDR =analogRead(0);
  
  byte second, minute, hour, dayOfWeek, dayOfMonth, month, year;
  getDateDs1307(&second, &minute, &hour, &dayOfWeek, &dayOfMonth, &month, &year);  
  
  
  //lcd.setCursor(6,0);
    Serial.print("Hoy es ");switch (dayOfWeek)
    {
    case 1:
      //Serial.print("Dia de la semana: ");
      //lcd.setCursor(9,0);
      Serial.print("Lunes "); 
      break;
    case 2:
    //Serial.print("Dia de la semana: ");
      //lcd.setCursor(9,0);
      Serial.print("Martes "); 
      break;
    case 3:
    //Serial.print("Dia de la semana: ");
     // lcd.setCursor(9,0);
      Serial.print("Miercoles "); 
      break;
    case 4:
   // Serial.print("Dia de la semana: ");
      //lcd.setCursor(9,0);
      Serial.print("Jueves "); 
      break;
    case 5:
   // Serial.print("Dia de la semana: ");
      //lcd.setCursor(9,0);
      Serial.print("Viernes "); 
      break;
    case 6:
    //Serial.print("Dia de la semana: ");
      //lcd.setCursor(9,0);
      Serial.print("Sabado "); 
      break;
    case 7:
    //Serial.print("Dia de la semana: ");
      //lcd.setCursor(9,0);
      Serial.print("Domingo "); 
      break;
   }
   
  if (dayOfMonth < 10) Serial.print("0");
  Serial.print(dayOfMonth, DEC); 
  //lcd.setCursor(2,0);
  Serial.print(" de ");
  //lcd.setCursor(3,0);
    //if (month < 10) Serial.print("0");
  //Serial.print(month); 
  switch (month)
    {
     
    case 1:
      //Serial.print("Mes: ");
      //lcd.setCursor(9,0);
      Serial.print("Enero"); 
      break;
    case 2:
    //Serial.print("Mes: ");
      //lcd.setCursor(9,0);
      Serial.print("Febrero"); 
      break;
    case 3:
    //Serial.print("Mes: ");
     // lcd.setCursor(9,0);
      Serial.print("Marzo"); 
      break;
    case 4:
    //Serial.print("Dia de la semana: ");
      //lcd.setCursor(9,0);
      Serial.print("Abril"); 
      break;
    case 5:
    //Serial.print("Dia de la semana: ");
      //lcd.setCursor(9,0);
      Serial.print("Mayo"); 
      break;
    case 6:
    //Serial.print("Dia de la semana: ");
      //lcd.setCursor(9,0);
      Serial.print("Junio"); 
      break;
    case 7:
   // Serial.print("Dia de la semana: ");
      //lcd.setCursor(9,0);
      Serial.print("Julio"); 
      break;
    case 8:
      Serial.print("Agosto");
      break;
       case 9:
    //Serial.print("Dia de la semana: ");
      //lcd.setCursor(9,0);
      Serial.print("Septiembre"); 
      break;
    case 10:
   // Serial.print("Dia de la semana: ");
      //lcd.setCursor(9,0);
      Serial.print("Octubre"); 
      break;
    case 11:
      Serial.print("Noviembre");
      break;
     case 12:
      Serial.print("Diciembre:");
     break;
   } 
  //lcd.setCursor(5,0);
  Serial.print(" del 20");   
  //lcd.setCursor(0,0);
    Serial.print(year, DEC);
  Serial.println("");
  //lcd.setCursor(0,1);
  Serial.print("Son las ");
    if (hour < 10) Serial.print("0");
  Serial.print(hour, DEC);
  //lcd.setCursor(2,1);
  Serial.print(" Horas ");  
  //lcd.setCursor(3,1);
    if (minute < 10) Serial.print("0");
  Serial.print(minute, DEC);
  //lcd.setCursor(5,1);
  Serial.print(" minutos ");
    if (second < 10) Serial.print("0");
  Serial.print(second, DEC);  
  Serial.println(" segundos");
  Serial.println();
  Serial.println(ValorLDR);
  delay(500);
   
  //Configuracion Motor1
 const int VALOR_UMBRAL_LDR = 690;
 int HoraM1 = (dayOfWeek == 7  && hour == 10 && minute == 22 && second == 00);
 int HoraM15 = (dayOfWeek == 7  && hour == 10 && minute == 23 && second == 00);
 int pita = 0; // se usa para que sólo entre una vez en el primer if hasta que entre en el segundo
 if(HoraM1 && (pita==0)){
    Motor1.step(-stepsPerRevolution);
    pita = 1;
    digitalWrite(Led, HIGH);
    digitalWrite(Alarma, HIGH);
    delay(100);// una pausa para que la luz del LED ilumine bien la celula
}
 if(HoraM15 || (ValorLDR > VALOR_UMBRAL_LDR)){
    digitalWrite(Led, LOW);
    digitalWrite(Alarma, LOW);
    pita = 0;
}
  
  
 //Configuracion Motor2
 //const int VALOR_UMBRAL_LDR = 690;
 int HoraM2 = (dayOfWeek == 7  && hour == 10 && minute == 25 && second == 00);
 int HoraM25 = (dayOfWeek == 7  && hour == 10 && minute == 26 && second == 00);
 //int pita = 0; // se usa para que sólo entre una vez en el primer if hasta que entre en el segundo
 if(HoraM2 && (pita==0)){
    Motor2.step(-stepsPerRevolution);
    pita = 1;
    digitalWrite(Led, HIGH);
    digitalWrite(Alarma, HIGH);
    delay(100);// una pausa para que la luz del LED ilumine bien la celula
}
 if(HoraM25 || (ValorLDR > VALOR_UMBRAL_LDR)){
    digitalWrite(Led, LOW);
    digitalWrite(Alarma, LOW);
    pita = 0;
}
 
 //Configuracion Motor3
 // const int VALOR_UMBRAL_LDR = 690;
 int HoraM3 = (dayOfWeek == 7  && hour == 10 && minute == 27 && second == 00);
 int HoraM35 = (dayOfWeek == 7  && hour == 10 && minute == 28 && second == 00);
 //int pita = 0; // se usa para que sólo entre una vez en el primer if hasta que entre en el segundo
 if(HoraM3 && (pita==0)){
    Motor3.step(-stepsPerRevolution);
    pita = 1;
    digitalWrite(Led, HIGH);
    digitalWrite(Alarma, HIGH);
    delay(100);// una pausa para que la luz del LED ilumine bien la celula
}
 if(HoraM35 || (ValorLDR > VALOR_UMBRAL_LDR)){
    digitalWrite(Led, LOW);
    digitalWrite(Alarma, LOW);
    pita = 0;
}
 
 //Configuracion Motor4
 // const int VALOR_UMBRAL_LDR = 690;
 int HoraM4 = (dayOfWeek == 7  && hour == 10 && minute == 31 && second == 00);
 int HoraM45 = (dayOfWeek == 7  && hour == 10 && minute == 32 && second == 00);
 //int pita = 0; // se usa para que sólo entre una vez en el primer if hasta que entre en el segundo
 if(HoraM4 && (pita==0)){
    Motor4.step(-stepsPerRevolution);
    pita = 1;
    digitalWrite(Led, HIGH);
    digitalWrite(Alarma, HIGH);
    delay(100);// una pausa para que la luz del LED ilumine bien la celula
}
 if(HoraM45 || (ValorLDR > VALOR_UMBRAL_LDR)){
    digitalWrite(Led, LOW);
    digitalWrite(Alarma, LOW);
    pita = 0;
}

}
  
 
  

  

 
 
  
  



