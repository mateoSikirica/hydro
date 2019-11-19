int ENABLE1 = 4;
int FWD1 = 10;
int REV1 = 11;

int CurrentPosition = 0;
const int sensorPin = 0;

int Speed = 1000;
bool value = false;
bool var = false;

volatile int rx_value = 0;
volatile int prev_time = 0;

void change(void)
{
  if (digitalRead(2)){
    prev_time = micros();
  }
  else {
    rx_value = micros() - prev_time;
  }
}

void setup() 
{
  Serial.begin(9600);
  
  pinMode(ENABLE1, OUTPUT);
  pinMode(FWD1, OUTPUT);
  pinMode(REV1, OUTPUT);
  
  pinMode(2, INPUT_PULLUP);
  
  attachInterrupt(digitalPinToInterrupt(2), change, CHANGE);
}

void loop() 
{
  int pwm_value = map(rx_value, 1000, 2000, 0, 100);
  pwm_value = constrain(pwm_value, 0, 100);
  //Serial.println(pwm_value);

  CurrentPosition = analogRead(sensorPin); 
  Serial.print("Current = " );                       
  Serial.print(CurrentPosition); 
  Serial.print("\n" );    

  if (pwm_value > 80)
  {
    Forward();
  }
  else if (pwm_value < 20)
  {
    Reverse();
  }
  else
  {
    if (CurrentPosition < 390)
    {
      Reverse ();
    }
    else if(CurrentPosition > 610)
    {
      Forward();
    }
    else
    {
      Stop();
      delay(500);
    }
  }
}

void Forward()
{
  digitalWrite(ENABLE1, HIGH);
  analogWrite(REV1, 0);
  analogWrite(FWD1, Speed);
}

void Reverse()
{
  digitalWrite(ENABLE1, HIGH);
  analogWrite(FWD1, 0);
  analogWrite(REV1, Speed);
}

void Stop()
{
  digitalWrite(ENABLE1, LOW);
  analogWrite(FWD1, 0);
  analogWrite(REV1, 0);
}
