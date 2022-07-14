Servo myservo1;  // 门舵机定义
Servo myservo2;  // 消毒器舵机定义
Servo myservo3;  // 门锁定义
int pos1 = 0;    //门舵机角度初始化值
int pos2 = 0;   //消毒器舵机角度初始化值
int pos3 = 0;    //门锁舵机角度初始化值
int kg = 1;      //红外按键扫描初始化
void jinglai(void)            //进门舵机开
{

  for (pos1 = 90; pos1 >= 0; pos1 --)  // 从180°到0°
  {
    myservo1.write(pos1);              // 舵机角度写入
    delay(5);                       // 等待转动到指定角度
  }
  delay(1000);
  for (pos1 = 0; pos1 <= 92; pos1 ++)  // 0°到180°
  {
    myservo1.write(pos1);              // 舵机角度写入
    delay(5);                       // 等待转动到指定角度
  }
  delay(500);
  digitalWrite(0, LOW);
  delay(50);
  
  Guan();             //舵机锁门
  //delay(50);
  digitalWrite(15, HIGH); //绿灯灭
  digitalWrite(13, LOW); //红灯亮，有人
  kg = 0;
}

void chuqu(void)      //出去舵机门开
{
  Kai();        //门锁舵机开锁
  delay(500);
  for (pos1 = 90; pos1 <= 180; pos1 ++)  // 0°到180°
  {
    myservo1.write(pos1);              // 舵机角度写入
    delay(5);                       // 等待转动到指定角度
  }
  delay(1000);
  for (pos1 = 180; pos1 >= 90; pos1 --)  // 从180°到0°
  {
    myservo1.write(pos1);              // 舵机角度写入
    delay(5);                       // 等待转动到指定角度
  }
  delay(500);
  digitalWrite(0, LOW);
  delay(50);
  
  xiaodu();
  digitalWrite(13, HIGH);    //红灯灭
  digitalWrite(15, LOW);     //绿灯亮，无人


  kg = 1;
}

void Kai(void)        //门锁舵机开关打开
{

  //for (pos3 = 90; pos3 <= 0; pos3 ++)  // 0°到180°
  //{
    myservo3.write(0);              // 舵机角度写入
   // delay(5);                       // 等待转动到指定角度
  //}
  delay(1000);
  digitalWrite(14, LOW);
  //  for (pos3 = 90; pos3 >= 0; pos3 --)  // 从180°到0°
  //  {
  //    myservo.write(pos3);              // 舵机角度写入
  //    delay(5);                       // 等待转动到指定角度
  //  }
  //  digitalWrite(0, LOW);
  // // kg = 0;
}

void Guan(void)     //门锁舵机开关关闭
{
  //  for (pos1 = 180; pos1 >= 0; pos1 --)  // 从180°到0°
  //
  //  {
  //    myservo.write(pos1);              // 舵机角度写入
  //    delay(5);                       // 等待转动到指定角度
  //  }
  //  delay(1000);

 // for (pos3 = 90; pos3 >= 0; pos3 --)  // 0°到180°
  //{
    myservo3.write(80);              // 舵机角度写入
  //  delay(5);                       // 等待转动到指定角度
  //}
  delay(1000);
  digitalWrite(14, LOW);
  //kg = 1;
}

void xiaodu(void)       //消毒器舵机
{
  digitalWrite(2, LOW);   //消毒黄灯亮
  for (pos2 = 0; pos2 <= 90; pos2 ++)  // 从180°到0°
  {
    myservo2.write(pos2);              // 舵机角度写入
    delay(5);                       // 等待转动到指定角度
  }
  digitalWrite(16, HIGH);          //消毒继电器开
  delay(5000);                    //消毒时间大概5秒
  digitalWrite(16, LOW);         //消毒继电器关
  for (pos2 = 90; pos2 >= 0; pos2 --)  // 0°到180°
  {
    myservo2.write(pos2);              // 舵机角度写入
    delay(5);                       // 等待转动到指定角度
  }
  digitalWrite(4, LOW);
  digitalWrite(2, HIGH);  //黄灯灭
}

void Hwkg(void)        //红外按键扫描
{
  if (kg == 1)
  {
    if (digitalRead(5) == 0) {
      //digitalWrite(15, LOW);
      jinglai();
    }
    if (digitalRead(12) == 0)
    {
      //digitalWrite(15, LOW);
      chuqu();
    }
  }
  if (kg == 0)
  {
    if (digitalRead(12) == 0)
    {
      //digitalWrite(15, LOW);  //红灯亮
      chuqu();                //出门
    }
  }
}

void setup()
{
  // 初始化串口
  Serial.begin(115200);
  Serial.println();
  myservo1.attach(0);  // 门控制线连接数字
  myservo1.write(90);
  myservo2.attach(4);  // 消毒器控制线连接数字
  myservo2.write(0);
  myservo3.attach(14); // 门锁控制线连接数字
  myservo3.write(0);
  delay(500);
  digitalWrite(0, LOW);
  digitalWrite(4, LOW);
  digitalWrite(14, LOW);
  pinMode(5, INPUT);     //门外红外扫描开关
  pinMode(12, INPUT);    //门内红外扫描开关
  pinMode(15, OUTPUT);   //绿灯输出
  pinMode(13, OUTPUT);   //红灯输出
  pinMode(2, OUTPUT);    //黄灯输出
  pinMode(16, OUTPUT);   //消毒器继电器输出

  digitalWrite(15, LOW);  //初始化绿灯亮
  digitalWrite(13, HIGH); //初始化红灯灭
  digitalWrite(2, HIGH);  //初始化黄灯灭
  digitalWrite(16, LOW); //初始化继电器关
}

void loop()
{
  Hwkg();
}
