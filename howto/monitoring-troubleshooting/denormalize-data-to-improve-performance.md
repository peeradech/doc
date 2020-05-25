# เกริ่นนำ
หัวข้อนี้จะอธิบายถึง จะเพิ่มประสิทธิภาพด้วยการ Denormalizing data ใน Mendix ได้อย่างไร

จะสอนการวิธีการทำในตามหัวข้อต่อไปนี้
- Denormalize data
- เก็บข้อมูลที่ได้จากการ Denormalize data

# สิ่งที่ต้องรู้ก่อน
ก่อนที่จะเรียนรู้ในหัวข้อนี้คุณต้องเรียนรู้พื้นฐานเกี่ยวกับการสร้าง [Data model](https://docs.mendix.com/howto/data-models/create-a-basic-data-layer) ก่อน

# Denormalization
กรณีตัวอย่าง มี entity ที่เรียกใช้งานกันอยู่ 2 entity คือ Customer กับ Order ซึ่งในหน้า Customer Overview ต้องการแสดงข้อมูลยอดรวมของ Order ในการดึงข้อมูลลูกค้ามาแสดงก็สามารถทำได้เลย แต่ถ้าจะนำยอดรวมมาแสดงได้ต้องมีการคำนวณก่อน

การแสดงยอดรวมสินค้าแต่ละรายการมีด้วยกัน 2 วิธี คือ
วิธีที่ 1 สร้าง Calculate ที่ Customer แล้วทำการคำนวณ Order ทั้งหมด
   - ข้อเสียของการทำวิธีนี้คือจะทำให้หน้า overview มีปัญหาเรื่อง Performance
วิธีที่ 2 สร้าง Attribute ที่ Customer แล้วทำการเก็บยอดรวม Order ไว้
   - ข้อเสียของการทำวิธีนี้คือจะมีความซ้ำซ้อนของข้อมูลที่ต้องเก็บลง Database (แต่สามาถทำได้ง่าย ๆ โดยใช้ [Event handlers](https://docs.mendix.com/refguide/event-handlers))

การทำ Denormalization สามารถทำได้ดังนี้

1. สร้าง Domain model ตามด้านล่างนี้

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402440980.png)

2. Double-click entity Customer เพื่อเปิด Properties

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402463414.png)

3. เพิ่ม attribute **TotalOrderAmount** เลือก type เป็น Decimal
4. สร้างหน้า customer overview สำหรับการแสดงข้อมูล *Customer*
5. ตรวจสอบดูว่าได้นำเอา **TotalOrderAmount** ไปแสดงหรือไม่
6. ตรวจสอบดูหน้า Order detail มีให้เลือก Customer หรือไม่

ตอนนี้ application ของคุณก็มีส่วนที่เก้บข้อมูลยอดรวมสินค้าแล้ว แต่ยังขาดส่วนที่ทำให้ข้อมูลตรงกัน

# เก็บข้อมูล Denormalization ไว้โดยใช้ After Commit Event
1. เปิดไปที่ domain model ที่เปิด properity ของ entity Order 
2. ไปที่ **Event handlers** สร้าง Event hanlder ใหม่หนึ่งอันโดยการ click **New**
3. ให้ทำการกำหนดค่าของ **New Event Handler** ดังนี้
    * ที่ **Moment** เลือก **After**
    * ที่ **Event** เลือก **Commit**
    * สำหรับ **Past the event object** ให้เลือก **Yes**
    * Click **Select...** เพื่อไปเลือก Microflow

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402520612.png)

4. ในส่วนนี้ให้ click **New** แล้วใส่ชื่อ Microflow (จากตัวอย่างนี้ให้ใช้เป็น *Order_AfterCommit*)
5. Click **OK** เพื่อสร้าง Microflow และ Click **OK** อีกครั้งเพื่อ save event handler
6. เมื่อเปิด Microflow จะเหมือนตามนี้

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402560941.png)

เรามาดูกันว่าสิ่งที่เราจะทำใน Microflow นี้มีอะไรบ้าง เราต้องการคำนวณนยอดเงินรวมจาก Order มาเก็บที่ Customer ซึ่ง Microflow ที่เห็นมีแค่ Order ที่รับเข้ามาจาก Input parameter ดังนั้น อย่างแรกต้อง retrieve ข้อมูล Customer เป็นอันดับแรก หลังจากนั้น retrieve ข้อมูล Order ที่อยู่ภายใต้ Customer และทำการคำนวณยอดรวม เมื่อได้ยอดรวมแล้วให้ทำการเก็บยอดรวมไปไว้ที่ Customer เมื่อคุณทำเรียบร้อยแล้วจะได้ Microflow เหมือนกับด้านล่างนี้

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402575376.png)

7. เปิด **Toolbox** ซึ่งจะอยู่ด้านข้างล่างขวา Mendix Studio Pro ถ้าไม่พบให้ไปที่ **View** จะเห็น Toolbox

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402590006.png)

8. ลาก Retrieve จาก Toolbox ไปวางที่ Microflow หลัง Start และ End 

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402602520.png)

9.  Double-click ที่ Activity ดังกล่างเพื่อ Retrieve Objects ตามวิธีด้านล่างนี้
    * เลือก **Source** เป็น **By assoicaition**
    * Click **Select...** ที่ **Association**

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402631513.png)

10. ที่ **Select association** popup เลือก **Order_Customer** จากส่วนของ **Variables** หลังจากนั้น Click **Select**

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402644480.png)

จากที่เลือก **Output** ที่ได้จากการ **Retrieve Objects** จะได้ Customer Object โดยอัตโนมัติ

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402657763.png)

11. Click **OK** ควรจะได้เหมือนกับ Microflow ด้านล่างนี้

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402669300.png)

12. เพิ่ม Activity การ **Retrieve Objects** ให้ทำการกำหนดค่าดังนี้ 
    * เลือก **Source** เป็น **From Database**
    * Click **Select...** เพื่อเลือก entity

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402680339.png)

13. ที่หน้า **Select entity** popup เลือก **Order** ผ่าน **Variables** หลังจากนั้น Click **Select**

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402690968.png)

คุณสามารถเห็น **XPath constraint** และ **Output** ที่ได้จากการ **Retrieve Objects** โดยอัตโนมัติ

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402703006.png)

14. Click **OK** ควรจะได้เหมือนกับ Microflow ด้านล่างนี้

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402714019.png)

15. เพิ่ม **Aggregate list** activity ให้ทำการกำหนดค่าดังนี้
    * ที่ **Input > List** เลือก **OrderList**
    * ที่ **Function** เลือก **Sum**
    * ที่ **Attribute** เลือก **TotalPrice**
    * จะได้ **Output > Variable** ให้กำหนดชื่อเป็น *SumTotalPrice*

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402724803.png)

16. Click **OK** ควรจะได้เหมือนกับ Microflow ด้านล่างนี้

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402744409.png)

17. เพิ่ม **Change object** activity ให้ทำการกำหนดค่าดังนี้
    * ที่ **Input > Object** เลือก **Customer**
    * เลือก **Commit** เป็น **Yes**
    * **Refresh in client** กำหนดเป็น **Yes**
    * Click **New** เพื่อเปลี่ยนแปลงค่า

18. ทำการปรับ **Change Item** ดังนี้
    * ที่ **Member** ให้เลือก **TotalPrice**
    * ที่ **Value** ใส่ *$SumTotalPrice*
    * Click **OK** เพื่อบันทึกค่า

19. Click **OK** ควรจะได้เหมือนกับ Microflow ด้านล่างนี้

![file](https://km.tbn.co.th/uploads/inline_files/image-1590402756782.png)

20. ทำการ run application แล้วสร้าง customer และ order ควรจะเห็น **TotalOrderAmount** เป็นค่าล่าสุดเสมอ
