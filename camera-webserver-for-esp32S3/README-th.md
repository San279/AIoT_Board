## เว็ปเซอร์กล้อง Esp32-S3
 [For English version](https://github.com/San279/AIoT_Board/blob/main/camera-webserver-for-esp32S3/README.md)
 <br/>
 <br/>
 โปรเจ็คนี้ถูกออกแบบมาให้ใช้กับ AI ตรวจจับวัตถุ[FOMO](https://docs.edgeimpulse.com/docs/edge-impulse-studio/learning-blocks/object-detection/fomo-object-detection-for-constrained-devices) ในส่วนของการรวบรวมรูปภาพจาก AIoT บอร์ด เพื่อดาวโหลดลงบนคอมพิวเตอร์ของเราและนำไปใช้ใน [Edge Impulse](https://edgeimpulse.com/)
<br/>
<br/>
![alt text](/camera-webserver-for-esp32S3/Images_for_readme/done.PNG)
<br/>
## สิงที่ต้องมี
 - [AIoT](https://wirelesssolution.asia/) บอร์ด Esp32-S3 หรือ Esp32 ที่มี PSRAM
 - กล้อง OV 2640
 - [Arduino IDE](https://www.arduino.cc/en/software) อันเก่าหรือใหม่ก้ได้
## โครงสร้าง
 1. camera-webserver-for-esp32S3.ino - c++ สำหรับสตรีมรูปภาพจากกล้อง Esp32 ขึ้นเว็ปเซอรเวอร์
 2. index.html - html สำหรับส่วนหน้าบ้านของเว็บไซต์
 3. index.js - javascript สำหรับฟังชั้นของการรับสรีมและส่วนหน้าบ้าน
 4. styles.css - css ทำให้เว็ปดูสวยงาม
## วิธีรันโปรเจ็ค
<strong> 1. เปิดแฟ้มที่พึ่งแยก และเปิดไฟล์ camera-webserver-for-esp32S3.ino เปลี่ยนชื่อกับรหัส WIFI ในบรรทัด 24 กับ 25 ให้ใช้อันเดียวกับที่คอมพิวเตอร์เรา  </strong>
<br /><br />
![alt text](/camera-webserver-for-esp32S3/Images_for_readme/ssidPassword.PNG)
<br /><br /><br /><br />
<strong> 2. กดไปที่ tools ตรงตัวเลือกด้านบนและเปลี่ยน Board เป็น "ESP32S3 Dev Module" และเปลี่ยน PSRAM เป็น "OPI PSRAM"  </strong>
<br /><br />
![alt text](/camera-webserver-for-esp32S3/Images_for_readme/IDE_configure.PNG)
<br /><br /><br /><br />
<strong> 3. อัพโหลดโค้ดขึ้นบน ESP32-S3 เสร็จแล้วให้คัดลอก ip address  </strong>
<br /><br />
![alt text](/camera-webserver-for-esp32S3/Images_for_readme/ip_IDE.PNG)
<br /><br /><br /><br />
<strong> 4. เปิด index.html และนำ ip address ที่คัดลอกมาวางใว้ในกล่องและกด ok </strong>
<br /><br />
![alt text](/camera-webserver-for-esp32S3/Images_for_readme/ip_prompt.PNG)
<br /><br /><br /><br />
<strong> 5. เสร็จสิ้น  </strong>
<br/> <br/>
![alt text](/camera-webserver-for-esp32S3/Images_for_readme/done.PNG)
<br /><br /><br /><br />
## วิธีใช้งานเว็ปเซอร์
- เมื่อกดปุ่มรูปกล้องจะเริ่มการบันทึกรูปภาพ เราสามารถเซ็ทเวลาระหว่างการบึนทึกแต่ละรูป และเซ็ทจำนวนรูปที่อยากบันทึกต่อครั้ง<br />
![alt_text](/camera-webserver-for-esp32S3/Images_for_readme/capture_console.PNG)
<br /><br /> <br />
- เมื่อเราบึนทึกรูปแล้ว เราสามารถดาวโหลดรูปถาพโดยการกดไปที่ปุ่มตรงซ้ายล้างของเว็ป<br />
- ลบรูปที่บันทึกใว้ทั้งหมดด้วยการกดปุ่มตรงด้านขวาล่าง <br /> <br />
![alt_text](/camera-webserver-for-esp32S3/Images_for_readme/gallery_img.PNG)
<br /><br /> <br />
- เราสามารถเปลี่ยนการตั้งค่าต่างๆ และมิติของกล้องได้ สามารถดูรายละเอียดของค่ากล้องเพิ่มเติมได้ที่[https://heyrick.eu/blog/index.php?diary=20210418&keitai=0](https://heyrick.eu/blog/index.php?diary=20210418&keitai=0) <br />
- ยูเซอร์ควรตั้งชื่อของ Class หรือประเภทของวุตถุ เนื่องจาก Edge Impulse ไม่สามารถอัพโหลดรูปที่มีชื่อเหมือนกันได้ <br /><br />
![alt_text](/camera-webserver-for-esp32S3/Images_for_readme/resolution_class.PNG)
<br /><br /><br /><br />
## การรวบรวมรูปภาพสำหรับฝึก AI
  - เราควรกำหนดให้มีอย่างน้อย 60 รูปต่อวัตถุ(class) และอีก 20% เป็นรูปของพื้นหลัง ยกตัวอย่างเช่น การฝึกโมเดลให้นับนิ้วมือนั้นเราจะกำหนด 2 class คือหนึ่งนิ้วกับสองนิ้ว โดยนิ้วที่หนึ่งจะต้องมี 60 รูป ส่วนสองนิ้วจะต้องมีอีก 60 รูปภาพ และสุดท้ายควรมีภาพพื้นหลังหรือวัตถุอื่นๆ อีก 20 รูปภาพ โดยถ้ารวมทั้งหมดเราจะมีประมาณ 150 รูปเพื่อใช้ในการฝึก AI
  - สำหรับฝึก AI บน Edge Impulse เราแนะนำให้ตั้งมิติของรูปให้มีตวามสูงและความยาวที่เหมือนกัน เนื่องจากทางเว็ปไซค์ของ Edge Impulse จะตัดส่วนความยาวให้เท่ากับความสูง ซึ่งอาจจะตัดส่วนสำคัญต่างๆ ของรูปนั้นออกไป สำหรับโปรเจ็คนี้เราใช้รูปมิติ 96 X 96 และความอิ่มสีใว้ที่ 2 ถ้าอยากให้รูปมีความละเอียดมากขึ้นเราสามารถลองเล่นกับ มิติ อื่นๆ ได้<br/> <br/>
![alt_text](/camera-webserver-for-esp32S3/Images_for_readme/one96_eg.PNG)
<br/> <br/> <br/> <br/>
- ตัวอย่างรูปของ class หนึ่งนื้ว เราควรมีอย่างน้อย 2 พื้นหลังที่ต่างกันและเซ็ทระยะของวัตถุนั้นๆ ให้มีความกลาหหลาย เพื่อให้ AI ตรวจจับได้ดีขึ้น <br/> <br/>
![alt_text](/camera-webserver-for-esp32S3/Images_for_readme/one96_gal.PNG)
<br/> <br/><br/> <br/>
- ตัวอย่างรูปของ class สองนิ้ว <br/> <br/>
![alt_text](/camera-webserver-for-esp32S3/Images_for_readme/two96_gal.PNG)
<br/> <br/><br/> <br/>
- ตัวอย่างของรูปภาพพื้นหลัง รูปที่มีลักษนะคล้ายกับ class ของวัตถุเรา หรือรูปอื่นๆ ที่ไม่มีวัตถุของเรา <br/> <br/>
![alt_text](/camera-webserver-for-esp32S3/Images_for_readme/bg96_eg.PNG)
<br/> <br/><br/>
<strong> เมื่อเรารวบรวมรูปภาพเสร็จเรียบร้อย ให้ download รูปลงบนคอมพิวเตอร์และแตกไฟล์รูปของเราเพื่ออัพโหลดรูปขั้น Edge Impulse [ในเสต็ปที่สอง](https://github.com/San279/AIoT_Board/blob/main/Readme-th.md) </strong>
<br /> <br /><br /> <br /><br />
## เครดิต
ต้องขอขอบคุณ [WIRELESS SOLUTION ASIA CO.,LTD](https://wirelesssolution.asia/) สำหรับการสนับสนุนโปรเจ็คนี้ และ [RandomNerdTutorials]([RandomNerdTutorial](https://RandomNerdTutorials.com/esp32-cam-video-streaming-web-server-camera-home-assistant) สำหรับโค้ดส่วนสตรีมรูปภาพขึ้นบนเว็ปเซอร์เวอร์
