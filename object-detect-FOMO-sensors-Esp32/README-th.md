# การใช้ AI กับ Sensor บน AIoT บอร์ด
ในโปรเจ็ตนี้ เราจะสอนวิธีการนำ AI ตรวจจับวัตถไปใช้คู่กับเซ็นเซอร์ต่างๆ บน AIoT บอร์ด <br/>
<strong>เราต้องมีโมเดล [Fomo](https://github.com/San279/AIoT_Board/blob/main/Readme-th.md) และรันไฟล์ใน [object-detect-FOMO-steam-Esp32](https://github.com/San279/AIoT_Board/blob/main/object-detect-FOMO-stream-Esp32/Readme-th.md) ให้เสร็จเรียบร้อยเพื่อนำมาใช้กับเซนเซอร์ ต่างๆ ในโปรเจ็คนี้</strong>
<br/><br/>
## การใช้ AI กับ NeoPixel
<strong> 1. ต่อ I/O 1 กับ NeoPixel บน AIoT บอร์ด </strong><br/> <br/>
[alt-text]()
<br/><br/><br/>
<strong> 2. เปิดไฟล์ FOMO_object_detect_stream_Esp32.ino กดไปที่ sketch และ Add .Zip library หลังจากนั้นให้ไปที่แฟ้ม FOMO_object_detect_stream_neopixel และ เลือก zip ไฟล์ในแฟ้มนั้น</strong> <br /><br />
[alt-text]()
<br/><br/><br/>
<strong> 3. ใน header file เพิ่ม โค้ดส่วนนี้เข้าไปใน FOMO_object_detect_stream_Esp32 </strong> <br/><br/>
[alt-text]()
<br/><br/><br/>
<strong> 4. เพิ่ม โค้ดของ function definition ของ change_color </strong> <br/><br/>
[alt-text]()
<br/><br/><br/>
<strong> 5. เพิ่มส่วน setup ของ library neoPixel ใน void_setup() </strong> <br/><br/>
[alt-text]()
<br/><br/><br/>
<strong> 6. เพิ่ม โค้ดของ change_color ตรงส่วนไหนในไฟล์เราก้ได้ </strong> <br/> <br/>
ในโค้ดส่วนนี้ให้เราเซ็ทค่าของ if ตามวัตถุหรือ label ที่เราใช้ฝึกโมเดลเรา โมเดลที่ใช้ในการทำ tutorial นี้มีอยู่ 3 label นั้นคือ red blue และ green ซึ่งสีของ NeoPixel จะเปิดตามผลของค่า AI เรา
<br/> <br/>
[alt-text]()
<br/><br/><br/>
<strong> 7. เพิ่มตัวแปรของผล AI ในช่วงบนของ static esp_err_t stream_handler(httpd_req_t *req) ก่อน while loop เราจะเก็บค่าของผล AI ใน String labels[] และจำนวนของผลใน result_size </strong> <br/> <br/>
[alt-text]()
<br/><br/><br/>
<strong> 8. เพิ่ม function ของเราใน static esp_err_t stream_handler(httpd_req_t *req) ตรงส่วนหลัง for loop ของการคำนวลผล AI เพื่อเรียกใช้ function เรา</strong> <br/> <br/>


