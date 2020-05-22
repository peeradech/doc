เราสามารถ debug application ที่ทำงานอยู่ Server ได้ ไม่ว่าจะเป็น On-premise, Docker และ Cloud ซึ่งเราสามารถ connect จาก Mendix Project ที่เรา deploy ไป

ซึ่งเราจะทำแสดงให้เห็นถึงวิธีการ remote debug ทั้งสองแบบ คือ On-Premise (Windwos/Linux) และบน Docker

# สิ่งที่ต้องเตรียม
Server สำหรับ run Mendix ทั้งบน [Linux](https://github.com/mendix/m2ee-tools) และ [Windows](https://docs.mendix.com/developerportal/deploy/deploy-mendix-on-microsoft-windows) หรือถ้าเป็น docker ก็สามารถเตรียมการ deploy ได้

# พื้นฐาน

- การ debug นั้นสามารถทำให้บน single-instance ถ้าต้องการทำบน multi-instance อาจจะต้องลดให้เหลือเครื่องเดียว หรือถ้าสามารถเรียกตรงไปยัง instance ที่ต้องการได้ก็สามารถทำได้เช่นกัน
- เราไม่แนะนำให้ deubgging บน production ถ้าจะต้อง debug ควรจะใส่ condition ใน breakpoint จุดที่ต้องการ debug (User)

## การเปิด Debugging บน Linux, Windows และ Docker
### Linux (m2ee-tools)
1. เข้าไปไปยัง m2ee-tools console
2. Run command `enable_debugger` แล้วกด `Enter`
3. คุณสามารถสามารถกำหนด **Password** เอง หรือถ้าต้องการให้ระบบ **auto-generate** ให้ก็สามารถกด `Enter` ได้ทันที

### Windows (Mendix Service Console)
1. เปิด **Mendix Service Console** เพื่อทำการ enable debugging
2. ไปยังเมนู *Advance > Enable Debugging*
3. ระบบจะแสดง Password สำหรับนำไปใช้งาน

### Docker
คุณสามารถกำหนด Password สำหรับการ Debugging ด้วยการใส่ Environment variable ที่ชื่อ [`DEBUGGER_PASSWORD`](https://github.com/mendix/cf-mendix-buildpack#enabling-the-mendix-debugger)

## ทำการ Debugging บน Mendix ได้อย่างไร
หลังจากที่คุณได้ Password มาแล้วสามารถทำการ Debugging ได้ดังนี้
1. ไปที่ **Run** หลังจากนั้นเลือก *Debug > Connect Debugger...*
2. จะแสดง popup `Connect Debugger` ให้เลือก Connect to เป็น **An app running in the Mendix Cloud or on another remote server.** ใส่ URL *{%your_domain_name%}/debugger/* และ *Password*
3. ใน Mendix จะ disabled breakpoint ทั้งหมดออก ให้ทำการ enabled breakpoint ที่ต้องการ debugging
