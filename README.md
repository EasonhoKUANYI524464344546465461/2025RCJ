---
title: RCJ 2025 (Line Tracker)

---

# 2025RCJ

[![hackmd-github-sync-badge](https://hackmd.io/HM27fnFMQ6mjRsJzNxseoQ/badge)](https://hackmd.io/HM27fnFMQ6mjRsJzNxseoQ)
# 這次比賽的日誌

## 第一週

這週我們的實作分為兩個部分進行：一組負責測試循跡模組，另一組則是測試馬達驅動模組。我負責的是馬達驅動模組的測試。我們使用 Arduino Uno 作為主控板，並搭配 AT8236 馬達驅動模組 來進行控制。
與常見的 L298N 驅動模組不同，AT8236 在控制方式上有些特別之處，例如它不需要透過雙路PWM控制，而是使用特定的PWM與方向控制腳位的組合來實現馬達的前進、後退與停止。
### 相片
![488467313_683074717411632_2164872895936659201_n](https://hackmd.io/_uploads/HJn225_Zle.jpg)
![490982919_694584216866840_6708368198806711531_n](https://hackmd.io/_uploads/rkhhn5_-xg.jpg)
![491005613_671290705850137_5178873068696880534_n](https://hackmd.io/_uploads/rkhn3cd-xg.jpg)


---


## 第二週

由於上週我們已經完成了馬達驅動模組與循跡模組的基本功能測試與接線確認，本週的目標轉為車體設計以及將兩個模組程式進行整合。
在第一版車體設計完成後，我們發現馬達支架無法安裝，原因是雷射切割時孔位開錯，導致無法對齊。
針對這個問題，我們在第二版車體中修正了孔位，成功安裝了馬達支架。但新的問題也出現了，由於輔助輪的高度設計不當，車體甲板上空間不足，導致電池無法放置。
下週我們計畫針對車體空間配置進行詳細計算與調整，特別是電池擺放位置、輔助輪高度以及模組佈線路徑，確保整體結構穩定並具備實際運作的可能性。


### 相片
![491006525_1006747944436943_6255593520882834350_n](https://hackmd.io/_uploads/ryp0ncdWge.jpg)
![491015706_1173141644108282_7128794950235943283_n](https://hackmd.io/_uploads/BJCCncO-gg.jpg)
![491093590_9401990959891188_3811507405140479177_n](https://hackmd.io/_uploads/Bk0RhcOZxg.jpg)



---

## 第三週

這週我們嘗試製作了多種不同版本的車體設計，雖然每個版本都有進步，但也伴隨著一些新的小問題出現。第一版在設計時忽略了排線的路徑，尋跡模組的排線沒有預留從車體下方往上拉的孔洞，導致實際安裝時排線會卡住或者無法美觀地佈線。這讓我們意識到，在模組配置完成後，必須要多花心思在線材走向的規劃，否則會影響整體穩定度與維修性。

在後續的版本中，我們試圖修正這個問題，但又遇到了新的挑戰。某一版的設計雖然加入了排線孔洞，不過卻讓輔助輪的位置受到干涉，導致輔助輪無法順利轉動或安裝，進而影響整體行駛的穩定性與流暢度。這讓我們開始重新檢討底盤的整體空間配置，並考慮是否要在設計時就預留出模組化的安裝位置與走線通道，以提高後續調整與擴充的彈性。

雖然每一版都有些小問題，但這些錯誤也幫助我們一步步更了解整體車體結構與實作時會遇到的細節。我們會繼續調整，希望下個版本能在功能與結構上都更完善。
### 程式
    #include <Wire.h>
    float old_error ;
    float i;
    float d;
    int L1, L2, L3, L4, L5, L6, L7, L8;
    void setup() {
      pinMode(10, OUTPUT);
      pinMode(11, OUTPUT);
      pinMode(5, OUTPUT);
      pinMode(6, OUTPUT);
      for (int i = 2; i <= 9; i++)
      {
        pinMode(i, INPUT);
      }
      Wire.begin();
    }

    void loop() {
      byte data = 0; // 用于存储读取的数据

      Wire.beginTransmission(0x12); // I2C设备的地址
      Wire.write(0x30); // 寄存器地址
      Wire.endTransmission();

      delay(10); // 给设备足够的时间来处理请求

      Wire.requestFrom(0x12, 1); // 请求1个字节的数据
      while (Wire.available()) {
        data = Wire.read(); // 读取数据
      }

      L1 = (data >> 7) & 0x01;
      L2 = (data >> 6) & 0x01;
      L3 = (data >> 5) & 0x01;
      L4 = (data >> 4) & 0x01;
      L5 = (data >> 3) & 0x01;
      L6 = (data >> 2) & 0x01;
      L7 = (data >> 1) & 0x01;
      L8 = (data >> 0) & 0x01;


      /* Serial.print(L1);
        Serial.print(L2);
        Serial.print(L3);
        Serial.print(L4);
        Serial.print(L5);
        Serial.print(L6);
        Serial.print(L7);
        Serial.println(L8);*/
      float error;

      /* if (L3 == 0 && L4 == 0 && L5 == 0 && L6 == 0)
        {
         digitalWrite(12, HIGH); //右
         analogWrite(10, 230);
         digitalWrite(13, HIGH); //左
         analogWrite(11, 230);
         digitalWrite(12, HIGH); //右
         analogWrite(10, 230);
         analogWrite(11, 230);
         digitalWrite(13, HIGH); //左

        }*/
      if (L1 == 0)
      {
        error = 3;//太右
      }
      else if (L2 == 0)//白色1,黑色0
      {
        error = 2;//太左
      }
      else if (L3 == 0)
      {
        error = 1;
      }
      else if (L4 == 0)
      {
        error = 0;//太右
      }
      else if (L5 == 0)//白色1,黑色0
      {
        error = 0;//太左
      }
      else if (L6 == 0)
      {
        error = -1;
      }
      else if (L7 == 0)
      {
        error = -2;
      }
      else if (L8 == 0)
      {
        error = -3;
      }

      i = constrain(i + error, -100, 100);
      d = error - old_error;

      float correct = 23 * error + 16 * d ;

      int rightspeed = 225 - correct;
      int leftspeed = 225 + correct;
      if (rightspeed > 255)
      {
        rightspeed = 510 - rightspeed;
        digitalWrite(10, HIGH); //右
        analogWrite(5, rightspeed);
      }
      else
      {
        digitalWrite(5, HIGH); //右
        analogWrite(10, rightspeed);
      }
      if (leftspeed > 255)
      {
        leftspeed = 510 - leftspeed;
        digitalWrite(11, HIGH); //右
        analogWrite(6, leftspeed);
      }
      else
      {
        digitalWrite(6, HIGH); //右
        analogWrite(11, leftspeed);
      }
      old_error = error;
      /*int a = 255 - leftspeed;
        int b = rightspeed - 255;
        Serial.print(a);
        Serial.print(",");
        Serial.println(b);*/
    }
### 相片
![491650180_642656871940314_8065051041912791271_n](https://hackmd.io/_uploads/SJTlTcdWlx.jpg)
![494325173_630496253313665_7481080289022598827_n](https://hackmd.io/_uploads/rypg65uWgx.jpg)
![494327800_2149001498856918_7983571083329864367_n](https://hackmd.io/_uploads/SJalTc_beg.jpg)
![494329210_8734291850007173_8886672350318653281_n](https://hackmd.io/_uploads/rkTxpqu-gx.jpg)


---


## 第四週

這週我們開始撰寫程式，使用8路紅外線感測器搭配權重和PID控制來進行路徑追蹤。測試時發現，原本的PID寫法在感測器與輪子距離太遠時會產生延遲，導致反應不靈敏。因此，我們將感測器移至車體中間、更加靠近輪子的位置，經測試後明顯改善了反應速度。

不過，在面對轉角很小的彎道時，我們發現車輛的迴轉半徑過大，加上輪胎容易打滑，常常導致迷航。為了解決這個問題，我們更換了抓地力更強的輪胎，並調整了程式邏輯。原本程式只能讓一個馬達轉動、另一個靜止，修改後改為可同時讓兩個輪子進行一前一後的轉動，大幅縮小了迴轉半徑，效果明顯改善。

目前PID參數仍需進一步調整，還有一些細節尚未完成，預計接下來會持續優化。






---

# **最終版程式**



    #include <Wire.h>
    float old_error ;
    float i;
    float d;
    float error;
    int L1, L2, L3, L4, L5, L6, L7, L8, rotate;
    void Sensor()
    {
      byte data = 0; // 用於存儲讀取的數據變數

      Wire.beginTransmission(0x12); // I2C設備的地址
      Wire.write(0x30); // 寄存器地址
      Wire.endTransmission();

      delay(10); // 給設備足夠的時間來處理請求
      Wire.requestFrom(0x12, 1); // 請求1個字節的數據
      while (Wire.available()) {
        data = Wire.read(); // 讀取數據
      }
      L1 = (data >> 7) & 0x01;
      L2 = (data >> 6) & 0x01;
      L3 = (data >> 5) & 0x01;
      L4 = (data >> 4) & 0x01;
      L5 = (data >> 3) & 0x01;
      L6 = (data >> 2) & 0x01;
      L7 = (data >> 1) & 0x01;
      L8 = (data >> 0) & 0x01;
    }
    void setup() {
      pinMode(10, OUTPUT);
      pinMode(11, OUTPUT);
      pinMode(5, OUTPUT);
      pinMode(6, OUTPUT);
      for (int i = 2; i <= 9; i++)
      {
        pinMode(i, INPUT);
      }
      Wire.begin();
      //Serial.begin(9600);
    }
    void loop() {
      rotate = 0;
      Sensor();
      if (L1 + L2 + L3 + L4 + L5 + L6 + L7 + L8 < 5)
      {
        digitalWrite(5, HIGH); //右
        digitalWrite(10, HIGH);
        digitalWrite(6, HIGH); //左
        digitalWrite(11, HIGH);
        delay(500);
        digitalWrite(5, HIGH); //直走
        analogWrite(10, 230);
        digitalWrite(6, HIGH);
        analogWrite(11, 230);
        delay(300);
        digitalWrite(5, HIGH);
        digitalWrite(10, HIGH);
        digitalWrite(6, HIGH);
        digitalWrite(11, HIGH);
        Sensor();
        if (L1 + L2 + L3 + L4 + L5 + L6 + L7 + L8 > 6) {
          Sensor();
          while (L1 + L2 + L3 + L4 + L5 + L6 + L7 + L8 > 7) {
            if (rotate > 45) {
              digitalWrite(5, HIGH); //左轉
              analogWrite(10, 220);
              digitalWrite(11, HIGH);
              analogWrite(6, 220);
              Sensor(); 
            }
            else {
              digitalWrite(10, HIGH); //右轉
              analogWrite(5, 220);
              digitalWrite(6, HIGH);
              analogWrite(11, 220);
              Sensor();
              rotate++;
            }
          }
        }
      }
    else if (L1 == 0)
    {
      error = 1.5;//太右
    }
    else if (L2 == 0)//白色1,黑色0
    {
      error = 1;//太左
    }
    else if (L3 == 0)
    {
      error = 0.5;
    }
    else if (L4 == 0)
    {
      error = 0;//太右
    }
    else if (L5 == 0)//白色1,黑色0
    {
      error = 0;//太左
    }
    else if (L6 == 0)
    {
      error = -0.5;
    }
    else if (L7 == 0)
    {
      error = -1;
    }
    else if (L8 == 0)
    {
      error = -1.5;
    }
    i = constrain(i + error, -100, 100);
    d = error - old_error;

    float correct = 20 * error + i * 0.01 + 25 * d ;

    int rightspeed = 225 - correct;
    int leftspeed = 225 + correct;
    if (rightspeed > 255)
    {
      rightspeed = 520 - rightspeed;
      digitalWrite(10, HIGH); //右
      analogWrite(5, rightspeed);
    }
    else
    {
      digitalWrite(5, HIGH); //右
      analogWrite(10, rightspeed);
    }
    if (leftspeed > 255)
    {
      leftspeed = 520 - leftspeed;
      digitalWrite(11, HIGH); //右
      analogWrite(6, leftspeed);
    }
    else
    {
      digitalWrite(6, HIGH); //右
      analogWrite(11, leftspeed);
    }
    old_error = error;
    }


# 比賽
![494820890_1725340601704732_2901879589007977701_n](https://hackmd.io/_uploads/rk7BksO-eg.jpg)
![494828003_1700219037254490_4098254403474269384_n](https://hackmd.io/_uploads/rkXHksOblg.jpg)

# 心得省思
這次比賽從買馬達到# 2025RCJ

[![hackmd-github-sync-badge](https://hackmd.io/HM27fnFMQ6mjRsJzNxseoQ/badge)](https://hackmd.io/HM27fnFMQ6mjRsJzNxseoQ)
# 這次比賽的日誌

## 第一週

這週我們的實作分為兩個部分進行：一組負責測試循跡模組，另一組則是測試馬達驅動模組。我負責的是馬達驅動模組的測試。我們使用 Arduino Uno 作為主控板，並搭配 AT8236 馬達驅動模組 來進行控制。
與常見的 L298N 驅動模組不同，AT8236 在控制方式上有些特別之處，例如它不需要透過雙路PWM控制，而是使用特定的PWM與方向控制腳位的組合來實現馬達的前進、後退與停止。
### 相片
![488467313_683074717411632_2164872895936659201_n](https://hackmd.io/_uploads/HJn225_Zle.jpg)
![490982919_694584216866840_6708368198806711531_n](https://hackmd.io/_uploads/rkhhn5_-xg.jpg)
![491005613_671290705850137_5178873068696880534_n](https://hackmd.io/_uploads/rkhn3cd-xg.jpg)


---


## 第二週

由於上週我們已經完成了馬達驅動模組與循跡模組的基本功能測試與接線確認，本週的目標轉為車體設計以及將兩個模組程式進行整合。
在第一版車體設計完成後，我們發現馬達支架無法安裝，原因是雷射切割時孔位開錯，導致無法對齊。
針對這個問題，我們在第二版車體中修正了孔位，成功安裝了馬達支架。但新的問題也出現了，由於輔助輪的高度設計不當，車體甲板上空間不足，導致電池無法放置。
下週我們計畫針對車體空間配置進行詳細計算與調整，特別是電池擺放位置、輔助輪高度以及模組佈線路徑，確保整體結構穩定並具備實際運作的可能性。


### 相片
![491006525_1006747944436943_6255593520882834350_n](https://hackmd.io/_uploads/ryp0ncdWge.jpg)
![491015706_1173141644108282_7128794950235943283_n](https://hackmd.io/_uploads/BJCCncO-gg.jpg)
![491093590_9401990959891188_3811507405140479177_n](https://hackmd.io/_uploads/Bk0RhcOZxg.jpg)



---

## 第三週

這週我們嘗試製作了多種不同版本的車體設計，雖然每個版本都有進步，但也伴隨著一些新的小問題出現。第一版在設計時忽略了排線的路徑，尋跡模組的排線沒有預留從車體下方往上拉的孔洞，導致實際安裝時排線會卡住或者無法美觀地佈線。這讓我們意識到，在模組配置完成後，必須要多花心思在線材走向的規劃，否則會影響整體穩定度與維修性。

在後續的版本中，我們試圖修正這個問題，但又遇到了新的挑戰。某一版的設計雖然加入了排線孔洞，不過卻讓輔助輪的位置受到干涉，導致輔助輪無法順利轉動或安裝，進而影響整體行駛的穩定性與流暢度。這讓我們開始重新檢討底盤的整體空間配置，並考慮是否要在設計時就預留出模組化的安裝位置與走線通道，以提高後續調整與擴充的彈性。

雖然每一版都有些小問題，但這些錯誤也幫助我們一步步更了解整體車體結構與實作時會遇到的細節。我們會繼續調整，希望下個版本能在功能與結構上都更完善。
### 程式
    #include <Wire.h>
    float old_error ;
    float i;
    float d;
    int L1, L2, L3, L4, L5, L6, L7, L8;
    void setup() {
      pinMode(10, OUTPUT);
      pinMode(11, OUTPUT);
      pinMode(5, OUTPUT);
      pinMode(6, OUTPUT);
      for (int i = 2; i <= 9; i++)
      {
        pinMode(i, INPUT);
      }
      Wire.begin();
    }

    void loop() {
      byte data = 0; // 用于存储读取的数据

      Wire.beginTransmission(0x12); // I2C设备的地址
      Wire.write(0x30); // 寄存器地址
      Wire.endTransmission();

      delay(10); // 给设备足够的时间来处理请求

      Wire.requestFrom(0x12, 1); // 请求1个字节的数据
      while (Wire.available()) {
        data = Wire.read(); // 读取数据
      }

      L1 = (data >> 7) & 0x01;
      L2 = (data >> 6) & 0x01;
      L3 = (data >> 5) & 0x01;
      L4 = (data >> 4) & 0x01;
      L5 = (data >> 3) & 0x01;
      L6 = (data >> 2) & 0x01;
      L7 = (data >> 1) & 0x01;
      L8 = (data >> 0) & 0x01;


      /* Serial.print(L1);
        Serial.print(L2);
        Serial.print(L3);
        Serial.print(L4);
        Serial.print(L5);
        Serial.print(L6);
        Serial.print(L7);
        Serial.println(L8);*/
      float error;

      /* if (L3 == 0 && L4 == 0 && L5 == 0 && L6 == 0)
        {
         digitalWrite(12, HIGH); //右
         analogWrite(10, 230);
         digitalWrite(13, HIGH); //左
         analogWrite(11, 230);
         digitalWrite(12, HIGH); //右
         analogWrite(10, 230);
         analogWrite(11, 230);
         digitalWrite(13, HIGH); //左

        }*/
      if (L1 == 0)
      {
        error = 3;//太右
      }
      else if (L2 == 0)//白色1,黑色0
      {
        error = 2;//太左
      }
      else if (L3 == 0)
      {
        error = 1;
      }
      else if (L4 == 0)
      {
        error = 0;//太右
      }
      else if (L5 == 0)//白色1,黑色0
      {
        error = 0;//太左
      }
      else if (L6 == 0)
      {
        error = -1;
      }
      else if (L7 == 0)
      {
        error = -2;
      }
      else if (L8 == 0)
      {
        error = -3;
      }

      i = constrain(i + error, -100, 100);
      d = error - old_error;

      float correct = 23 * error + 16 * d ;

      int rightspeed = 225 - correct;
      int leftspeed = 225 + correct;
      if (rightspeed > 255)
      {
        rightspeed = 510 - rightspeed;
        digitalWrite(10, HIGH); //右
        analogWrite(5, rightspeed);
      }
      else
      {
        digitalWrite(5, HIGH); //右
        analogWrite(10, rightspeed);
      }
      if (leftspeed > 255)
      {
        leftspeed = 510 - leftspeed;
        digitalWrite(11, HIGH); //右
        analogWrite(6, leftspeed);
      }
      else
      {
        digitalWrite(6, HIGH); //右
        analogWrite(11, leftspeed);
      }
      old_error = error;
      /*int a = 255 - leftspeed;
        int b = rightspeed - 255;
        Serial.print(a);
        Serial.print(",");
        Serial.println(b);*/
    }
### 相片
![491650180_642656871940314_8065051041912791271_n](https://hackmd.io/_uploads/SJTlTcdWlx.jpg)
![494325173_630496253313665_7481080289022598827_n](https://hackmd.io/_uploads/rypg65uWgx.jpg)
![494327800_2149001498856918_7983571083329864367_n](https://hackmd.io/_uploads/SJalTc_beg.jpg)
![494329210_8734291850007173_8886672350318653281_n](https://hackmd.io/_uploads/rkTxpqu-gx.jpg)


---


## 第四週

這週我們開始撰寫程式，使用8路紅外線感測器搭配權重和PID控制來進行路徑追蹤。測試時發現，原本的PID寫法在感測器與輪子距離太遠時會產生延遲，導致反應不靈敏。因此，我們將感測器移至車體中間、更加靠近輪子的位置，經測試後明顯改善了反應速度。

不過，在面對轉角很小的彎道時，我們發現車輛的迴轉半徑過大，加上輪胎容易打滑，常常導致迷航。為了解決這個問題，我們更換了抓地力更強的輪胎，並調整了程式邏輯。原本程式只能讓一個馬達轉動、另一個靜止，修改後改為可同時讓兩個輪子進行一前一後的轉動，大幅縮小了迴轉半徑，效果明顯改善。

目前PID參數仍需進一步調整，還有一些細節尚未完成，預計接下來會持續優化。






---

# **最終版程式**



    #include <Wire.h>
    float old_error ;
    float i;
    float d;
    float error;
    int L1, L2, L3, L4, L5, L6, L7, L8, rotate;
    void Sensor()
    {
      byte data = 0; // 用於存儲讀取的數據變數

      Wire.beginTransmission(0x12); // I2C設備的地址
      Wire.write(0x30); // 寄存器地址
      Wire.endTransmission();

      delay(10); // 給設備足夠的時間來處理請求
      Wire.requestFrom(0x12, 1); // 請求1個字節的數據
      while (Wire.available()) {
        data = Wire.read(); // 讀取數據
      }
      L1 = (data >> 7) & 0x01;
      L2 = (data >> 6) & 0x01;
      L3 = (data >> 5) & 0x01;
      L4 = (data >> 4) & 0x01;
      L5 = (data >> 3) & 0x01;
      L6 = (data >> 2) & 0x01;
      L7 = (data >> 1) & 0x01;
      L8 = (data >> 0) & 0x01;
    }
    void setup() {
      pinMode(10, OUTPUT);
      pinMode(11, OUTPUT);
      pinMode(5, OUTPUT);
      pinMode(6, OUTPUT);
      for (int i = 2; i <= 9; i++)
      {
        pinMode(i, INPUT);
      }
      Wire.begin();
      //Serial.begin(9600);
    }
    void loop() {
      rotate = 0;
      Sensor();
      if (L1 + L2 + L3 + L4 + L5 + L6 + L7 + L8 < 5)
      {
        digitalWrite(5, HIGH); //右
        digitalWrite(10, HIGH);
        digitalWrite(6, HIGH); //左
        digitalWrite(11, HIGH);
        delay(500);
        digitalWrite(5, HIGH); //直走
        analogWrite(10, 230);
        digitalWrite(6, HIGH);
        analogWrite(11, 230);
        delay(300);
        digitalWrite(5, HIGH);
        digitalWrite(10, HIGH);
        digitalWrite(6, HIGH);
        digitalWrite(11, HIGH);
        Sensor();
        if (L1 + L2 + L3 + L4 + L5 + L6 + L7 + L8 > 6) {
          Sensor();
          while (L1 + L2 + L3 + L4 + L5 + L6 + L7 + L8 > 7) {
            if (rotate > 45) {
              digitalWrite(5, HIGH); //左轉
              analogWrite(10, 220);
              digitalWrite(11, HIGH);
              analogWrite(6, 220);
              Sensor(); 
            }
            else {
              digitalWrite(10, HIGH); //右轉
              analogWrite(5, 220);
              digitalWrite(6, HIGH);
              analogWrite(11, 220);
              Sensor();
              rotate++;
            }
          }
        }
      }
    else if (L1 == 0)
    {
      error = 1.5;//太右
    }
    else if (L2 == 0)//白色1,黑色0
    {
      error = 1;//太左
    }
    else if (L3 == 0)
    {
      error = 0.5;
    }
    else if (L4 == 0)
    {
      error = 0;//太右
    }
    else if (L5 == 0)//白色1,黑色0
    {
      error = 0;//太左
    }
    else if (L6 == 0)
    {
      error = -0.5;
    }
    else if (L7 == 0)
    {
      error = -1;
    }
    else if (L8 == 0)
    {
      error = -1.5;
    }
    i = constrain(i + error, -100, 100);
    d = error - old_error;

    float correct = 20 * error + i * 0.01 + 25 * d ;

    int rightspeed = 225 - correct;
    int leftspeed = 225 + correct;
    if (rightspeed > 255)
    {
      rightspeed = 520 - rightspeed;
      digitalWrite(10, HIGH); //右
      analogWrite(5, rightspeed);
    }
    else
    {
      digitalWrite(5, HIGH); //右
      analogWrite(10, rightspeed);
    }
    if (leftspeed > 255)
    {
      leftspeed = 520 - leftspeed;
      digitalWrite(11, HIGH); //右
      analogWrite(6, leftspeed);
    }
    else
    {
      digitalWrite(6, HIGH); //右
      analogWrite(11, leftspeed);
    }
    old_error = error;
    }


# 比賽
![494820890_1725340601704732_2901879589007977701_n](https://hackmd.io/_uploads/rk7BksO-eg.jpg)
![494828003_1700219037254490_4098254403474269384_n](https://hackmd.io/_uploads/rkXHksOblg.jpg)

# 心得省思
這次比賽從買馬達到