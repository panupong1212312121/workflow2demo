
# 🤖 n8n Setup Guide

คู่มือนี้จะอธิบายขั้นตอนการรัน n8n ด้วย Docker Desktop และการเชื่อมต่อ Webhook สำหรับ LINE Messaging API ด้วย ngrok เพื่อให้สามารถพัฒนาบนเครื่อง Local ได้

## 1\. การรัน n8n ผ่าน Docker Desktop (Local Host)

ขั้นตอนนี้เป็นการสร้างและรัน n8n Workflow Automation Tool ในสภาพแวดล้อมที่แยกจากกัน (Isolated Environment) ด้วย Docker

### 1.1 การเตรียมไฟล์และ Build Image

1.  **ติดตั้ง Docker Desktop:** ดาวน์โหลดและติดตั้ง [Docker Desktop](https://www.docker.com/products/docker-desktop) บนเครื่องของคุณ

2.  **เตรียมไฟล์:** สร้าง Folder สำหรับโปรเจกต์ของคุณ จากนั้นคัดลอกไฟล์ที่ได้รับ (**`Dockerfile`** และ **`docker-compose.yml`**) ไปไว้ใน Folder นั้น

3.  **Build Image:** เปิด Command Prompt (CMD) หรือ Terminal ใน Folder ที่เก็บไฟล์ Docker แล้วรันคำสั่งเพื่อสร้าง Docker Image:

    ```bash
    docker-compose build
    ```

### 1.2 การรัน Container

1.  **เปิด Docker Desktop:** เปิดแอปพลิเคชัน Docker Desktop ขึ้นมา

2.  **ค้นหา Image:** ไปที่แท็บ **Images** และค้นหา Image ที่สร้างขึ้น (ชื่อ Image มักจะตรงกับชื่อ Folder ที่ครอบไฟล์ Docker)

3.  **ตั้งค่าและรัน:** กดปุ่ม **Run** เพื่อสร้าง Container จาก Image นั้น พร้อมตั้งค่าพอร์ตให้เป็นไปตามที่กำหนด (โดยปกติ Docker Compose จะจัดการพอร์ตให้แล้ว แต่ควรตรวจสอบว่าพอร์ต **`5678`** ถูก Mapping เรียบร้อยแล้ว)

    ![docker_container_setting](C:\Users\Panupong Jindarat\Desktop\Project\Present\N8N\asset\docker-container-n8n-setting.png)

4.  **เข้าใช้งาน n8n:** เมื่อ Container รันสำเร็จ เข้าใช้งาน n8n ผ่าน Web Browser:

    ```
    http://localhost:5678
    ```

5.  **Setup/Login:** ทำการตั้งค่าผู้ใช้ (กรณีเข้าครั้งแรก) หรือ Login เข้าใช้งาน (กรณีเข้าครั้งต่อ ๆ ไป)

-----

## 2\. วิธีใช้ ngrok เพื่อเชื่อมต่อ Webhook กับ LINE

ngrok ทำหน้าที่เป็นสะพานเชื่อมต่อระหว่าง Public Internet (LINE Server) กับ n8n ที่รันอยู่บน Local Host ของคุณ

1.  **ดาวน์โหลด ngrok:** ไปที่ [ngrok download](https://dashboard.ngrok.com/get-started/setup/windows) เพื่อดาวน์โหลดไฟล์

2.  **แตกไฟล์:** แตกไฟล์ ZIP ที่ดาวน์โหลดมา

3.  **รัน ngrok:** Double-click เปิด **`ngrok.exe`**

4.  **เปิด Tunnel:** รันคำสั่งเพื่อเปิด Public Tunnel เชื่อมต่อกับพอร์ตของ n8n (พอร์ต 5678):

    ```bash
    ngrok http 5678
    ```

5.  **คัดลอก Public URL:** ngrok จะแสดง URL สาธารณะที่ใช้งานได้ (Forwarding URL) ซึ่งขึ้นต้นด้วย `https://` **คัดลอก URL นี้ไว้**

    ![ngrok_terminal_URL](C:\Users\Panupong Jindarat\Desktop\Project\Present\N8N\asset\ngrok-terminal-url.png)

-----

## 3\. ตั้งค่าใน LINE Developers Console

นำ Public URL ที่ได้จาก ngrok ไปตั้งค่าใน LINE Channel เพื่อรับ Webhook Event

1.  **ไปที่ Console:** เข้าสู่ระบบ **LINE Developers Console** และเลือก Channel ของ LINE Official Account ที่คุณต้องการ

2.  **ตั้งค่า Webhook URL:** ไปที่แท็บ **Messaging API**

3.  **วาง URL:** ในช่อง **Webhook URL** ให้วาง Public URL ที่คัดลอกมาจาก ngrok และ **ต้องต่อท้ายด้วย Path ของ Webhook Endpoint** ของ n8n (ซึ่งโดยทั่วไปคือ `/webhook`)

    *ตัวอย่าง URL ที่สมบูรณ์:*

    ```
    https://<random-id>.ngrok-free.app/webhook/<trigger-webhook-node-n8n-id>
    ```

4.  **กด Verify:** กดปุ่ม **Verify** เพื่อให้ LINE ตรวจสอบว่าสามารถเข้าถึง URL นี้ได้ (สถานะควรเป็น "Success")

5.  **เปิดใช้งาน Webhook:** เลื่อนลงไปที่ส่วน **Webhook Settings** และเปิดใช้งาน **Use Webhook** (ตั้งค่าเป็น On)

### 🚨 ข้อควรจำ (สำคัญมาก)

> **URL ของ ngrok (ฟรี) จะเปลี่ยนทุกครั้ง** ที่คุณปิดและเปิด Tunnel ใหม่ ดังนั้น หากคุณปิด Terminal ของ ngrok ไปแล้ว **คุณต้องทำซ้ำขั้นตอนที่ 2 และ 3 ใหม่** ทุกครั้งเพื่ออัปเดต URL ใน LINE Developers Console ให้ถูกต้อง มิฉะนั้น Webhook จะไม่ทำงาน