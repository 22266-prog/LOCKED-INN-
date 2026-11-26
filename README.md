# LOCKED IN

เว็บแอปสร้างวินัยและติดตามนิสัยประจำวัน ไฟล์เดียว: `locked-in.html` (HTML + CSS + JavaScript ล้วน ไม่ต้อง build)

## วิธีเปิดใช้งาน
- ดับเบิลคลิก `locked-in.html` เพื่อเปิดในเบราว์เซอร์ (ต้องมีอินเทอร์เน็ตสำหรับฟอนต์ Prompt และไอคอน Lucide ที่โหลดจาก CDN)
- หรือเปิดผ่านเซิร์ฟเวอร์ภายในเครื่อง เช่น `npx serve .` หรือ `python3 -m http.server`
- ข้อมูลบัญชีและกิจกรรมเก็บใน LocalStorage ของเบราว์เซอร์ จึงแยกกันระหว่างลิงก์ที่เผยแพร่กับไฟล์ที่เปิดจากเครื่อง

## โครงสร้างในไฟล์ (ส่วน `<script>`)
| ส่วน | หน้าที่ |
|---|---|
| `L` | พจนานุกรมแปลภาษา TH/EN (`t('key')`) |
| `PRE`, `CN` | กิจกรรมสำเร็จรูป 39 รายการ และหมวดหมู่ |
| `S` / `save()` | ข้อมูลของผู้ใช้ที่ล็อกอิน (กิจกรรม `acts`, เช็กอิน `chk`, ตั้งค่า `set`) |
| `U()`, `hashPw()`, `getSess()`, `loadUser()`, `doAuth()` | ระบบสมัคร/เข้าสู่ระบบ/Session |
| `dayN()`, `streak()`, `planned()` | แกนคำนวณสถิติ ทุกตัวเลขมาจากประวัติเช็กอิน |
| `pages.dash/acts/chk/prog/set` | แต่ละหน้า |
| `renderApp()`, `renderAuth()`, `render()` | ตัวแสดงผลหลัก |
| click/change handler ท้ายไฟล์ | จัดการปุ่มด้วย `data-a` / `data-v` |

คีย์ LocalStorage: `lockedin.users` (บัญชี), `lockedin.sess` (session), `lockedin.data.<userId>` (ข้อมูลรายบัญชี), `lockedin.lang` (ภาษา)

## ข้อจำกัดของระบบบัญชีปัจจุบัน
บัญชีอยู่ในเบราว์เซอร์เครื่องเดียว ไม่ซิงก์ข้ามอุปกรณ์ และกู้รหัสผ่านทางอีเมลไม่ได้ รหัสผ่านเก็บเป็นแฮช PBKDF2 พร้อม salt แต่ไม่ปลอดภัยเทียบเท่าระบบ Backend จริง

## ย้ายไปใช้ Backend จริง (Supabase)
1. สร้างโปรเจกต์ Supabase แล้วเปิด Email Auth ตั้งเทมเพลตอีเมลรีเซ็ตรหัสผ่าน
2. สร้างตาราง `activities` (id, user_id, name, category, icon, target_per_day, created_at, is_active) และ `checkins` (id, user_id, activity_id, checkin_date, checkin_time)
3. เปิด Row Level Security ให้เห็นเฉพาะแถวที่ `user_id = auth.uid()`
4. ใช้เฉพาะ anon key ในหน้าเว็บ ห้ามใส่ service key
5. แทนที่ `doAuth()`/`loadUser()`/`save()` ด้วยการเรียก Supabase ส่วนการคำนวณสถิติ (`dayN`, `streak`) และหน้า `pages.*` ใช้ต่อได้เลย
6. โฮสต์ไฟล์บนโดเมนของคุณ (เช่น Vercel, Netlify) เพราะหน้าที่เผยแพร่ใน Claude เรียกเครือข่ายภายนอกไม่ได้
