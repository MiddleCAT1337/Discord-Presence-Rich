# Discord Rich Presence (Gateway)

สคริปต์ Node.js สำหรับตั้งค่า Rich Presence บนโปรไฟล์ Discord ผ่าน Gateway WebSocket โดยอ่านค่าจากไฟล์ config.json แทนการเปิดเกมจริง เหมาะกับคนที่อยากให้โปรไฟล์แสดงข้อความ รูป และปุ่มลิงก์ตามที่กำหนดเอง

## ฟีเจอร์

- เชื่อมต่อ Discord Gateway แล้วส่ง Presence อัตโนมัติ
- ตั้งข้อความ details และ state ได้หลายชุดใน config (อ้างอิงจากรายการ presences)
- ใส่รูป large_image และ small_image จาก Discord Developer Portal (ใช้ชื่อ asset หรือเลข snowflake)
- ใส่ปุ่มลิงก์ได้สูงสุด 2 ปุ่ม (ระดับทั้งแอปหรือแยกต่อแต่ละ presence)
- จำกัดอัตราการส่ง presence ให้สอดคล้องกับขีดจำกัดของ Gateway (ประมาณ 5 ครั้งต่อ 20 วินาที)
- กด Ctrl+C แล้วสคริปต์จะล้าง presence ก่อนปิด

## เทคโนโลยีที่ใช้

- ภาษา: JavaScript (รันแบบ ES Module)
- รัน time: Node.js
- ไลบรารี: ws (WebSocket client)
- โปรโตคอล: Discord Gateway API v10

## สิ่งที่ต้องมีก่อนติดตั้ง

- Node.js เวอร์ชัน 18 ขึ้นไป (แนะนำ LTS) เช็กด้วยคำสั่ง node -v
- บัญชี Discord
- แอปพลิเคชันใน Discord Developer Portal (ได้ applicationId)
- User token ของบัญชีที่จะใช้แสดง presence (เก็บเป็นความลับ ห้ามอัปโหลดขึ้น GitHub)

หมายเหตุด้านนโยบาย Discord: การใช้ user token กับ client ที่ไม่ใช่แอป Discord อย่างเป็นทางการอาจขัด Terms of Service ของ Discord ใช้ด้วยความรับผิดชอบและความเสี่ยงของตัวเอง

## วิธีติดตั้ง

1. โหลดโค้ดมาไว้ในเครื่อง (clone หรือแตก zip ก็ได้)

2. เปิดเทอร์มินัลแล้วเข้าโฟลเดอร์โปรเจกต์

```bash
cd Discord-Presence-Rich-main
```

3. ติดตั้ง dependency

```bash
npm install
```

4. คัดลอกไฟล์ตัวอย่าง config เป็นของจริง

Windows (PowerShell):

```powershell
Copy-Item config.example.json config.json
```

macOS / Linux:

```bash
cp config.example.json config.json
```

5. แก้ไฟล์ config.json ใส่ token, applicationId, applicationName และรายการ presences ตามขั้นตอนในหัวข้อวิธีใช้งานด้านล่าง

6. อย่า commit ไฟล์ config.json ขึ้น Git (มี token อยู่)

## วิธีใช้งาน

### ขั้นที่ 1: สร้างแอปใน Discord Developer Portal

1. เปิดเว็บ Discord Developer Portal แล้วล็อกอิน
2. กด New Application ตั้งชื่อแอป (ชื่อนี้จะไปแสดงบน Rich Presence)
3. คัดลอก Application ID ไปใส่ใน config.json ที่ฟิลด์ applicationId
4. ใส่ applicationName ให้ตรงหรือใกล้เคียงชื่อแอป (สคริปต์อาจดึงชื่อจาก Portal มาใช้ถ้าไม่ตรง)
5. ไปที่ Rich Presence แล้วอัปโหลด Art Assets ถ้าต้องการรูป (จดชื่อ asset เช่น logo ไว้ใส่ใน large_image)

### ขั้นที่ 2: เตรียม token ใน config.json

1. เปิด config.json ด้วย editor
2. แทนที่ YOUR_USER_TOKEN_HERE ด้วย user token ของบัญชีที่จะใช้
3. เก็บ token เป็นความลับ อย่าแชร์หรืออัปโหลดไฟล์นี้

### ขั้นที่ 3: ตั้งค่า presence และช่วงเวลา

ตัวอย่างโครงสร้างสำคัญใน config.json

- token: user token
- applicationId: เลขแอปจาก Portal
- applicationName: ชื่อที่แสดงบน activity
- intervalMs: ช่วงเวลาเป็นมิลลิวินาที ต้องไม่ต่ำกว่า 4000 แนะนำ 5000 ขึ้นไป
- presenceType: ประเภท activity (0 Playing, 1 Streaming, 2 Listening, 3 Watching, 5 Competing)
- buttons: ปุ่มสูงสุด 2 ปุ่ม แต่ละปุ่มมี label และ url
- presences: อาร์เรย์ของข้อความและรูปแต่ละชุด (details, state, assets, buttons ต่อชุดได้)

### ขั้นที่ 4: รันสคริปต์

ในโฟลเดอร์โปรเจกต์รัน

```bash
npm start
```

หรือ

```bash
node index.js
```

ถ้าตั้งค่าถูกต้อง ในเทอร์มินัลจะเห็นประมาณนี้

- Connected to Discord gateway.
- Logged in as ชื่อผู้ใช้
- บรรทัด [presence] ตาม details และ state ที่ตั้งไว้
- [presence] confirmed on gateway session เมื่อ Gateway ยืนยันแล้ว

### ขั้นที่ 5: ดูผลบน Discord

1. ปิด Discord แบบ Desktop และ Web ก่อนรันสคริปต์ (ถ้าเปิดค้างอยู่ โปรไฟล์อาจโชว์ activity จาก client อื่นแทน)
2. เปิดดูโปรไฟล์จากบัญชีอื่นหรือเพื่อน (ปุ่ม Rich Presence มักไม่กดได้บนโปรไฟล์ตัวเอง)
3. ควรเห็นชื่อแอป ข้อความ รูป และปุ่มตาม config

### ขั้นที่ 6: หยุดและล้าง presence

1. กลับไปที่หน้าต่างเทอร์มินัลที่รันสคริปต์
2. กด Ctrl+C
3. รอข้อความ Clearing presence... แล้วโปรแกรมจะปิดเอง

### แก้ปัญหาเบื้องต้น

- config.json not found: ยังไม่ได้ copy จาก config.example.json
- Set a valid token / applicationId: ยังเป็นค่ placeholder อยู่
- intervalMs must be >= 4000: เพิ่มค่า intervalMs
- unknown asset key: ชื่อรูปใน assets ไม่ตรงกับใน Developer Portal
- โปรไฟล์ไม่เปลี่ยน: ปิด Discord client อื่น แล้วรันสคริปต์ใหม่

## โครงสร้างโฟลเดอร์

```
Discord-Presence-Rich-main
  index.js              โค้ดหลัก เชื่อม Gateway และส่ง presence
  config.example.json   ตัวอย่าง config (เอาไป copy เป็น config.json)
  config.json           ไฟล์ตั้งค่าจริง (สร้างเอง ไม่ควร commit)
  package.json          ชื่อโปรเจกต์และคำสั่ง npm start
  package-lock.json     lock ไฟล์ของ npm
  README.md             เอกสารนี้
```

## ผู้จัดทำ

- (ใส่ชื่อและรหัสนักศึกษาหรือ GitHub ของคุณที่นี่)

## ไลเซนส์

- (ใส่ไลเซนส์ถ้าจะเปิดแชร์โค้ด เช่น MIT หรือข้อความงานส่งอาจารย์)
