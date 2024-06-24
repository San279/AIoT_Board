# Automating FOMO with sensors
[ภาษาไทย](https://github.com/San279/AIoT_Board/blob/main/object-detect-FOMO-sensors-Esp32/README-th.md)
<br/><br/>
This project will be exploring ways to automate object detection model with basic sensors such as NeoPixel, LED, and Servo motor. <br/>
<strong>This project requires [Fomo](https://github.com/San279/AIoT_Board/blob/main/Readme-th.md) model and succesfully run Arduino project from [object-detect-FOMO-steam-Esp32](https://github.com/San279/AIoT_Board/blob/main/object-detect-FOMO-stream-Esp32/Readme-th.md) 
since we will be editing the Arduino Files with these code snippets for experimenting and understanding.</strong>
<br/><br/>
## Change NeoPixel colors with FOMO
<strong> 1. Connect I/O 1 of the Esp32 or AIot board with NeoPixel.</strong><br/> <br/>
![alt-text]()
<br/><br/><br/><br/>
<strong> 2.  Open "FOMO_object_detect_stream_Esp32.ino" located inside "object-detect-FOMO-stream-Esp32" and add .Zip Adafruit NeoPixel library from this local directory "FOMO_object_detect_stream_neopixel" to Arduino IDE.</strong> <br /><br />
![alt-text]()
<br/><br/><br/><br/>
<strong> 3. Add these header files to the folder to import the zip library and instanstiate an object to use with NeoPixel.</strong>
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
<strong> 4. Add function definition named change_color with 2 inputs to obtain inference results and it's size.</strong> <br/><br/>
  ```text1
void change_color(String labels[], int result_size);
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/functiondef_neo.png)
<br/><br/><br/><br/>
<strong> 5. In void_setup() add these snippets to begin using the created NeoPixel object.</strong> <br/><br/>
  ```text1
#if defined(__AVR_ATtiny85__) && (F_CPU == 16000000)
  clock_prescale_set(clock_div_1);
#endif
  pixels.begin(); 
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/neopixel_setup.PNG)
<br/><br/><br/><br/>
<strong> 6. Add a change_color function.</strong> <br/> <br/>
We will be checking if the object matched our label in this function and automate the NeoPixel. For this tutorial, our models has 3 labels which reads the color blocks as red, blue, 
and/or green of which we will be changing NeoPixel colors according to the label.
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
<strong> 7. declare string of arrays to save the labels name and number of labels in result_size in the top part of static esp_err_t stream_handler(httpd_req_t *req) function</strong> <br/> <br/>
  ```text1
String labels[10];
int result_size = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/variables_none.png)
<br/><br/><br/><br/>
<strong> 8.  Inside the for loop of static esp_err_t stream_handler(httpd_req_t *req) function we will be collecting inference results and it's size to use it with function change_color().</strong> <br/> <br/>
Add these codes inside for loop to save inference results and it's size.
  ```text1
labels[result_size] = bb.label;
result_size++;
```
Call change_color() after the for loop to pass saved labels to the function and don't forget to reset the size of inference results to 0.
  ```text1
change_color(labels, result_size);
result_size = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/neopixel_placement.png)
<br/><br/><br/><br/>
<strong> 9. Add pixels.clear() to reset NeoPixel color in static esp_err_t stream_handler(httpd_req_t *req) before ei_sleep(1000) to reset colors of NeoPixel at each and every frame</strong> <br/> <br/>
  ```text1
pixels.clear();
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/neopixel_clear.png)
<br/><br/><br/><br/><br/>

## Change LED brightness with FOMO
<strong> 1. connect I/O Pin 2 of Esp32 or AIoT Board to LED and open FOMO_object_detect_stream_Esp32.ino to add these snippets</strong><br/> <br/>
![alt-text]()
<br/><br/><br/><br/>
<strong> 2. Since we will be using PWM signal to control LED brightness, LEDC library will be used</strong>
<br/><br/>
LEDC_CHANNEL - PWM channel no.1 so we can use with I/O Pin 2, if you want to change the PWM channel read more at [luisllamas](https://www.luisllamas.es/en/esp32-s3-hardware-details-pinout/) <br/>
LEDC_RESOLUTION - hertz <br/>
RESOLUTION_BITS - number of resolution bits for the PWM signal, when it's set to 8 the highest duty cycle is 256.<br/>
LED_PIN - Output Pin for Esp32 to LED.<br/>
duty_cycle - PWM signal <br/>
  ```text1
#define LEDC_CHANNEL 1
#define LEDC_RESOLUTION 200
#define RESOLUTION_BITS 8
#define LED_PIN 2
int duty_cycle = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/led_pins.PNG)
<br/><br/><br/><br/>
<strong> 3. Add change_led_brightness as function definition with 2 inputs to save labels and it's size similiar to the previous part</strong> <br/><br/>
  ```text1
void change_led_brightness(String labels[], int result_size);
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/functiondef_led.png)
<br/><br/><br/><br/>
<strong> 4. In void setip() attach PWM pins and channels</strong>
  ```text1
ledcSetup(LEDC_CHANNEL, LEDC_RESOLUTION, RESOLUTION_BITS);
ledcAttachPin(LED_PIN, LEDC_CHANNEL);
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/led_setup.png)
<br/><br/><br/><br/>
<strong> 5. Add a function change_led_brightness </strong> <br/> <br/>
Inside the function we will be looping through the labels to check if the input labels match the given function to adjust brightness of the LED. In this part we have only 2 label namely +
and - of which we will be adjusting PWM signal according to it.<br/> 
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
<strong> 6. Like NeoPixel part we add these two arrays to store labels and it's size. Add this to the top part of "static esp_err_t stream_handler(httpd_req_t *req)". </strong> <br/> <br/>
  ```text1
String labels[10];
int result_size = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/variables_none.png)
<br/><br/><br/><br/>
<strong> 7. Once again like NeoPixel, we push inference results and it's size to arrays inside the for loop of static esp_err_t stream_handler(httpd_req_t *req) function to use it with change_led_brightness() mention eariler.</strong> <br/> <br/>
Add these variables inside for loop to store labels name and it's size.
  ```text1
labels[result_size] = bb.label;
result_size++;
```
Call function change_led_brightness() after for loop and set result size back to 0 for the next frame.
  ```text1
change_led_brightness(labels, result_size);
result_size = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/led_placement.png)
<br/><br/><br/><br/><br/>
## Control Servo-motor with FOMO
<strong> 1. Connect I/O Pin 2 of the Esp32 to Servo-motor and open FOMO_object_detect_stream_Esp32.ino to add these code snippets, don't forget to comment out the previous codes.</strong><br/> <br/>
![alt-text]()
<br/><br/><br/><br/>
<strong> 2. Since we will be using PWM signal to control Servo as well, we will use the same function.</strong>
  ```text1
#define LEDC_CHANNEL 1
#define LEDC_RESOLUTION 200
#define RESOLUTION_BITS 8
#define SERVO_PIN 2
int duty_cycle = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/servo_pins.PNG)
<br/><br/><br/><br/>
<strong> 3. Add function definition named as change_servo() with 3 input for this case to filter out inference results with highest confidence.</strong> <br/><br/>
  ```text1
void change_servo(String labels[], float confidence[], int result_size);
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/functiondef_servo.png)
<br/><br/><br/><br/>
<strong> 4. Set pins, PWM channels, and resolution bits. </strong>
  ```text1
ledcSetup(LEDC_CHANNEL, LEDC_RESOLUTION, RESOLUTION_BITS);
ledcAttachPin(LED_PIN, LEDC_CHANNEL);
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/servo_setup.png)
<br/><br/><br/><br/>
<strong> 5. Add a function change_servo </strong> <br/> <br/>
Firstly, we will be looping through labels' confidence percentage and select the highest one. Afterwards we will using the given variable to check labels name and turn the servo-motor according to it's name.
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
<strong> 6. Add these 3 variables to store labels name, confidence level , and it's size inside the top part of static esp_err_t stream_handler(httpd_req_t *req) function </strong> <br/> <br/>
  ```text1
String labels[10];
confidence[10];
int result_size = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/variable_con.png)
<br/><br/><br/><br/>
<strong> 7. Store inference results inside these variables inside for loop of static esp_err_t stream_handler(httpd_req_t *req) function to use it with change_servo() mentioned earlier.</strong> <br/> <br/>
Add these variables inside the for loop.
  ```text1
labels[result_size] = bb.label;
confidence[result_size] = bb.value;
result_size++;
```
Call change_servo() after the for loop and set result size back to 0 to use it for the next frame
  ```text1
change_servo(labels, confidence, result_size);
result_size = 0;
```
![alt-text](/object-detect-FOMO-sensors-Esp32/Images_for_readme/servo_placement.png)
<br/><br/><br/><br/><br/>
