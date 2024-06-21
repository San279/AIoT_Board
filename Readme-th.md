
## วิธีการฝึก FOMO AI ตรวจจับวัตถุโดยใช้ Edge Impulse
 [For English version](https://github.com/San279/AIoT_Board/blob/main/Readme.md)
 <br/>
 <br/>
  [FOMO](https://docs.edgeimpulse.com/docs/edge-impulse-studio/learning-blocks/object-detection/fomo-object-detection-for-constrained-devices) คือ AI ตรวจจับวัตถุที่เหมาะสำหรับไมโครคอนโทรลเลอร์มที่มีเสป็คที่จำกัด ในโปรเจ็คนี่เราจะทำการเทรน AI ให้นับสองนิ้ว โดยให้ข้อแนะนำต่างๆ ในการรวบรวมรูปภาพจากกล้อง การฝึก AI และการนำโมเดลไปรันบน Esp32-S3 ใน Arduino
<br/>
## สิงที่ต้องมีสำหรับโปรเจ็ค
 - AIoT บอร์ด Esp32-S3 หรือ Esp32 ที่มี PSRAM
 - กล้อง OV 2640
 - กล้องเว็ปแคมหรือมือถือ (ไม่จำเป็น)
 - บัญชีผู้ใช้ Edge Impulse
## ก่อนเริ่ม
  <strong> สร้างบัญชีใน Edge Impulse และกด create new project เพื่อสร้างโปรเจ็คใหม่. </strong> 
  <br/> <br/>
  ![alt text](/Images_for_readme/create_new_project.PNG)
<br/>
## การรวบรวมรูปภาพ
  <strong> 1. การรวบรวมรูปภาพเพื่อฝึก AI ประเภทนี้ควรใช้กล้องจาก AIoT หรือ Esp32 โดยตรง เราสามารถใช้ไลบรารี่ [camera-webserver-for-esp32S3](https://github.com/San279/AIoT_Board/blob/main/camera-webserver-for-esp32S3/README-th.md) เพื่อเก็บรูปภาพจาก AIoT บอร์ด </strong>
<br/><br/>   
  ![alt text](/Images_for_readme/webserver.PNG)
<br/> <br/> <br/><br/>
 <strong>2. ไปที่โปรเจ็คของเราในเว็ป Edge Impulse ในช่องด้านซ้าย กดเลือก data aquisition และกดที่ upload images เพื่ออัพโหลดไฟล์รูปภาพขึ้นบน Edge Impulse</strong>
 <br/> <br/> 
 ![alt text](/Images_for_readme/add_data.PNG)
  <br/> <br/>
![alt text](/Images_for_readme/upload_data.PNG)
  <br/> <br/> <br/> <br/> 
 <strong>3. ระหว่างการอัพโหลดจะมีตัวเลือกให้เช็คว่ารูปถ่ายนี้จะนำไปใช้ในการเทรน AI ตรวจจับวัตถุ ให้เรากด yes</strong>
  <br/> <br/> 
![alt text](/Images_for_readme/object_detection_tab..PNG)
  <br/> <br/>  <br/> <br/> <br/> 
## การฝึก AI 
  <strong> 1. กดตรง labeling queue และให้วาดกล่องกับชื่อของ class ให้หมดทุกรูปที่มีวัตถุชนิดนั้นๆ </strong>
     <br/> <br/>
ตัวอย่างของรูปที่มีมิติที่ต่างกัน 320 X 240 เราจะเห็นสีทึบในทั้งสองฝั่งของรูป ซึ่งส่วนนั้นจะถูกตัดออกไป 
 <br/> <br/>
   ![alt text](/Images_for_readme/label_320.PNG)
    <br/> <br/>
ตัวอย่างของรูปที่ใช้ในโปรเจ็คนี่ มิติเท่ากัน 96 X 96 จะไม่ถูกปรับเปลี่ยน
  <br/> <br/>
   ![alt text](/Images_for_readme/label_96.PNG)
<br/> <br/> <br/>
 <strong> 2. หลังจากกำหนด class ของเราเรียบร้อยแล้ว ให้กดไปที่ Create impulse ในช่องด้านซ้าย ในพาร์ทนี่เราสามารถกำหนดขนาดของโมเดลเรา โดยจะต้องอยู่ในผลคูณของ 8 </strong>
    <br/><br/>
    - เราสามารถกำหนดขนาดของโมเดลให้ใหญ่กว่าหรือเล็กกว่ารูปเราก้ได้ โดยจะมีข้อดีกับข้อเสียที่ตรงกันข้าม ในโมเดลใหญ่ ข้อดีคือความแม่นยำอาจจะสูงกว่า แต่จะใช้เวลาในการตรวจจับจะนานขึ้น เมื่อเทียบกับโมเดลที่เล็กกว่า ซึ่งความแม่นยำอาจจะน้อยกว่าแต่เร็วกว่า เราควรเทส AI ของเราและหาขนาดที่เหมาะสมในแต่ละสถานนะการณ์ ในส่วนของโมเดลนับนิ้วมือตัวนี้ เราต้องใช้ความเร็วเป็นหลักเลยกำหนดขนาดที่ 96 X 96 ให้มีขนาดเท่ารูปของเรา 
 <br/> <br/>
 ![alt text](/Images_for_readme/input_size.PNG)
<br/> <br/>
กดไปตรงที่ add processing block และเลือกตัวบนสุด
<br/> <br/>
 ![alt text](/Images_for_readme/add_processing.PNG)
<br/><br/>
หลังจากนั้นกดตรง add learning block และเลือกตัวบนสุด จากนั้นให้กดไปที่ save impulse
 <br/> <br/>
 ![alt text](/Images_for_readme/learning_block.PNG)
<br/><br/> <br/>
<strong> 3. หลังจากสร้าง impulse ของเราแล้ว เราสามารถกำหนดการฝึก AI โดยใช้รูปแบบ ขาวดำ(Gray Scale) หรือ สี(RGB) ซึ่งเราควรทดลองว่าแบบไหนเหมาะสมมากกว่า เราจะใช้ รูปแบบเป็น สี(RGB) พอเลือกเสร็จให้กดไปที่ save parameters เราจะเด้งขึ้นหน้าใหม่</strong>
<br/>  <br/>
 ![alt text](/Images_for_readme/rgb.PNG)
<br/> <br/>
<strong> 4. กดไปตรง generate feature เพื่อโชว์กราฟของความใกล้คลียงในแต่ละ class </strong>
 - ในส่วนของโมเดลนี้ เรามีสอง class นั้นคือ นิ้วที่ 1 กับ 2 จุดสีแดงคือหนึ่งนิ้ว ส่วนจุดสีชมพูคือสองนิ้ว เพื่อที่จะให้โมเดลของเรามีความแม่นยำ วัตถุแต่ละประเภท หรือ class จะต้องอยู่แยกกัน ซึ่งในเคสที่วงใว้นั้น วุตถุแต่ละประเภทนั้นอยู่ไกล้กันเกินไป ซึ่งทำให้โมเดลของเราไม่สามารถแยกความแตกต่างของกลุ่มรูปนั้นได้ และจะทำให้โมเดลเรามีความแม่นยำต่ำ
 - เราควรลบรูปส่วนนั้นออกและอัพโหลดรูปเพิ่มให้มีตามขั้นต่ำ (60 รูป) (อย่าลืม label รูปภาพใหม่ด้วย) ควรปรับรูปให้มีแสงไฟส่องมากขึ้น หรือ ลองเล่นกับการตั้งค่ากล้องเพือให้รูปภาพชัดขึ้น
<br/><br/>
 ![alt text](/Images_for_readme/feature_unedit.PNG)
<br/> <br/>
- กราฟรูปแบบนี่จะทำให้โมเดลแยกแยะได้ดี เพราะวัตถุแต่ละประเภทไม่อยู่ใกล้กัน
 <br/> <br/>
 ![alt text](/Images_for_readme/feature_edited.PNG)
<br/><br/> <br/>
<strong> 5. กดไปที่ Object detection ในช้องด้านซ้าย เราจะเห็นค่าต่างๆ ที่ใช้ในการเทรน AI </strong>
  - Traning cycles คือจำนวน Step ที่โมเดลทำการเทรนนิ่ง ในส่วนนี่อย่าปรับเกิน 70 เนื่องจากไม่ค่อยมีผล ส่วนในนี้ เราจะใช้เพียงแค่ 25 ครั้ง
  - Data augmentation คือการคูณรูปภาพให้มีจำนวนมากยิ่งขึ้น โดยแต่ละรูปจะมีการปรับแสง พลิก เปลี่ยนมุม ๆลๆ ส่วนนี่จำเป็นต้องเปิดใว้เนื่องจากเรามีแค่ 170 รูป
  - Learning rate ควบุคมความเร็วที่โมเดลเราจะเรียน feature ต่างๆ ในแต่ละ training cycles สามารถอ่านเพื่มเติมได้ใน [mindphp](https://www.mindphp.com/%E0%B8%9A%E0%B8%97%E0%B9%80%E0%B8%A3%E0%B8%B5%E0%B8%A2%E0%B8%99%E0%B8%AD%E0%B8%AD%E0%B8%99%E0%B9%84%E0%B8%A5%E0%B8%99%E0%B9%8C/python-tensorflow/8491-what-is-the-learning-rate.html) สำหรับโปรเจ็คนี้เราจะทิ้งค่าเดิมของมันใว้ที่ 0.001
  - Validation set size ในส่วนนี้ หลังทุกๆ traning cycle โมเดลของเราจะทำการทดสอบความแม่นยำของแต่ละประเภทโดยการนำเปอร์เซ็นของรูปทั้งหมดมาทดสอบ ควรปล่อยใว้ที่ค่าเดิมของมัน
  - batch size คือจำนวนรูปที่โมเดลจะใช้ในการเทรนในทุกๆ traning cycle ยกตัวอย่างเช่น ถ้าเราเซ็ทค่ามันที่ 8 โมเดลของเราจะฝึกโดยใช้รูปที่ 1 - 8 และใน traning cycle ต่อไป จะทำการเทรน รูปที 9 - 16 โดยค่าของ Batch size ควรอยู่ในผลคูณของ 2^n เช่น 2, 4, 8, 16, 32, 64 ยิ่งเราเซ็ทค่า batch size สูง เวลาการใช้เทรนนิ่งก้จะมากขึ้นเช่นกัน ส่วนตัวแล้วสำหรับ dataset เพียงแค่ 150 รูป batch size ที่แนะนำคือ 8 16 หรือ 32
<br/><br/>
 ![alt text](/Images_for_readme/best_setting.PNG)
<br/><br/>
  - เรามีสองตัวเลือกของ FOMO ในส่วน alpha 0.35 หรือ 0.1 เราจะใช้โมเดล FOMO 0.35
<br/><br/>
   ![alt text](/Images_for_readme/model_choice.PNG)
<br/><br/>
  - กด start training เพื่อเริ่มการฝึกโมเดลเรา
     <br/><br/>
   ![alt text](/Images_for_readme/100.PNG)
  <br/><br/>
  <strong> เทคนิคสำหรับเพิ่มความแม่นยำโมเดล </strong>
  - ลดจำนวน batch size
  - เพิ่ม traning cycles
  - เช็คขึ้นตอนที่ 4
  - เพิ่มความละเอียด(resolution) ของรูปถาพและขนาดของโมเดล
  - เพิ่มจำนวนรูป
  <br/><br/><br/><br/>
## การ build โปรเจ็คสำหรับ Arduino
  <strong> 1. ในช่องด้านซ่ายให้กดตรงที่ deployment และเลือก change deployment เป็น Arduino library </strong>
    <br/> <br/>
   ![alt text](/Images_for_readme/deployment1.PNG)
   <br/><br/><br/>
  <strong> 2. กดไปที่ change target option และเลือก Esp32 </strong>
   <br/> <br/>
   ![alt text](/Images_for_readme/deployment2.PNG)
   <br/> <br/><br/>
  <strong> 3. กด Build เพื่อโหลดโมเดล เป็น zip และไป import บน Arduino เรามีอยู่สอง library ให้เลือกในการนำโมเดลเราไปใช้ในกล่อง AIoT  </strong>
- [FOMO-object-detect-stream-Esp32](https://github.com/San279/AIoT_Board/blob/main/object-detect-FOMO-stream-Esp32/Readme-th.md) สำหรับการ Stream โมเดลเราขึ้นเว็ปเซอร์เวอร์ 
- [FOMO-object-detect-TFT](https://github.com/San279/AIoT_Board/blob/main/object-detect-FOMO-TFT-Esp32/README-th.md) สำหรับการแสดงผลของโมเดลเราบนจอ TFT บน AIoT บอร์ด โดยวิธีนี่จะ setup ง่ายกว่า
 <br/><br/><br/><br/><br/>
## Credit
ต้องขอขอบคุณ [WIRELESS SOLUTION ASIA CO.,LTD](https://wirelesssolution.asia/) สำหรับ AIOT board และ support ในโปรเจ็คนี่ และ [Bodmer / TFT_eSPI](https://github.com/Bodmer/TFT_eSPI/blob/master/README.md) สำหรับ library จอ TFT และสกริปสำหรับการรัน FOMO จาก [Edge Impulse](https://edge-impulse.gitbook.io/docs/edge-impulse-studio/learning-blocks/object-detection/fomo-object-detection-for-constrained-devices) 
