# การใช้ AI กับ Sensor บน AIoT บอร์ด
ในโปรเจ็ตนี้ เราจะสอนวิธีการนำ AI ตรวจจับวัตถไปใช้คู่กับเซ็นเซอร์ต่างๆ บน AIoT บอร์ด <br/>
<strong>เราต้องมีโมเดล [Fomo](https://github.com/San279/AIoT_Board/blob/main/Readme-th.md) และรันไฟล์ใน [object-detect-FOMO-steam-Esp32](https://github.com/San279/AIoT_Board/blob/main/object-detect-FOMO-stream-Esp32/Readme-th.md) ให้เสร็จเรียบร้อยเพื่อนำมาใช้กับเซนเซอร์ ต่างๆ ในโปรเจ็คนี้</strong>
<br/><br/>
## การใช้ AI กับ NeoPixel
<strong> 1. ต่อ I/O 1 กับ NeoPixel บน AIoT บอร์ด </strong><br/> <br/>
![alt-text]()
<br/><br/><br/><br/>
<strong> 2. เปิดไฟล์ FOMO_object_detect_stream_Esp32.ino กดไปที่ sketch และ Add .Zip library หลังจากนั้นให้ไปที่แฟ้ม FOMO_object_detect_stream_neopixel และ เลือก zip ไฟล์ในแฟ้มนั้น</strong> <br /><br />
![alt-text]()
<br/><br/><br/><br/>
<strong> 3. ใน header file เพิ่ม โค้ดส่วนนี้เข้าไปใน FOMO_object_detect_stream_Esp32 </strong> <br/><br/>
ในโค้ดส่วนนี้เรา import library ของ neopixel และเซ็ท I/O pin หลังจากนั้น เราสร้าง object ชื่อว่า pixels เพื่อที่จะนำไปใช้ในโค้ดของเรา
<br/>
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
<br/><br/><br/><br/>
<strong> 4. เพิ่ม โค้ดของ function definition เราจะตั้งชื่อมันว่า change_color มี 2 input เพื่อเก็บชื่อของ labels และจำนวนของผล AI ใน result_size</strong> <br/><br/>
  ```text1
void change_color(String labels[], int result_size);
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/functiondef_neo.png)
<br/><br/><br/><br/>
<strong> 5. เพิ่มส่วน setup ของ library neoPixel ใน void_setup() เพื่อเริ่มใช้ object pixels ที่เราสร้างขึ้นมาใน step 3</strong> <br/><br/>
  ```text1
#if defined(__AVR_ATtiny85__) && (F_CPU == 16000000)
  clock_prescale_set(clock_div_1);
#endif
  pixels.begin(); 
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/neopixel_setup.PNG)
<br/><br/><br/><br/>
<strong> 6. เพิ่ม โค้ดของ change_color ตรงส่วนไหนในไฟล์เราก้ได้ </strong> <br/> <br/>
ในโค้ดส่วนนี้ให้เราเซ็ทค่าของ if ตามชื่อวัตถุหรือ label ที่เราใช้ฝึกโมเดลเรา ส่วนใน tutorial นี้โมเดลของเรามีอยู่ 3 label นั้นคือ red blue และ green ซึ่งเราจะ set ค่าสีของ NeoPixel ให้มีสีตามผล label ของเรา
<br/>
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
<br/><br/><br/><br/>
<strong> 7. เพิ่มตัวแปรของผล AI ในช่วงบนของ static esp_err_t stream_handler(httpd_req_t *req) ก่อน while loop เราจะเก็บผลของ AI ใน String labels[] และจำนวนของผลใน result_size </strong> <br/> <br/>
  ```text1
String labels[10];
int result_size = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/variables_none.png)
<br/><br/><br/><br/>
<strong> 8. เก็บผลของ AI ใน static esp_err_t stream_handler(httpd_req_t *req) ตรงในส่วน for loop เพื่อเก็บผล AI และนำไปใช้ใน function change_color() หลัง for loop</strong> <br/> <br/>
ส่วนที่เพิ่มใน for loop เพื่อเก็บผลและจำนวนของผล AI ใว้ในตัวแปร
  ```text1
labels[result_size] = bb.label;
result_size++;
```
เรียกใช้ function change_color() หลัง for loop และเซ็ทจำนวนผลกลับเป็น 0
  ```text1
change_color(labels, result_size);
result_size = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/neopixel_placement.png)
<br/><br/><br/><br/>
<strong> 9. เพิ่มส่วนของการเคลียสี neopixel ในส่วนท้ายของ static esp_err_t stream_handler(httpd_req_t *req) ก่อน ei_sleep(1000) เพื่อรีเซ็ตสี neopixel ในกรณีที่วัตถุนั้นไม่อยู่ในเฟรมเราแล้ว </strong> <br/> <br/>
  ```text1
pixels.clear();
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/neopixel_clear.png)
<br/><br/><br/><br/><br/>

## การคุมความสว่าง LED ด้วย AI
<strong> 1. ต่อ I/O 2 กับ LED บน AIoT บอร์ด </strong><br/> <br/>
![alt-text]()
<br/><br/><br/><br/>
<strong> 2. เนื่องจากเราจะใช้ Pwm เพื่อคุมความสว่าง LED เราประกาศตัวแปรเพื่อใช้ใน ledc ในเสต็ปที่ 4 </strong>
<br/><br/>
LEDC_CHANNEL ตัวแปรของ channel ของ pwm ใน channel 1 เราสามารถใช้ได้กับ I/O 2 ถ้าอยากเปลี่ยน channel อ่านต่อเพิ่มเติ่มได้ที่ [luisllamas](https://www.luisllamas.es/en/esp32-s3-hardware-details-pinout/) <br/>
LEDC_RESOLUTION จำนวน hertz <br/>
RESOLUTION_BITS จำนวน bits ของ pwm ถ้าเราเซ็ทมันใว้ที่ 8 เราปรับค่า pwm ได้สูงสุด 256 โดยวิธีคำนวนคือ 2^(bits) <br/>
LED_PIN I/O ของ Pin ที่เราใช้ <br/>
duty_cycle ตัวแปรของค่า pwm <br/>
  ```text1
#define LEDC_CHANNEL 1
#define LEDC_RESOLUTION 200
#define RESOLUTION_BITS 8
#define LED_PIN 2
int duty_cycle = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/led_pins.PNG)
<br/><br/><br/><br/>
<strong> 3. เพิ่ม โค้ดของ function definition เราจะตั้งชื่อมันว่า change_led_brightness มี 2 input เพื่อเก็บชื่อของ labels และจำนวนของผล AI ใน result_size คล้ายกับในส่วน NeoPixel</strong> <br/><br/>
  ```text1
void change_led_brightness(String labels[], int result_size);
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/functiondef_led.png)
<br/><br/><br/><br/>
<strong> 4. นำตัวแปรในเสต็ป 2 มาใช้ใน ledc function เพื่อเริ่ม channel ของ pwm และ เซ็ท I/O ใน void_setup() </strong>
  ```text1
ledcSetup(LEDC_CHANNEL, LEDC_RESOLUTION, RESOLUTION_BITS);
ledcAttachPin(LED_PIN, LEDC_CHANNEL);
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/led_setup.png)
<br/><br/><br/><br/>
<strong> 5. เพิ่ม โค้ดของ function change_led_brightness ตรงส่วนไหนในไฟล์เราก้ได้ </strong> <br/> <br/>
ในโค้ดส่วนนี้ให้เราเซ็ทค่าของ if ตามชื่อวัตถุหรือ label ที่เราใช้ฝึกโมเดลเรา ส่วนใน tutorial นี้โมเดลของเรามีอยู่ 2 label นั้นคือ + กับ - ซึ่งเราจะเปลี่ยนความสว่างของ LED ตามสัญลักษณ์มือเรา<br/>
ทุกๆ ครั้งที่เราได้ label + เราจะเพิ่มความสว่าง LED โดยการเพิ่ม duty cycle + 32 โดยเราล็อคค่าของมันใว้ที่มากสุด 256 เนื่องจากเราเซ็ท RESOLUTION_BITS ใว้ที่ 8 และทุกๆครั้งที่ label เป็น - เราจะลดความสว่าง LED และล็อคค่ามันไม่ให้ต่ำกว่า 0 <br/> 
  ```text1
void change_led_brightness(String labels[], int result_size) {
  for(int i = 0; i < result_size; i++){
    if (labels[i] == "+") {
      duty_cycle += 32;
      if (duty_cycle > 256) {
        duty_cycle = 256;
      }
    } else if (labels[i] == "-") {
      duty_cycle -= 32;
      if (duty_cycle < 0) {
        duty_cycle = 0;
      }
    }
    ledcWrite(LEDC_CHANNEL, duty_cycle);
  }
}
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/led_function.png)
<br/><br/><br/><br/>
<strong> 6. เพิ่มตัวแปรเพื่อเก็บผล AI ในช่วงบนของ static esp_err_t stream_handler(httpd_req_t *req) ก่อน while loop เราจะเก็บผลของ AI ใน String labels[] และจำนวนของผลใน result_size </strong> <br/> <br/>
  ```text1
String labels[10];
int result_size = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/variables_none.png)
<br/><br/><br/><br/>
<strong> 7. เก็บผลของ AI ใน static esp_err_t stream_handler(httpd_req_t *req) ตรงในส่วน for loop เพื่อเก็บผล AI และนำไปใช้ใน function change_led_brightness() หลัง for loop</strong> <br/> <br/>
ส่วนที่เพิ่มใน for loop เพื่อเก็บผลและจำนวนของผล AI ใว้ในตัวแปร
  ```text1
labels[result_size] = bb.label;
result_size++;
```
เรียกใช้ function change_led_brightness() หลัง for loop และเซ็ทจำนวนผลกลับเป็น 0
  ```text1
change_led_brightness(labels, result_size);
result_size = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/led_placement.png)
<br/><br/><br/><br/><br/>
## การควบคุม Servo-motor ด้วย AI
<strong> 1. ต่อ I/O 12 กับ Servo-motor บน AIoT บอร์ด </strong><br/> <br/>
![alt-text]()
<br/><br/><br/><br/>
<strong> 2. เนื่องจากเราใช้ Pwm เหมือนกันกับการคุม Servo เราประกาศตัวแปรคล้ายกันกับ LED เพื่อใช้ใน ledc ในเสต็ปที่ 4 </strong>
<br/><br/>
LEDC_CHANNEL ตัวแปรของ channel ของ pwm ใน channel 1 เราสามารถใช้ได้กับ I/O 2 ถ้าอยากเปลี่ยน channel อ่านต่อเพิ่มเติ่มได้ที่ [luisllamas](https://www.luisllamas.es/en/esp32-s3-hardware-details-pinout/) <br/>
LEDC_RESOLUTION จำนวน hertz <br/>
RESOLUTION_BITS จำนวน bits ของ pwm ถ้าเราเซ็ทมันใว้ที่ 8 <br/>
SERVO_PIN I/O ของ Pin ที่เราใช้ <br/>
duty_cycle ตัวแปรของค่า pwm <br/>
  ```text1
#define LEDC_CHANNEL 1
#define LEDC_RESOLUTION 200
#define RESOLUTION_BITS 8
#define SERVO_PIN 12
int duty_cycle = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/servo_pins.PNG)
<br/><br/><br/><br/>
<strong> 3. เพิ่ม โค้ดของ function definition เราจะตั้งชื่อมันว่า change_servo ในเคสนี่เรามี 3 input ของฟังชั้น เพื่อการกรองผลของ AI ที่มีระดับ confidence ที่สูงสุดเท่านั้น </strong> <br/><br/>
  ```text1
void change_servo(String labels[], float confidence[], int result_size);
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/functiondef_servo.png)
<br/><br/><br/><br/>
<strong> 4. นำตัวแปรในเสต็ป 2 มาใช้ใน ledc function เพื่อเริ่ม channel ของ pwm และ เซ็ท I/O ใน void_setup() </strong>
  ```text1
ledcSetup(LEDC_CHANNEL, LEDC_RESOLUTION, RESOLUTION_BITS);
ledcAttachPin(LED_PIN, LEDC_CHANNEL);
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/servo_setup.png)
<br/><br/><br/><br/>
<strong> 5. เพิ่ม โค้ดของ function change_servo ตรงส่วนไหนในไฟล์เราก้ได้ </strong> <br/> <br/>
ในโค้ดส่วนนี้ให้เราเซ็ทค่าของ if ตามชื่อวัตถุหรือ label ที่เราใช้ฝึกโมเดลเรา ส่วนใน tutorial นี้โมเดลของเรามีอยู่ 2 label นั้นคือ + กับ - ซึ่งเราจะเปลี่ยนทิศทางของ Servo ตามสัญลักษณ์มือเรา<br/>
fucntion นี้จะยาวกว่าส่วน LED และ NeoPixel เนื่องจากเราจะทำการกรองผลของ AI ที่มีค่า confidence สูงสุดเท่านั้น โดยใช้ for loop เพื่อเปรียบเทียบค่า confidence ของแต่ละ label และ save ชื่อ label ที่มีค่าสูงที่สุดใว้ในตัวแปร highest_label <br/>
เมื่อเราทำการกรองผลเรียบน้อยแล้ว เราใช้ if statement เพื่อเช็คค่า highest_label ว่าเป็น + หรือ - และหมุน Servo ไปในทิศทางขวาหรือซ้าย<br/> 
  ```text1
void change_servo(String labels[], float confidence[], int result_size){
  String highest_label;
  float highest_confidence;
  if (result_size == 0){
    return;
  }
  for(int i = 0; i < result_size; i++){
    if (i == 0){
      highest_label = labels[i];
      highest_confidence = confidence[i];
      continue;
    }
    if (confidence[i] > highest_confidence){
      highest_label = labels[i];
      highest_confidence = confidence[i];
    }
  }
  if (highest_label == "+"){
      duty_cycle += 64;
      if (duty_cycle > 256){
        duty_cycle = 256;
      } 
  }else if(highest_label == "-"){
    duty_cycle -= 64;
    if (duty_cycle < 0){
      duty_cycle = 0;
    }
  }
    //Serial.println(duty_cycle);
    ledcWrite(LEDC_CHANNEL, duty_cycle);
}
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/servo_function.png)
<br/><br/><br/><br/>
<strong> 6. เพิ่มตัวแปรเพื่อเก็บผล AI ในช่วงบนของ static esp_err_t stream_handler(httpd_req_t *req) ก่อน while loop เราจะเก็บผลของ AI ใน String labels[] ระดับความมั่นใจใน confidence และจำนวนของผลใน result_size </strong> <br/> <br/>
  ```text1
String labels[10];
confidence[10];
int result_size = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/variable_con.png)
<br/><br/><br/><br/>
<strong> 7. เก็บผลของ AI ใน static esp_err_t stream_handler(httpd_req_t *req) ตรงในส่วน for loop เพื่อเก็บผล AI และนำไปใช้ใน function change_servo() หลัง for loop</strong> <br/> <br/>
ส่วนที่เพิ่มใน for loop เพื่อเก็บผลและจำนวนของผล AI ใว้ในตัวแปร
  ```text1
labels[result_size] = bb.label;
confidence[result_size] = bb.value;
result_size++;
```
เรียกใช้ function change_servo() หลัง for loop และเซ็ทจำนวนผลกลับเป็น 0
  ```text1
change_servo(labels, confidence, result_size);
result_size = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/servo_placement.png)
<br/><br/><br/><br/><br/>
