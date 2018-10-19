# Atom


//WIRE (NECESARIO PARA UTILIZAR I2C)
#include <Wire.h>

//CONTROLADORA SERVOS I2C
#include <Adafruit_PWMServoDriver.h>

Adafruit_PWMServoDriver pwm = Adafruit_PWMServoDriver();

#define SERVOMIN  180
#define SERVOMAX  600


#define servoMin1 150
#define servoMax1 450

#define servoMin2 150
#define servoMax2 450

#define servoMin3 120 // mitad 90, 0-160
#define servoMax3 400

#define servoMin4 150 // solo de (max)0-80, mitad en 80
#define servoMax4 450

#define servoMin5 200 // Mitad en 120, 0 delante, 180 detras
#define servoMax5 350

#define servoMin6 200 //Mitad en 120, 0 detras, 180 delante
#define servoMax6 300

#define servoMin7 150 // mitad 90, 0 detras, 180 delante
#define servoMax7 300

#define servoMin8 150 // mitad 90, 0- 180(inclina hacia arriba por fuera)
#define servoMax8 300

#define servoMin9 220 // mitad 90, 180-0 (inclina hacia arriba por fuera)
#define servoMax9 350

#define servoMin10 100 //mitad 120, 0 delante, 180 detras
#define servoMax10 300

#define servoMin11 280 //mitad 90, 0 delante, 180 detras
#define servoMax11 400

#define servoMin12 250 // mitad 90, 0 detras, 180 delante
#define servoMax12 400

#define servoMin13 90 // 90-180(max)
#define servoMax13 400


#define servoMin14 120 // 0 abajo, 180 arriba
#define servoMax14 400

#define servoMin15 120
#define servoMax15 450

#define servoMin16 150
#define servoMax16 450

int v[16] = {700, 700, 700, 700, 700, 700, 700, 700, 700, 700, 700, 700, 700, 700, 700, 700};

String cmd_motor = "";         // a String to hold incoming data
String cmd_angulo = "";         // a String to hold incoming data
String cmd_movertodos = "";         // a String to hold incoming data

bool stringComplete = false;  // whether the string is complete

int anguloelegido;
int motorelegido;
int fase_serial = 0;
int servonum = 0;
int lastSend[16];// = {0, 0, 0};
int signo;
int aux;
int servoMin[16] = {servoMin1, servoMin2, servoMin3, servoMin4, servoMin5, servoMin6, servoMin7, servoMin8, servoMin9, servoMin10, servoMin11, servoMin12, servoMin13, servoMin14, servoMin15, servoMin16};

int servoMax[16] = {servoMax1, servoMax2, servoMax3, servoMax4, servoMax5, servoMax6, servoMax7, servoMax8, servoMax9, servoMax10, servoMax11, servoMax12, servoMax13, servoMax14, servoMax15, servoMax16};


int auxposFinal[16] = {0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0};
bool cambiado[16] = {false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false};
bool movertodos = false;

int posFinal[16];// = {180, 180, 180};
int lastRes[16] = {0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0};


void setup() {
  pwm.begin();
  Serial.begin(9600);
  //inputString.reserve(200);
  pwm.setPWMFreq(50);
  
  pwm.setPWM(0, 0, map(90,0,180,servoMin[0],servoMax[0])); // mueve motor a 90
  lastSend[0] = 90;                                        // indica ultimo angulo mandado  
  lastRes[0]=map(90,0,180,0,v[0]);                         // indica ultima resolucion mandada
  
  pwm.setPWM(1, 0, map(90,0,180,servoMin[1],servoMax[1]));
  lastSend[1] = 90;
  lastRes[1]=map(90,0,180,0,v[1]);
  
  pwm.setPWM(2, 0, map(90,0,180,servoMin[2],servoMax[2]));
  lastSend[2] = 90;
  lastRes[2]=map(90,0,180,0,v[2]);

  pwm.setPWM(3, 0, map(80,0,180,servoMin[3],servoMax[3]));
  lastSend[3] = 80;
  lastRes[3]=map(80,0,180,0,v[3]);

  pwm.setPWM(4, 0, map(120,0,180,servoMin[4],servoMax[4]));
  lastSend[4] = 120;
  lastRes[4]=map(120,0,180,0,v[4]);

  pwm.setPWM(5, 0, map(120,0,180,servoMin[5],servoMax[5]));
  lastSend[5] = 120;
  lastRes[5]=map(120,0,180,0,v[5]);

  pwm.setPWM(6, 0, map(90,0,180,servoMin[6],servoMax[6]));
  lastSend[6] = 90;
  lastRes[6]=map(90,0,180,0,v[6]);

  pwm.setPWM(7, 0, map(90,0,180,servoMin[7],servoMax[7]));
  lastSend[7] = 90;
  lastRes[7]=map(90,0,180,0,v[7]);

  pwm.setPWM(8, 0, map(70,0,180,servoMin[8],servoMax[8]));
  lastSend[8] = 70;
  lastRes[8]=map(70,0,180,0,v[8]);

  pwm.setPWM(9, 0, map(120,0,180,servoMin[9],servoMax[9]));
  lastSend[9] = 120;
  lastRes[9]=map(120,0,180,0,v[9]);

  pwm.setPWM(10, 0, map(90,0,180,servoMin[10],servoMax[10]));
  lastSend[10] = 90;
  lastRes[10]=map(90,0,180,0,v[10]);

  pwm.setPWM(11, 0, map(90,0,180,servoMin[11],servoMax[11]));
  lastSend[11] = 90;
  lastRes[11]=map(90,0,180,0,v[11]);

  pwm.setPWM(12, 0, map(90,0,180,servoMin[12],servoMax[12]));
  lastSend[12] = 90;
  lastRes[12]=map(90,0,180,0,v[12]);

  pwm.setPWM(13, 0, map(80,0,180,servoMin[13],servoMax[13]));
  lastSend[13] = 80;
  lastRes[13]=map(80,0,180,0,v[13]);

  pwm.setPWM(14, 0, map(90,0,180,servoMin[14],servoMax[14]));
  lastSend[14] = 90;
  lastRes[14]=map(90,0,180,0,v[14]);

  pwm.setPWM(15, 0, map(90,0,180,servoMin[15],servoMax[15]));
  lastSend[15] = 90;
  lastRes[15]=map(90,0,180,0,v[15]);
  
  for(int k=0;k<=15;k++)
  posFinal[k]=lastSend[k];
  delay(4000);
}


void loop()
{
  if (stringComplete) {
    if (fase_serial == 0)
    {
      Serial.println("recibido numero motor");
      motorelegido = cmd_motor.toInt();
      Serial.println(motorelegido);
      fase_serial = 1;
      cmd_motor = "";
    }
    else if (fase_serial == 1)
    {
      Serial.println("recibido angulo motor");
      anguloelegido = cmd_angulo.toInt();
      Serial.println(anguloelegido);
      auxposFinal[motorelegido] = anguloelegido;
      cambiado[motorelegido] = true;
      fase_serial = 2;
      cmd_angulo = "";
    }
    else if (fase_serial == 2)
    {
      Serial.println(cmd_movertodos);
      if (cmd_movertodos == "s\n")
      {
        Serial.println("wii");
        for (int j = 0; j <= 15; j++)
        {
          if (cambiado[j])
          {
            posFinal[j] = auxposFinal[j];
            cambiado[j] = false;
          }
        }

      } else
        Serial.println("siguiente motor");
      cmd_movertodos = "";
      fase_serial = 0;
    }
    // clear the string
    stringComplete = false;

  }
  if (lastSend[servonum] != posFinal[servonum])
  {

    if (lastSend[servonum] < posFinal[servonum])
    {
      signo = 1;
    }
    else if (lastSend[servonum] > posFinal[servonum])
    {
      signo = -1;
    }
    lastRes[servonum] = lastRes[servonum] + (1 * signo);
    lastSend[servonum] = map(lastRes[servonum], 0, v[servonum], 0, 180);
    aux = map(lastRes[servonum], 0, v[servonum], servoMin[servonum], servoMax[servonum]);
    pwm.setPWM(servonum, 0, aux);
    // Serial.println(aux);
  }
  if (servonum == 15)
    servonum = 0;
  else
    servonum = servonum + 1;
}
void serialEvent() {
  while (Serial.available()) {
    // get the new byte:
    char inChar = (char)Serial.read();
    // add it to the inputString:
    if (fase_serial == 0)
      cmd_motor += inChar;
    else if (fase_serial == 1)
      cmd_angulo += inChar;
    else if (fase_serial == 2)
      cmd_movertodos += inChar;
    // if the incoming character is a newline, set a flag so the main loop can
    // do something about it:
    if (inChar == '\n') {
      stringComplete = true;
    }
  }
}
