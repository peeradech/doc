ในบทความนี้จะเป็นการนำ *[Custom Runtime Settings](https://docs.mendix.com/refguide/custom-settings)* ที่มีการใช้งานกันเป็นประจำ ไม่ได้ยกทุก Option มาอธิบาย แต่จะอ้างอิงจาก Document ของ Mendix และจะเพิ่มเติมส่วนที่นำไปใช้งานจริงในส่วนของ **Mendix Studio Pro**, **Mendix Service Console** และ **m2ee-tools**

# General Settings
| Name | Description | Default Value |
| --- | --- | --- |
| ApplicationRootUrl     | กำหนด Domain ของ Application โดยปกติแล้วค่านี้จะถูกนำไปใช้งานโดยเรียกผ่าน Java แนะนำให้เซ็ตไว้ ถ้าใน Studio Pro จะสามารถดูได้ที่ *Setting -> Server (tab)* จะมีค่าเริ่มต้นให้เป็น `http://localhost:8080`      |      |
| ScheduledEventExecution | เป็นการกำหนดการทำงานของ Scheduled มี 3 แบบ คือ *‘ALL’, ‘NONE’ or ‘SPECIFIED’* ในกรณีที่กำหนดเป็น `SPECIFIED` จะดูรายการ Scheduled ที่อยู่ใน `MyScheduledEvents`  | NONE |
| MyScheduledEvents | แต่ละ Schedule จะคั่นด้วย `,` ซึ่งเราสามารถกำหนดโดยเริ่มจาก `Module.MyScheduled` |  |
| PersistentSessions | กำหนดว่า session ที่ใช้งานจะถูกเก็บไว้ใน database หรือไม่ เมื่อ session ถูกเก็บลง database จะสามารถดู user ที่ใช้งานอยู่ได้ (Active Session), เมื่อ server restart session จะยังคงอยู่โดยที่ user ไม่ต้อง sign in ใหม่ (ในกรณีที่เป็น clustered environment)  | true |
| CACertificates | กำหนด path ของ Authority Certificates (CA) ที่ใช้ใน application ซึ่งจะคั่นด้วย `,` | |
| SessionTimeout | กำหนดระยะเวลาในการที่ session อยู่ในระบบเมื่อไม่ได้ใช้หยุดการใช้งาน (milliseconds) | 600000</br>(10 นาที) |
| ClusterManagerActionInterval | กำหนดรอบการทำงานของ Cluster Manager ซึ่งจะทำงานเกี่ยวกับการ Unlooking users และการลบ Session ที่ไม่ได้ใช้งาน ถ้าไม่ได้มีการหนดค่าจะเป็นครึ่งหนึ่งของ SessionTimeout | 300000</br>(5 นาที) |
| com.mendix.core.StorageService | กำหนดที่เก็บของไฟล์ ซึ่งสามารถกำหนดค่าที่เป็นไปได้ดังนี้ `com.mendix.storage.localfilesystem`, `com.mendix.storage.s3`, `com.mendix.storage.azure`, and `com.mendix.storage.swift` | com.mendix.storage.localfilesystem |

# Log File Settings
| Name | Description | Default Value |
| --- | --- | --- |
| LogFileName | ชื่อ log ไฟล์ที่จัดเก็บ | Application.log |
| MaxLogFileSize | ขนาดของ log ไฟล์ เมื่อถึงขนาดที่กำหนดก็จะสร้างไฟล์ใหม่ (ถ้ามีขนาดใหญ่มาอาจจะทำให้ editor บางตัวเปิดไม่ได้) | 2097152 (2 MB) |
| MaxLogFileCount | จำนวน log ไฟล์ที่เก็บมากสุด | 10 |

# Database Settings
## Common Settings
| Name | Description | Default Value |
| --- | --- | --- |
| DatabaseType | กำหนด database engine สำหรับการเชื่อมต่อ มีค่าที่สามารถใช้งานได้ ดังนี้ <ul><li>DB2</li><li>HSQLDB</li><li>MYSQL</li><li>ORACLE</li><li>POSTGRESQL</li><li>SAPHANA</li><li>SQLSERVER</li></ul> |  |
| DatabaseHost | ชื่อหรือ IP ของ database ตามด้วย port คั่นด้วย `:` เช่น  `10.0.0.5:1433` สามารถรองรับ **IPv6** จะอยู่ภายใน `[]` เช่น `[::1]:5432` |  |
| DatabaseName | ชื่อ database หรือ schema ที่ใช้งาน |  |
| DatabaseUserName | Username สำหรับการเชื่อมต่อ |  |
| DatabasePassword | Password สำหรับการเชื่อมต่อ |  |

## Connection Pooling
การตั้งค่าด้านล่างนี้เป็นการกำหนด database connection pooling ซึ่ง Mendix Runtime ใช้การ reuse database connections สามารถดูตัวอย่างการใช้งานได้ที่ [Apache Commons Object-poooling API]([https://link](http://commons.apache.org/pool/))
| Name | Description | Default Value |
| --- | --- | --- |
| ConnectionPoolingMaxActive | กำหนดจำนวน `active` ใน pool | 50 |
| ConnectionPoolingMaxIdle | กำหนดจำนวน `idle` ใน pool | 50 |


# S3 Storage Service Settings
เป็นการกำหนดค่าสำหรับการเชื่อมต่อกับ Amazon S3 Storage Service
| Name | Description | Default Value |
| --- | --- | --- |
| com.mendix.storage.s3.AccessKeyId | ค่าที่ได้จาก AWS |  |
| com.mendix.storage.s3.SecretAccessKey | ค่าที่ได้จาก AWS |  |
| com.mendix.storage.s3.BucketName | ชื่อ bucket สำหรับเก็บไฟล์ |  |

# Migration Settings
การตั้งค่านี้จะใช้ในการย้าย database ทั้งหมดไปยัง database ใหม่โดยที่ database หลักต้องไม่มีตาราง ระหว่างที่ Start-up ข้อมูลจะถูก copied ไป
| Name | Description | Default Value |
| --- | --- | --- |
| SourceBuiltInDatabasePath | กำหนด path ของ database (build-in) | [deployment folder]/data/database |
| SourceDatabaseType | ประเภทของ database ต้นทาง ซึ่งมีค่า ดังนี้ <ul><li>HSQLDB</li><li>MYSQL</li><li>ORACLE</li><li>POSTGRESQL</li><li>SQLSERVER</li></ul> |  |
| SourceDatabaseHost | ชื่อหรือ IP ของ database ตามด้วย port คั่นด้วย `:` เช่น  `10.0.0.5:1433` สามารถรองรับ **IPv6** จะอยู่ภายใน `[]` เช่น `[::1]:5432` |  |
| SourceDatabaseName | ชื่อ database หรือ schema ที่ใช้งาน |  |
| SourceDatabaseUserName | Username สำหรับการเชื่อมต่อ |  |
| SourceDatabasePassword | Username สำหรับการเชื่อมต่อ |  |

# Web Client Settings
การตั้งค่านี้จะมีผลกับ Mendix web client
| Name | Description | Default Value |
| --- | --- | --- |
| EnableKeepAlive | เป็นการกำหนดเพื่อให้ client ส่งไปหา server เพื่อให้ session ไม่หมดอายุ โดยจะส่งไปทุก ๆ ครึ่งหนึ่งของ `SessionTimeout` ถ้าปิดการทำงาน (`false`) ก็จะออกจากระบบโดยอัตโนมัติตามการตั้งค่าของ `SessionTimeout` แม้จะเปิด browser ไว้ | true |
