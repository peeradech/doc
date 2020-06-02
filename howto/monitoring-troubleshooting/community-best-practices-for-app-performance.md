# เกริ่นนำ

```
เนื้อหาส่วนนี้เป็นส่วนที่ได้รวบรวมมาจาก Mendix community ซึ่งได้ถูกคัดเลือกสิ่งที่ควรทำ หรือไม่ควรทำ เพื่อให้ได้ประสิทธิภาพที่ดีที่สุด
```

สิ่งที่ application คุณควรจะทำมันได้ดีนั้น ผู้ใช้จะเป็นคนบอกว่าจะรับหรือไม่รับ และอาจจะไปเลือกทางอื่น ถ้า Performance ไม่ดี เนื้อหาส่วนนี้จะอธิบายบาง Best Partices ที่จะนำไปใช้ในการพัฒนาเพื่อที่จะได้ app ที่มีประสิทธิภาพให้มากที่สุด

มุมมองของคนดังให้กล่าวไว้ถึงเรื่องการเพิ่มประสิทธิภาพได้กล่าวไว้ คนแรกก็คือ อัลเบิร์ต ไอน์สไตน์ (Albert Einstein) กล่าวว่า *“Everything should be made as simple as possible, but no simpler.”* - *"ทุกสิ่งทุกอย่างควรจะทำอย่างเรียบง่ายที่สุดเท่าที่จะทำได้ ไม่ใช่แค่ง่ายขึ้น"* คนถัดมาคือ programming ผู้ยิ่งใหญ่ คือ โดนัลด์ คนูธ (Donald Knuth) ได้กล่าวไว้ว่า *“Premature optimization is the root of all evil.”* - *การทำ Optimization เป็นหลักจะเป็นจุดเริ่มต้นของความวอดวายทั้งมวล* คำกล่าวของทั้งสองคนแนะนำให้เราหลีกเลี่ยงจากการทำอะไรที่ยาก ๆ เพื่อที่จะได้ Performance ยกเว้นคุณต้องการให้มันมี Performance จริง ๆ และมีทางการในการตรวจสอบปัญหาได้ง่าย คุณอาจจะต้อง load test และใช้เครื่องมือวัดที่ดี แต่จะไม่ใช่ในเอกสารเรื่องนี้

อาจจะมี Best Practices อื่น ๆ ที่อาจจะขัดแย้งกับ Best Practices ของ Performance เช่น เรื่อง Security อาจจะต้องจัดการ และทำประสิทธิภาพที่น้อยลง Architecture ที่ดีอาจจะแลกด้วยคุณภาพ หรือประสิทธิภาพ ซึ่งคุณต้องเลือกมัน

เอกสารส่วนนี้จะเป็นส่วนที่ใช้กับ Mendix Studio Pro เพื่อเพิ่มประสิทธิภาพของ Domain Model (การกำหนด Index) Microflow และ Page ในส่วนอื่น ๆ จะกล่าวถึงประสิทธิภาพของภาษาที่ใช้ใน Mendix ไม่ว่าจะเป็น XPath, expression และ OQL 

# Domain Model Best Practies

ถ้าออกแบบ Domain Model ที่เรียบง่ายก็จะนำมาซึ่ง app ที่มีประสิทธิภาพ มีดังนี้

- สร้าง Index ใน Entities ที่มีข้อมูลมากกว่า 100 records และจะทำการค้นหาโดยใช้ Reference/IDs ตามที่กำหนดไว้
- ใช้ Calculate Attribute ให้น้อยที่สุด เพราะทุกครั้งที่ retrieve ข้อมูลในแต่ละ row จะทำการคำนวณทุกครั้ง ซึ่งบางครั้งเราไม่ได้ต้องการมาแสดง
- ใช้ Event Handler ให้น้อยที่สุด เพราะทุกครั้งที่ event นั้นทำงานก็จะถูกเรียกใช้งานเสมอ (เช่น การ Commit)
- ใช้ Association ที่เป็น Rerference Set ให้น้อยที่สุด เมื่อ Mendix retrieve ID (แต่ละ Row) จะทำการดึงทุก ๆ query ดังนั้น การมีหลาย Reference และ Reference Set ก็จะมีการโหลดข้อมูลจาก Database
- พิจารณาการจัดเก็บข้อมูล ถ้าข้อมูลมีปริมาณมากแล้วไม่ได้ใช้ข้อมูลนั้นทั้งหมดตลอดเวลา ให้ทำการแยก Entities แล้วทำการแยกส่วนที่ใช้งานอยู่ ณ ปัจจุบัน และอีกส่วนจะใช้สำหรับการทำรายงานหรือเป็นข้อมูลที่เก่าแล้ว
- การทำ Denormalizing ข้อมูล ก็คือการเก็บข้อมูลจาก Entities อื่นมาไว้ใน Attribute โดยที่ไม่ต้องทำการ Retrieve ข้อมูลเหล่านั้นทุกครั้ง ถ้าข้อมูลไม่ได้มีการเปลี่ยนแปลงมากจะช่วยให้ลดการ query ลง อย่างไรก็ตามก็ต้องมีวิธีการจัดเก็บ attribute เหล่านั้นให้ตรงกัน
- ไม่ใช้ Inheritance หลายชั้นมากจนเกินไป เพราะการมี Specialization ใน entity เยอะ ก็จะทำให้มีการเก็บข้อมูลที่หลายชั้น โดยเฉพาะอย่างยิ่งเมื่อคุณใช้ XPath บน entity จะทำให้การสร้าง XPath มีความยุ่งยากในแต่ละชั้น และมีหลายชุด จะทำให้การ query ก็จะช้าลงเช่นกัน พิจารณาทางเลือกที่สามารถทำได้ดังนี้
  * สร้าง attribute รวมกันแล้วสร้าง enumeration เพื่อแยกข้อมูลแต่ละประเภท
  * แยก entity โดยใช้ Assoication แบบ 1 to 1 ขึ้นอยู่กับ UI ว่าการใช้แบบ 1 to 1 แบบปกติจะช่วยลดการดึงข้อมูลมาทั้งหมดได้
  * ใช้ Non-Persistance ด้วยมีการ Inheritance ในการทำข้อมูลให้ตรงตาม Business Logic
- ไม่ใช้ Association ชั่วคราวกับการเก็บข้อมูล Persistance entities ให้ใช้ Non-Persistance entity สำหรับหน้า หรือ UI แทน

# Index Best Practices

Indexes เป็นหัวข้อที่พูดถึงกันมายาวนานในโลกของ Database สำหรับ Mendix apps แล้วสามารถปรับการใช้งานได้ดังนี้

- สร้าง Indexs ตามที่ต้องการ แม้ว่าทุก ๆ Index จะใช้เวลาในการ `insert`, `update` และ `delete` นานขึ้น แต่มันจะช่วยให้ query เร็วขึ้นมาก
- เนื่องจาก Mendix retrieve มาเป็น Object ข้อมูลทุก ๆ columns จำเป็นต้องมี index ด้วยเหตุผล 2 ประการ อย่างแรกคือต้องได้ Mendix ID อย่างรวดเร็ว เหตุผลที่ 2 ก็คือการเรียงข้อมูล
- คุณต้องสร้าง Index ให้ครอบคลุมทั้ง Search และ Sort ให้อยู่ภายใน Index เดียวกัน
- อย่าสร้าง Index ที่เริ่มด้วย Attribute เดียวกัน
- ตามกฎโดยทั่วไปพยายามกำหนด Index ให้จำนวน columns ให้น้อยที่สุด เป็นไปได้ควรมีแค่ 3 แต่มากสุดไม่เกิน 5
- เริ่มด้วย Attribute ที่มีการเลือกใช้งานมากที่สุด ถึงแม้ว่าคุณต้องการ search เพียงแค่ attribute เดียว ก็สามารถใช้ indexes นั้นได้
- Indexes ใช้ได้ดีกับ search แบบ equal, แบบ ranges ที่เป็น dates และ numbers, และ `startWith` สำหรับ search กับ strings
- Index จะทำงานยากหน่อยในกรณีที่ใช้เป็นแบบ `contains` หรือ `endWith` แต่ก็ยังเร็วกว่าการค้นหาตั้ง table

# Microflow Best Practices

- ลดการทำงาน loop ให้น้อยที่สุด ดังนี้
  * ให้ทำการ commit หลังจากจบ loop
  * ถ้าต้องการทำการ commit ให้สร้าง list ชื่อ `<Entity>_CommitList` ไว้ด้านหน้า แล้วเก็บใส่ list แล้วจึง commit
  * สำหรับการ retrieves ใน loop ให้ทำการ retrieve ทั้งหมดไว้หน้า loop แล้วใช้ list operation find ด้านใน loop  *(ใช้ในกรณีที่ข้อมูลมีไม่เยอะมาก เช่น master data ต่าง ๆ)*
  * ถ้ามี decision ใน loop ลองดูว่าสามารถใช้การ query แทนได้ไหม ก็จะทำให้ลดจำนวน loop ลงได้
- พยายามหลีกเลี่ยงการ retrieve object หรือ list ถ้าเป็นไปได้ให้ใช้การส่ง parameter แทน
- เข้าใจและใช้การ retrieve + aggregate ที่มีประสิทธิภาพ ถ้าคุณ retrieve และทำการ count list Mendix จะทำการ optimize query ให้เพียง query เดียว ถ้าคุณต้องการ list หลังจาก microflow หลังจาก decision ก็ควร retrieve อีกครั้งถ้าเท่าที่จำเป็น นอกจากนี้ยังใช้ในกรณีที่เป็น batch ก็ให้ retrieve มาเป็นช่วง ๆ (offset)
- ถ้าเป็นไปได้ให้ใช้การ retrieve over associate ซึ่งจะใช้ข้อมูลจาก caching ถ้า business logic ต้องการดึงข้อมูลจาก database (เพราะค่าที่อยู่ใน over associate อาจจะมีเปลี่ยนไป) ซึ่งแน่นอนว่าต้อง retrieve จาก database
- ถ้าเป็นไปได้ให้ Commit ท้ายสุดท้าย การ commit นั้นจะทำการ lock record เหล่านั้น ซึ่งหมายถึงว่า user อื่นที่ต้องการ commit ต้องรอจนกว่าการ commit transaction ก่อนหน้าจะทำงานเสร็จ
- ให้ทำการแยกการ commit เป็นชุด ชุดละเล็ก ๆ เพื่อป้องกันการ lock ที่ใช้เวลานานจนนานเกินไป

# Page Best Practices

- ทำให้ง่ายเข้าไว้ ถ้าเป็นไปได้ให้แยกเป็นหลาย ๆ หน้า คิดซะว่าเป็น mobile app และไม่รวมทุกอย่างไว้ในหน้าเดียวกัน
- ลดการส่งข้อมูล ตัวอย่างเช่น ชุดของข้อมูลหรือเรื่องความปลอดภัยของข้อมูลที่ถูกส่งไปยัง client
- พยายามไม่เลือก data source หลาย ๆ แบบ เนื่องจากจะโหลดข้อมูลหลาย ๆ ครั้ง
- ใช้ visiblity ให้น้อยที่สุด
- ให้ user แจ้งหน้าที่ใช้เวลานาน ๆ ใช้ progress bar เข้ามาช่วย
- ใช้ asynchronous ถ้า user ไม่ต้องรอผลการทำงาน ตัวอย่าง เช่น การส่ง email หรือ การเปลี่ยนแปลงข้อมูลของ app อื่น ๆ ซึ่งไม่ควรจะให้ user รอ สำหรับการทำงานแบบ asynchronous ก็มีทางเลือกโดยการใช้ [Community Commons Function Library](https://docs.mendix.com/appstore/modules/community-commons-function-library) ที่อยู่ใน **Mendix App Store** ก็สามารถใช้งาน `executeMicroflowInBackground` หรือใช้ในส่วนของ Process Queue เพื่อช่วยลดโหลดการทำงานของ Background Process

# Infrastructure Best Practices

- ใช้ infrastructure ที่ดีที่สุด
- เมื่อใช้แบบ on-premise ควรจะมี proxy web server ไว้ด้านหน้าของ Mendix เพื่อที่จะไว้สำหรับ static content และการ compress data


# XPath Best Practices

- หลีกเลี่ยงการใช้ “unquals“ และ “not“ ใน XPath บ่อยครั้งที่เราสามารถเขียน statement แบบนี้แทนได้ เช่น `<boolean>=false()`, `<enumeration> = valueA`, `<enumeration> = valueB`, `integer>valueA`, หรือ `integer<valueB`
- รวบ path เป็นอันเดียวกันถ้ามันทำได้
- เช็คให้แน่ใจว่ามีการเพิ่ม attributes ไว้ที่ index แล้ว

# OQL Best Practices
สำหรับ OQL ในหลาย ๆ อย่างสามารถใช้ตาม XPath ได้เลย
- สังเกตุว่า OQL นั้นจะไม่สนใจ security ของ domain model ดังนั้นควรจะมีวิธีจัดการส่วนนี้ด้วย

# Security Best Practices

- แต่ละ user ควรมี role ให้น้อยที่สุด
- มี role ในแต่ละ entity ให้น้อยที่สุด

# Web Services & XML Best Practices

- ใช้ SHA256 แทน BCrypt
- การ validate schema ทำให้การทำงานช้าลง
- การใช้ sub-transaction สำหรับ microflow ทำให้ทำงานช้าลง
