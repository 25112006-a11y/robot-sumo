const int TIME_VAO_TAM = 390;   // Phi từ vạch xuất phát vào giữa sân
const int TIME_XOAY_90 = 60;    // Xoay góc 90 độ (Căn chỉnh lại nếu xe xoay lố)
const int TIME_QUET = 100;       // Nhích đuôi xe để quét radar (Khoảng 25-30ms)
const int TIME_LUI = 300;       // Thời gian lùi về tâm sau khi kết thúc đòn húc
const int IN1 = 5;  // Bánh trái tiến
const int IN2 = 6;  // Bánh trái lùi
const int IN3 = 9;  // Bánh phải tiến
const int IN4 = 10; // Bánh phải lùi
const int TRIG_PIN = 11;
const int ECHO_PIN = 12;
int i = 0;
const int CB_DO_DUONG_SAU = 4;
const int NUT_START = 2;
const int KHOANG_CACH_PHAT_HIEN = 40; 
bool start=false;     
void setup() {
  pinMode(IN1, OUTPUT); pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT); pinMode(IN4, OUTPUT);
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);
  pinMode(CB_DO_DUONG_SAU, INPUT_PULLUP);
  pinMode(NUT_START, INPUT_PULLUP);
  Serial.begin(9600);
}

void loop() {
  long duration;
  int distance;
  if(!start ) {
    if(digitalRead(NUT_START)==HIGH) {
      delay(5000);
      start=true;
    }  
    dung_lai();
    return;
  }
  else if(digitalRead(NUT_START)==LOW) {
    start=false;
  }
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);
  duration = pulseIn(ECHO_PIN, HIGH,30000);
  distance = duration* 0.034 / 2; 
  if (i==0) {
    tien_len(); delay(TIME_VAO_TAM); 
    dung_lai(); delay(100);   
    i+=1;
  }
  int trangThaiVach_SAU = digitalRead(CB_DO_DUONG_SAU);
  Serial.print("khoang cach:");
  Serial.println(distance);
  Serial.print("cam bien sau: ");
  Serial.println(trangThaiVach_SAU);
if (trangThaiVach_SAU==0) {
    tien_len();
    delay(500);   
    quay_phai();
    delay(300);
}

else if (distance > 0 && distance <= KHOANG_CACH_PHAT_HIEN) {
    tien_len();
    delay(400);
    dung_lai();
    delay(400);
    tien_len();
    delay(400);
    dung_lai();
    delay(700);
  

 } 
else {
    //  tien_len(); delay(200); // Rướn người lên nửa đường
    //  dung_lai(); delay(40);  
     digitalWrite(TRIG_PIN, LOW);
     delayMicroseconds(2);
     digitalWrite(TRIG_PIN, HIGH);
     delayMicroseconds(10);
     digitalWrite(TRIG_PIN, LOW);
     duration = pulseIn(ECHO_PIN, HIGH,30000);
     int kc_moi = duration* 0.034 / 2;;
     if (kc_moi > 0 && kc_moi <= KHOANG_CACH_PHAT_HIEN) {
       return; // Thấy là vòng lại cắn luôn, không thụt về nữa
     }
     quay_phai(); delay(TIME_QUET); // Xoay nhích radar sang góc mới
     dung_lai(); delay(60);
   }
}


void tien_len() {
  digitalWrite(IN1, HIGH); digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW); digitalWrite(IN4, HIGH);
}

void lui_lai() {
  digitalWrite(IN1, LOW);  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, HIGH);  digitalWrite(IN4, LOW);
}

void quay_phai() {
  digitalWrite(IN1, LOW); digitalWrite(IN2, HIGH);
  digitalWrite(IN3, LOW);  digitalWrite(IN4, HIGH);
}

void dung_lai() {
  digitalWrite(IN1, LOW);  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);  digitalWrite(IN4, LOW);
}
