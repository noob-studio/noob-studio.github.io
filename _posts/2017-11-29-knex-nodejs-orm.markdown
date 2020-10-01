---
layout: post
title: "วิธีใช้ knex.js จัดการฐานข้อมูล MySQL ใน Node.js"
description: "วิธีการใช้ knex.js จัดการฐานข้อมูล MySQL แบบ Object ใน Node.js ด้วยในยุคนี้นะครับเป็นยุคที่ Javascript ครองเมืองหันไปทางไหนก็มีแต่ Javascript เต็มไปหมดเลยครับไม่ว่าจะ React, Angular, Vue, Node, MEAN โอ้ยยเยอะแยะไปหมด หลายๆที่ก็เริ่มเปลี่ยนฝั่ง Back จาก PHP มาเป็น Node.js และฝั่ง Front ก็เริ่มเป็น Angular, Vue, React อะไรก็ว่า แต่เขียนโค้ดใหม่ที่ว่ายากแล้วการต้อง migration ข้อมูลจาก MySQL ไปเป็นฐานข้อมูลแบบ NoSQL สถาปัตยกรรมมันเข้ากันก็ลำบากเหลือเกิน ครั้นจะเขียน SQL คิวรี่เหมือนเดิมก็ขัดใจ๋ขัดใจ การเขียนโปรแกรมแบบ ORM (Object Relational Mapping) จึงเกิดขึ้น การเขียนโปรแกรมแบบ ORM พูดง่ายๆก็คือการแปลงข้อมูลที่ไม่เป็น Object มาอยู่ในรูป Object สำหรับใช้ภาษาโปรแกรมที่เป็น Object Oriented ซึ่งในบทความนี้เราจะใช้ ORM Tool ชื่อว่า"
date: 2017-11-29 10:38
author:     "ป๋าแพะ"
published: true
hero: 'img/node-orm/cover.jpg'
categories:
 - backend
 - database
 - library
tags: 
 - node.js
 - javascript
 - MySql
---

ด้วยในยุคนี้นะครับเป็นยุคที่ Javascript ครองเมืองหันไปทางไหนก็มีแต่ Javascript เต็มไปหมด
{: .lead}

เลยครับไม่ว่าจะ React, Angular, Vue, Node, MEAN โอ้ยยเยอะแยะไปหมด หลายๆที่ก็เริ่มเปลี่ยนฝั่ง Back จาก PHP มาเป็น Node.js และฝั่ง Front ก็เริ่มเป็น Angular, Vue, React อะไรก็ว่า แต่เขียนโค้ดใหม่ที่ว่ายากแล้วการต้อง migration ข้อมูลจาก MySQL ไปเป็นฐานข้อมูลแบบ NoSQL สถาปัตยกรรมมันเข้ากันก็ลำบากเหลือเกิน ครั้นจะเขียน SQL คิวรี่เหมือนเดิมก็ขัดใจ๋ขัดใจ การเขียนโปรแกรมแบบ ORM (Object Relational Mapping) จึงเกิดขึ้น การเขียนโปรแกรมแบบ ORM พูดง่ายๆก็คือการแปลงข้อมูลที่ไม่เป็น Object มาอยู่ในรูป Object สำหรับใช้ภาษาโปรแกรมที่เป็น Object Oriented ซึ่งในบทความนี้เราจะใช้ ORM Tool ชื่อว่า
<a herf="http://knexjs.org/" target="_blank">knexjs.org</a> นั่นเอง
<!–-break-–>


<blockquote>ORM คือการแปลงข้อมูลที่ไม่เป็น Object มาอยู่ในรูป Object สำหรับใช้ภาษาโปรแกรมที่เป็น Object Oriented</blockquote>
<p style="color:red;">หมายเหตุในบทความนี้ใช้ async / await เยอะมากเพื่อความเข้าใจกรุณาไปศึกษาเรื่องนี้มาก่อนนะจ๊ะ</p>

# What is knex.js

<p>knex.js คือเครื่องมือที่ช่วยให้เราเขียน node.js สามารถจัดการกับฐานข้อมูลในรูปแบบ Object ได้ (ORM) ทำให้เราเขียนโปรแกรมได้ง่ายขึ้นเพราะไม่ต้องมาคอยเขียนคำสั่ง SQL และแปลงข้อมูลที่ได้มาให้เป็น Object ของเราอีกทีนึงให้วุ่นวาย ขั้นตอนการใช้งานมีดังนี้</p>

# Setup

<p>เอาละครับเรารู้จัก ORM กับ knex.js กันคร่าวๆ แล้วเรามาลงมือทำกันเลยดีกว่าเปิด terminal ขึ้นมาแล้วพิมพ์ตามนี้</p>
<pre>
mkdir test-knex 
cd test-knex
npm init
npm install -–save knex mysql
</pre>
<p>ขั้นตอนแรกสร้างโฟลเดอร์ขึ้นมาแล้วติดตั้ง Module ต่างๆที่จำเป็นดังนี้ knex และ mysql</p>

# Connect Database

<p>ต่อไปเรามาสร้าง CRUD จาก knex กันนะครับโดยขั้นตอนแรกให้เพิ่มทำการ connect ฐานข้อมูลก่อนดังนี้</p>
~~~js
this.knex = knex({
    client: 'mysql',
    connection: {
        host : '127.0.0.1',
        user : 'root',
        password : '',
        database : 'knex'
    }
});
~~~
<p>บรรทัดนี้เป็นการเชื่อมต่อกับฐานข้อมูลนะครับโดยใส่รายละเอียดต่างๆ เข้าไป client คือบอกว่าเราใช้ฐานข้อมูลประเภทอะไรส่วน connection ก็คือรายละเอียดทั่วๆไปของฐานข้อมูล</p>

# Do table Job

<p>ก่อนที่เราจะสร้าง CRUD ได้ เราจำเป็นต้องมีตารางสำหรับทดสอบก่อนโดยเราสามารถจัดการกับตารางได้โดยใช้คำสั่งต่อไปนี้</p>
~~~js
await this.knex.schema.dropTableIfExists('Blog');
await this.knex.schema.createTableIfNotExists('Blog', (tbl) => {
    tbl.increments('id').primary().unique().index();
    tbl.string('name');
    tbl.string('writer');
});
~~~

คำสั่งข้างล่างนี้

~~~js
this.knex.schema.dropTableIfExists('Blog');
~~~ 
คือการ Drop ตารางถ้าหากว่ามีตารางนี้แล้ว เทียบได้กับ คำสั่ง

~~~sql 
DROP TABLE IF EXIST ‘Blog’
~~~ 

ใน MySQLและ 

~~~js
this.knex.schema.createTableIfNotExists('Blog', (tbl) => {
	...
}); 
~~~

<p>คือคำสั่งสำหรับตารางถ้ายังไม่มี</p>

~~~js
tbl.increments('id').primary().unique().index();
tbl.string('name');
tbl.string('writer');
~~~

<p>คือการ set structure ให้ตาราง Blog โดยเทียบได้กับคำสั่ง</p>

~~~sql
CREATE TABLE IF NOT EXISTS ‘Blog’ (‘id’ INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
‘name’ VARCHAR(255), ‘writer’ VARCHAR(255));
~~~

<p>ซึ่ง data type ใน knex มีครอบคลุมทุกประเภทใน MySQL โดยสามารถดูเพ่ิมเติมได้ที่ <a herf="http://knexjs.org/#Schema-Building" target="_blank">knexjs.org</a></p>

# CREATE

ต่อมาเรามาเริ่ม insert ข้อมูลชุดแรกลงฐานข้อมูลกัน โดยการ insert ข้อมูลของ knex มี 2 แบบคือ แบบธรรมดา และ แบบ Batch สำหรับ insert ข้อมูลจำนวนมาก
	ตัวอย่างคำสั่ง insert ข้อมูลธรรมดา

~~~js
    await this.knex('Blog').insert({name: 'lonely insert', writer: 'lonely man'});
~~~

เทียบได้กับ

~~~sql
INSERT INTO Blog (‘name’, ‘writer’) VALUES (‘lonely insert’,’lonely man’);
~~~

หาต้องการ insert ID ให้เอาตัวแปลไปรับได้เลย เช่น 

~~~js
let id = await this.knex('Blog').insert({name: 'lonely insert', writer: 'lonely man'});
~~~

ตัวอย่างคำสั่ง insert ข้อมูลแบบ Batch สำหรับ insert ข้อมูลจำนวนมาก

~~~js
await this.knex.batchInsert('Blog',[{
        name: 'hello world',
        writer: 'mike'
    },{
        name: 'hello awesome',
        writer: 'nun'
    },{
        name: 'this is awesome lib',
        writer: 'mike'
}]);
~~~

เทียบได้กับ

~~~sql
INSERT INTO Blog (‘name’, ‘writer’) VALUES (‘hello world’,’mike’),(‘hello awesome’, ’nun’),(‘this is awesome lib’, ‘mike’);
~~~

# READ

เมื่อเรา insert  ข้อมูลลงฐานข้อมูลเสร็จแล้วต่อมาเราจะทำการอ่านข้อมูลจากฐานข้อมูลซึ่งเราสามารถทำได้โดยใช้คำสั่งดังนี้

~~~js
let data = await this.knex('Blog').select();
~~~

เทียบได้กับ

~~~sql
SELECT * FROM Blog
~~~

หรือหากต้องการใส่เงื่อนไขในการคิวรี่ข้อมูลก็สามารถทำได้ดังนี้

~~~js
let data = await this.knex('Blog').where({writer: 'mike'}).select();
~~~

เทียบได้กับ

~~~sql
SELECT * FROM Blog WHERE writer = ‘mike’
~~~

นอกจากนี้คำสั่งที่ใช้การคิวรี่อื่นๆ เช่น JOIN, COUNT, SUM, GROUP BY และอื่นๆ knex ก็รองรับนะครับโดยสามารถดูตัวอย่างการใช้งานได้ที่นี่นะครับ  <a herf="http://knexjs.org/" target="_blank">knexjs.org</a>


# UPDATE


READ เสร็จแล้วต่อมาก็คือ UPDATE ซึ่งใน การ UPDATE ก็สามารถทำได้ดังนี้

~~~js
await this.knex('Blog').where({writer: 'lonely man'}).update({name: 'not alone insert'});
~~~

 เทียบได้กับ

~~~sql
UPDATE Blog SET name = 'not alone insert' WHERE writer = 'lonely man'
~~~

# DELETE

สุดท้ายแล้วนะครับเมื่อเรา CREATE READ UPDATE จนหนำใจแล้วพอเราเริ่มเบื่อเราก็ลบมันทิ้งโดยใช้คำสั่งดังนี้

~~~js
await this.knex('Blog').where({writer: 'mike'}).del();
~~~

เทียบได้กับ

~~~sql
DELETE from Blog WHERE writer = 'mike'
~~~

# Conclusion

หมดแล้วครับสำหรับบทความนี้ก็สั้นหน่อยนะครับเพราะแอบเขียนในที่ทำงานแหะๆ เหมือนเดิมครับ source code อยู่บน Github ตามลิงค์นี้นะจ๊ะ
<a href="https://github.com/freeweed/knex-example" target="_blank">Github</a>
