# aduino.bluetooth.alarm.application
#include <SoftwareSerial.h>

int bluetoothTx = 2; //블루투스 포트 설정
int bluetoothRx = 3; //블루투스 포트 설정
int buttonPin = 7; //버튼 포트 설정
int LED = 11; // LED 포트 설정
char cmd; // 앱에서 보내는 문자
int count = 0; //경과 시간 체크
char st;
char bs; //눌리면 1로 변경, 안눌렸으면 0상태
SoftwareSerial bluetooth(bluetoothTx, bluetoothRx); // 블루투스 설정

void setup() {
  
Serial.begin(9600); 
delay(100);
bluetooth.begin(9600); 
pinMode(LED, OUTPUT); // LED 설정
pinMode(buttonPin, OUTPUT); // 버튼 설정
}

void loop() {
  // put your main code here, to run repeatedly:
  int buttonState = digitalRead(buttonPin);
  
if(bluetooth.available())
{
  cmd = (char)bluetooth.read(); //앱에서 보낸 문자를 cmd에 저장 (앱에서 알람이 울리면 블루투스롤 통해 cmd에게 1전달)
  bs='0';
  count = 0;
}

if(cmd=='1') // cmd 값이 1일때 
{
  
  digitalWrite(LED,HIGH);  // led를 킨다
  Serial.println("LED ON"); // 시리얼 모니터에 led on 표시
  count=count+1; //count값을 증가시킴
  Serial.println(count); // count 값을 시리얼 모니터에 표시

  if(bs == '0')
  {
    if(buttonState == HIGH) // 버튼이 눌렸을 때 
    {
      Serial.println("button push");
      bs ='1';
    }
      
      
    if(count > 50&&count <55) // 알람시간 이후 50초가 경과할때까지 약을 먹지 않으면 보호자에게 메세지가 보내진다
    {
      
      bluetooth.write('n'); // 앱으로 st값을 보낸다.
      Serial.println("message send"); //시리얼 모니터에 message send 표시
      cmd='0';
    }
  }
  
  else if(bs =='1') //버튼 눌려서 bs = '1'로 바꼈을 때
  {
      
      bluetooth.write('y');
      cmd = '0'; // cmd를 0으로 설정
  } 
}
if(cmd == '0'){ // cmd가 0일때


digitalWrite(LED, LOW); // led를 끄고
Serial.println("LED OFF"); // 시리얼 모니터에 led off표시
count = 0; // count 값을 0으로 설정
}
delay(1000); //1초 delay 즉, 1초마다(정확하게 1초는 아님) count 값이 올라가게 해뒀음
}
