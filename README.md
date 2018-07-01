# entry-exit-detection-using-arduino
detects the entry and exit of a person using ultrasonic sensors

//time counter
unsigned long time0=0;
unsigned long time1=0;
unsigned long time2=0;

//loop count
#define repeat 10
int loops = 1;

//ultrasonic 1 & 2
#define echoPinA 2
#define trigPinA 3 
#define echoPinB 4
#define trigPinB 5
int maxD = 110;
int duA,duB,disA,disB;
int Ai=0,Ao=0,Bi=0,Bo=0;
int LAi=0,LAo=0,LBi=0,LBo=0;
int net=0;

//smoke
#define smokePin 1
int smoke;
int smokeThres = 400;

//vibration
#define vibraPin 9

//temperature
#define tempPin 0
float temp;

void setup() 
{
  // put your setup code here, to run once:
  //Serial Communication
  Serial.begin(500000);
  //pinMode(13, OUTPUT);
  Serial.println("_____________________________START_____________________________");
  
  //ultrasonic 1 & 2
  pinMode(12, OUTPUT);
  digitalWrite(12,HIGH);
  pinMode(trigPinA, OUTPUT);
  pinMode(echoPinA, INPUT);
  pinMode(trigPinB, OUTPUT);
  pinMode(echoPinB, INPUT);

  //vibration 
  pinMode(vibraPin, INPUT);
  pinMode(11, OUTPUT);
  pinMode(10, OUTPUT);
  digitalWrite(11,HIGH);
  digitalWrite(10,LOW);

  //temperature
  pinMode(tempPin, INPUT);
  pinMode(6, OUTPUT);
  pinMode(7, OUTPUT);
  digitalWrite(7,HIGH);
  digitalWrite(6,LOW);

  //smoke
  pinMode(smokePin, INPUT);
  pinMode(8, OUTPUT);
  digitalWrite(8,HIGH);

}

void loop() 
{
  // put your main code here, to run repeatedly:
  
  //ultrasonic sensors
  digitalWrite(trigPinA, LOW); 
  delayMicroseconds(2); 
  digitalWrite(trigPinA, HIGH);
  delayMicroseconds(10); 
  digitalWrite(trigPinA, LOW);
  duA=pulseIn(echoPinA, HIGH);
  disA=duA/58.2;  
  //Serial.print(disA);
  //Serial.print(" ");

  digitalWrite(trigPinB, LOW); 
  delayMicroseconds(2); 
  digitalWrite(trigPinB, HIGH);
  delayMicroseconds(10); 
  digitalWrite(trigPinB, LOW);
  duB=pulseIn(echoPinB, HIGH);
  disB=duB/58.2;

  if(disA < maxD && Ai == 0)
  {
    Ai=1;
    LAi = loops;
  }
  if(disB < maxD && Bi == 0)
  {
    Bi=1;
    LBi = loops;
  }

  if(disA>=maxD && Ao == 0 && Ai == 1)
  {
    Ao=1;
    LAo = loops;
    
  }
  if(disB>=maxD && Bo == 0 && Bi == 1)
  {
    Bo=1;
    LBo = loops;
  }

  if(Ai==1 && Ao==1 && Bi==1 && Bo==1)
  {
    if(LAi==LBi && LAo<LBo)
    {
      net++;
      Serial.println("entry ");
    }
    else if(LAi<LBi && LAo<LBo)
    {
      net++;
      Serial.println("entry ");
    }
    else if(LAi<LBi && LAo==LBo)
    {
      net++;
      Serial.println("entry ");
    }
    else if(LAi>LBi && LAo>LBo)
    {
      net--;
      Serial.println("exit ");
    }
    else if(LAi==LBi && LAo>LBo)
    {
      net--;
      Serial.println("exit ");
    }
    else if(LAi>LBi && LAo==LBo)
    {
      net--;
      Serial.println("exit ");
    }
    /*else if(LAi==LBi && LAo==LBo)
    {
      //net--;
       
      Serial.println("garbage ");
    }*/
    //else Serial.print("You Rock");
    Serial.println(net);
      Ai=0;
      Bi=0;
      Ao=0;
      Bo=0;
  }

  if(loops%repeat!=0)
  delay(120);

  if(loops%repeat==0)
  {
  //vibration 
  long measurement = pulseIn(vibraPin, HIGH);
  Serial.print("V ");
  Serial.print(measurement);
  Serial.print(" ");


  //temperature
  temp = (5.0 * analogRead(tempPin) * 100.0) / 1024;
  Serial.print("T ");
  Serial.print(temp);
  Serial.print("  ");


  //smoke
  smoke = analogRead(smokePin);
  Serial.print("S ");
  if(smoke>smokeThres)
  {
   Serial.print(smoke);
  }
  else 
  Serial.print(smoke);
  Serial.println(" ");

  Serial.flush();
  }

  loops++;

  if(loops<100000 && loops>99000 && disA>=maxD && disB>=maxD)
  {
    loops=1;
  }
/*Serial.print("Time: ");
  time1=time2;
  time2= micros();
  time0=time2-time1;
  Serial.println(time0);
*/
  
}
