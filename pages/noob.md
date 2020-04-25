---
layout: page
title: Noob Framework V 1.0
description: famework สำหรับ RESTful API โดยคำไทย 100%
permalink: /noobjs/
tags: [ระบบจัดการสต๊อค, ระบบจัดการสินค้า, ระบบจัดการออร์เดอร์, ระบบจัดการสต๊อคแบบเรีบลไทม์, ไม่จำกัดจำนวนผู้ใช้,node.js, Angular,javascript, MongoDB,noob, stock, noobstock]
---
# noob.js
noob ถูกออกแบบ และพัฒนาโดย Noob Studio โปรแกรมเมอร์ฟรีแลนซ์ ถูกออกแบบมาให้ใช้งานง่าย มีขนาดเล็ก และมีแพคเกจต่างๆ ที่จำเป็นสำหรับเริ่มการทำ RESTful API ด้วย Node.js ช่วยให้คุณพัฒนา API ได้เร็วขึ้น แบบติดจรวด!!!

## Install

คุณสามารถติดตั้งใช้งาน noob ได้ในง่ายๆ ดังนี้
```
git clone https://github.com/noob-studio/noob-framework.git awesome-project
cd awesome-project
npm install
noob start
```
> noob ต้องการ Node >= v7.6.0 และ MySQL สำหรับใช้งาน

หลังจากนั้นลองเข้าไปที่หน้า /example/alive จะขึ้นดังนี้
```json
{
    "code": 200,
    "status": "ok",
    "message": "success",
    "data": "noob worker /example/alive still alive! up time 00:00:02"
}
```
เป็นอันเสร็จเรียบร้อย

## Concept
### Idea
`noob.js` พยายามให้ออกแบบให้สามารถใช้งานได้ง่ายที่สุด และสามารถทำ RESTful API ได้เร็วที่สุดเพราะฉะนั้นในส่วนของงานซ้ำซากเช่น `CRUD` แต่ละ Table นั้น Noob Framework จะทำให้อัตโนมัติเพราะฉะนั้นถึงเรียกว่า `noob.js` เพราะมันง่ายมากนั่นเอง

### Structure
```js
awesome-project
├── app
|   ├── controller // สำหรับ extend route จาก route เดิม
|   └── model // model จ้า
├── config
|   ├── constants.config.json // สำหรับตั้งค่าทั่วไป
|   ├── db.config.json // ตั้งค่าการเชื่อมต่อฐานข้อมูล
|   ├── response.config.json // ตั้งค่า response code
|   └── route.config.json // สำหรับตั้งค่า route ไปหน้าต่างๆ 
├── core // core noob
├── package.json
├── server.js
├── README.md
└── index.html
```

## Build API in 5 Minute
ในส่วนนี้จะพูดถึงการทำ API ด้วย noob ใน 5 นาที

### Setting Route
ให้เราเปิดไฟล์ `route.config.json` ขึ้นมาโดยจะพบโครงสร้างหลักของ route ประมาณนี้

#### Simple Route

```json
[
...
    {
        "path": "/example", // path ของ route นี้
    }
...
]

```

#### Connect database with automatic CRUD
ต่อมาทำการต่อฐานข้อมูลโดยให้เพิ่ม property database เข้าไปใน route ดังนี้
```json
{
    "path": "/example",
    "database": {
        "table": "example", // ชื่อตารางหลักของ route สำหรับทำ CRUD
        "primary_key": "example_id" // id ตารางหลักของ route สำหรับทำ CRUD
    },
}
```
เพียงแค่นี้เราก็ทำ CRUD สำหรับตาราง example เสร็จแล้วจ้า โดยสามารถทำ CRUD ได้โดยใช้ Method ดังนี้
- GET `/` สำหรับดึงข้อมูลจากตารางโดยสามารถส่ง column ที่ต้องการคิวรี่มาเป็น query string ได้เลยเช่น `localhost:3000/example?example_id=1`

- POST `/` สำหรับเพิ่มข้อมูลในตารางโดยชื่อใน body ที่ถูกส่งมาต้องตรงกับชื่อ column

- PATCH `/:id` สำหรับแก้ไขข้อมูลในตารางโดย id คือ primary_key ของตารางและ ส่ง column ที่ต้องการแก้ไขมากับ body

- DELETE `/:id` สำหรับลบข้อมูลในตารางโดยใช้ id คือ primary_key ของตาราง

## Controller


    "controller": "example", // controller ของ route
    "auth": true, // ต้องการใช้ jwt authen หรือไม่
    "permission": { // 
        "user": {
            "C": false,
            "R": true,
            "U": false,
            "D": false,
            "A": true
        },
        "admin": {
            "C": true,
            "R": true,
            "U": true,
            "D": true,
            "A": true
        }
    },
    "children": [{ // route ย่อยของ route นี้เช่น /example/alive
        "path": "/alive",
        "method": "get",
        "function": "alive"
    }]