# การใช้ AI กับ Sensor บน AIoT บอร์ด
ในโปรเจ็ตนี้ เราจะสอนวิธีการนำ AI ตรวจจับวัตถไปใช้คู่กับเซ็นเซอร์ต่างๆ บน AIoT บอร์ด <br/>
<strong>เราต้องมีโมเดล [Fomo](https://github.com/San279/AIoT_Board/blob/main/Readme-th.md) และรันไฟล์ใน [object-detect-FOMO-steam-Esp32](https://github.com/San279/AIoT_Board/blob/main/object-detect-FOMO-stream-Esp32/Readme-th.md) ให้เสร็จเรียบร้อยเพื่อนำมาใช้กับเซนเซอร์ ต่างๆ ในโปรเจ็คนี้</strong>
<br/><br/>
## การใช้ AI กับ NeoPixel
<strong> 1. ต่อ I/O 1 กับ NeoPixel บน AIoT บอร์ด </strong><br/> <br/>
![alt-text]()
<br/><br/><br/>
<strong> 2. เปิดไฟล์ FOMO_object_detect_stream_Esp32.ino กดไปที่ sketch และ Add .Zip library หลังจากนั้นให้ไปที่แฟ้ม FOMO_object_detect_stream_neopixel และ เลือก zip ไฟล์ในแฟ้มนั้น</strong> <br /><br />
![alt-text]()
<br/><br/><br/>
<strong> 3. ใน header file เพิ่ม โค้ดส่วนนี้เข้าไปใน FOMO_object_detect_stream_Esp32 </strong> <br/><br/>
ในโค้ดส่วนนี้เรา import libray ของ neopixel และเซ็ท I/O pin หลังจากนั้น เราสร้าง object ชื่อว่า pixels เพื่อที่จะนำไปใช้ในโค้ดของเรา
<br/><br/>
  ```text1
#include <Adafruit_NeoPixel.h>
#ifdef __AVR__
#include <avr/power.h>  
#endif
#define NEO_PIN 1
#define NUMPIXELS 3  
Adafruit_NeoPixel pixels(NUMPIXELS, NEO_PIN, NEO_GRB + NEO_KHZ800);
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/neopixel_header.PNG)
<br/><br/><br/>
<strong> 4. เพิ่ม โค้ดของ function definition เราจะตั้งชื่อมันว่า change_color </strong> <br/><br/>
  ```text1
void change_color(String labels[], int result_size);
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/functiondef_neo.png)
<br/><br/><br/>
<strong> 5. เพิ่มส่วน setup ของ library neoPixel ใน void_setup() เพื่อเริ่มใช้ object pixels ของเรา</strong> <br/><br/>
  ```text1
#if defined(__AVR_ATtiny85__) && (F_CPU == 16000000)
  clock_prescale_set(clock_div_1);
#endif
  pixels.begin(); 
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/neopixel_setup.PNG)
<br/><br/><br/>
<strong> 6. เพิ่ม โค้ดของ change_color ตรงส่วนไหนในไฟล์เราก้ได้ </strong> <br/> <br/>
ในโค้ดส่วนนี้ให้เราเซ็ทค่าของ if ตามชื่อวัตถุหรือ label ที่เราใช้ฝึกโมเดลเรา ส่วนใน tutorial นี้โมเดลของเรามีอยู่ 3 label นั้นคือ red blue และ green ซึ้งเราจะ set ค่าสีของ NeoPixel ให้มีสีตามผล label ของเรา
<br/> <br/>
  ```text1
void change_color(String labels[], int result_size) {
  for(int i = 0; i < result_size; i++){
    if (labels[i] == "red") {
      pixels.setPixelColor(0, pixels.Color(255, 0, 0)); //red
    } else if (labels[i] == "green") {
      pixels.setPixelColor(1, pixels.Color(0, 255, 0)); //green
    } else if (labels[i] == "blue") {
      pixels.setPixelColor(2, pixels.Color(0, 0, 255)); //blue
    }
  }
    pixels.show();
}
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/neopixel_function.png)
<br/><br/><br/>
<strong> 7. เพิ่มตัวแปรของผล AI ในช่วงบนของ static esp_err_t stream_handler(httpd_req_t *req) ก่อน while loop เราจะเก็บผลของ AI ใน String labels[] และจำนวนของผลใน result_size </strong> <br/> <br/>
  ```text1
String labels[10];
int result_size = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/variables_none.png)
<br/><br/><br/>
<strong> 8. เพิ่ม function ของเราใน static esp_err_t stream_handler(httpd_req_t *req) ตรงในส่วน for loop และหลัง เพื่อเก็บผล AI และนำไปใช้ใน function change_color() </strong> <br/> <br/>
ส่วนที่เพิ่มใน for loop เก็บผลของ AI ใว้ในตัวแปร
  ```text1
labels[result_size] = bb.label;
result_size++;
```
<br/>
เรียกใช้ function change_color() หลัง for loop และเซ็ทจำนวนผลกลับเป็น 0
  ```text1
change_color(labels, result_size);
result_size = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/neopixel_placement.png)
<br/><br/><br/>
<strong> 9. เพิ่มส่วนของการเคลียสี neopixel ในส่วนท้ายของ static esp_err_t stream_handler(httpd_req_t *req) ก่อน ei_sleep(1000) เพื่อ reset สี neopixel ในกรณีที่วัตถุนั้น ไม่อยู่ในเฟรมเราแล้ว <br/> <br/>
  ```text1
pixels.clear();
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/neopixel_clear.png)
<br/><br/><br/>


