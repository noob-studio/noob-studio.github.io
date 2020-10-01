---
layout: post
title: "วิธีทำแอพแชทง่ายๆ ด้วย Ionic2"
description: "มาทำแอพแชทแบบง่ายๆ ไว้คุยมุ้งมิ้งกับแฟนด้วย Ionic 2 กับ Node.Js กันเถอะ เบื่อไหมครับที่เวลาจะเขียนแอพขึ้นมาซักแอพหนึ่งเนี่ยต้องนั่งทำทั้ง Android iOS ไหนจะต้องเรียนรู้ภาษาใหม่ๆ ทั้ง Java Swift โหยลำบากวุ่นวายอ่ะ แล้วทำไมกูต้องไปเรียนรู้ภาษาใหม่ๆด้วยว่ะครับ ทั้งๆ ที่กูก็เขียบเว็ปเป็นอยู่แล้วทำไมจะเอาพวก Javascript CSS HTML มาทำแอพไม่ได้!! แต่ช้าก่อนนี่มันโลกยุค 2017 แล้วนะจ๊ะ TV Direct ขอนำเสนอ"
date: 2017-03-18 20:07:00
author: "ป๋าแพะ"
hero: "{{site.url}}/img/ionic-chat/cover.jpg"
categories:
 - mobile
 - backend
tags: 
 - node.js
 - ionic framework
 - Typescript
 - javascript
 - firebase
 - Angular
 - socket.io
---

เบื่อไหมครับที่เวลาจะเขียนแอพขึ้นมาซักแอพหนึ่งเนี่ยต้องนั่งทำทั้ง Android iOS 
{: .lead}

ไหนจะต้องเรียนรู้ภาษาใหม่ๆ ทั้ง Java Swift โหยลำบากวุ่นวายอ่ะ แล้วทำไมกูต้องไปเรียนรู้ภาษาใหม่ๆด้วยว่ะครับ ทั้งๆ ที่กูก็เขียบเว็ปเป็นอยู่แล้วทำไมจะเอาพวก Javascript CSS HTML มาทำแอพไม่ได้!! แต่ช้าก่อนนี่มันโลกยุค 2017 แล้วนะจ๊ะ TV Direct ขอนำเสนอ <a href="https://ionicframework.com/" target="_blank">Ionic2</a> !
<!–-break-–>

<blockquote>สมัยก่อนการทำแอพขึ้นมาซักแอพนึงเนี่ยต้องทำทั้ง iOS และ Android แต่เดี๋ยวนี้ไม่ต้องแล้ว</blockquote>

# What is Ionic2

Ionic2 คือเครื่องมือที่ทำให้เราสามารถเขียนแอพเพียงครั้งเดียวสามารถทำงานได้ทุก Platform (หรือที่เรียกแบบเกร๋ๆ ว่า Cross Platform) โดยใช้แค่ Javascript CSS HTML ในการเขียนโปรแกรมซึ่งการทำงานของ Ionic2 ก็คือตัว Ionic2 จะทำตัวเป็นเหมือน Browser ที่แสดงเฉพาะเว็ปๆ เดียวก็คือเว็ปที่เราเขียนขึ้นมานั่นเองซึ่งเราจะเรียกแอพที่ทำงานในลักษณะนี้ว่า Hybrid App หลายคนอ่านมาถึงตรงนี้อาจจะคิดว่ามันก็คือเว็ปธรรมดาก็ไม่เหมือนแอพจริงๆ สิ ผมขอบอกตรงนี้นะครับนี่มันโลกยุคไหนแล้วถ้าไม่มีคนบอกผมนั่งยันนอนยันตะแคงข้างยันเลยว่ายังไงก็ดูไม่ออกเอาล่ะโม้มาเยอะแล้วมาเริ่มกันเถอะ
<blockquote>Javascript ที่ Ionic2 ใช้คือ <a href="https://angular.io/" target="_blank">Angular2</a> นะครับ</blockquote>

# Required & Install

สิ่งที่ต้องการสำหรับใช้งาน

<ul>
    <li>พื้นฐาน Node.js นิดหน่อย (อีกแล้วครับท่าน)</li>
    <li>Node.js เวอร์ชันล่าสุด</li>
    <li>Android Studio เพราะว่าเราต้องการตัว Android SDK นะจ๊ะถ้าใครไม่มีสามารถดาวโหลดได้ <a href="https://developer.android.com/studio/index.html" target="_blank">ที่นี่</a> (จริงๆแล้วดาวโหลดแค่ SDK tool ก็ได้แต่ว่าผมว่าลง Android Studio ไปเลยจะเวิร์คกว่า)</li>
    <li>Geanymotion เป็น emulator ทดสอบแอพของเรานะครับถ้าใครไม่มีสามารถดาวโหลดได้ <a href="https://www.genymotion.com/" target="_blank">ที่นี่</a></li>
    <li>Ionic2 ติดตั้งสามารถด้วยคำสั่งนี้ 

    ~~~js
    npm install -g cordova ionic
    ~~~ 

    </li>
    <li>ในบทความนี้จะไม่ลงรายละเอียดถึงตัว Angular2 เยอะนะครับควรจะศึกษามาบ้างเล็กน้อยเพื่อความเข้าใจ</li>
</ul>

# Start Server

เมื่อเรามีสิ่งที่ต้องหมดแล้วต่อมาก็มาเริ่มกันเถอะ โดยเราจะเริ่มจากทำฝั่ง Server ก่อนนะจ๊ะ

~~~js
    mkdir ionic-chat
    cd ionic-chat
    npm init
    npm install socket.io express --save
~~~

socket.io สำหรับรับส่งข้อความแบบ real time ส่วน express ใช้เป็น server (จริงๆไม่ต้องใช้ก็ได้แต่ผมขี้เกียจเขียนเองอ่ะมีไรป่ะ) เมื่อทำเสร็จแล้วให้สร้างไฟล์ index.js ขึ้นมาโดยใส่ Code ข้างล่างนี้ลงไป
<b>index.js</b>

~~~js
const express = require('express');
const app = express();
const server = require('http').createServer(app);
const io = require('socket.io').listen(server);
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
    next();
});
app.get('/', (req, res) =>{
    res.send('This is ma awesome server');
});
io.on('connection', (client) => {
    console.log('Client connected...');
    client.on('msg', (message) => {
        io.emit('msg', message);
    });
});
server.listen(5555, () => {
    console.log('Program running on port : 5555');
});
~~~

แค่นี้เหลาะครับสำหรับฝั่ง Server ของเราเป็นอันเรียบร้อยอธิบาย Code นะครับ

~~~js
const express = require('express');
const app = express();
const server = require('http').createServer(app);
const io = require('socket.io').listen(server);
~~~
เรียกใช้และสร้าง server โดยใช้ express และผูก Socket.io ไว้กับ server ที่ถูกสร้างขึ้น
~~~js
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin");
    res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
    next();
});
~~~
เซ็ท Header ให้เรียกใช้จาก server อื่นได้
~~~js
app.get('/', (req, res) =>{
    res.send('This is ma awesome server');
});
~~~
เซ็ท route สำหรับคนที่เข้ามาใน server ผ่านทาง browser 
~~~js
io.on('connection', (client) => {
  ...
});
~~~
เป็น event ที่จะเกิดขึ้นเมื่อมีผู้ใช้เชื่อมต่อเข้ามาในท่อ
~~~js
client.on('msg', (message) => {
  ...
});
~~~
เป็น event ที่จะเกิดขึ้นเมื่อผู้ใช้ส่งข้อมูลเข้ามาผ่านท่อที่ชื่อว่า msg
~~~js
io.emit('msg', message);
~~~
ส่ง msg ที่ได้รับให้ผู้ใช้ทุกคน 
<blockquote>
ถ้าหากต้องการส่งให้ทุกคนยกเว้นตัวคนส่งเองให้ใส่เป็น
~~~js
client.broadcast.emit('msg', message);
~~~
แต่ถ้าต้องการส่งกลับไปให้คนส่งคนเดียวเท่านั้น(แล้วจะเรียกว่า chat ไหมหนอ) ให้ใส่เป็น
~~~js
client.emit('msg', message);
~~~
</blockquote>
~~~js
server.listen(5555, () => {
    console.log('Program running on port : 5555');
});
~~~
เริ่มต้นการทำงานของ server ที่ port 5555 ด้วยคำสั่ง
<pre>node index.js</pre>
เมื่อทดลองเข้าผ่านหน้าเว็ปไปที่ <a href="http://localhost:5555" target="_blank">http://localhost:5555</a> จะได้หน้าตาดังภาพข้างล่างนี้
<img src="{{ site.baseurl }}/img/ionic-chat/img01.png" alt="ทดลองรัน server">
<span class="caption text-muted">ทดลองรัน server ผ่าน Browser</span>

# Start Ionic2

เอาล่ะครับน่าจะถึงเวลาที่ทุกคนรอคอยได้เขียนแอพจริงๆซักที 5555 เริ่มด้วยคำสั่ง
<pre>
ionic start ionic-chat-client blank --v2
cd ionic-chat-client
</pre>
เข้ามาจะเห็นไฟล์อะไรเยอะแยะเลยนะครับไม่ต้องตรงใจในตอนนี้เราสนใจแค่ในโฟรเดอร์ src ก็พอเมื่อเข้ามาจะเห็นว่าโครงสร้างมันคล้ายๆเว็ปไซด์เลยให้เปิดไฟล์ index.html ขึ้นมาหลังจากนั้นให้เพิ่มโหลด script นี้เข้าไป
~~~html
<script src="https://cdnjs.cloudflare.com/ajax/libs/socket.io/1.7.3/socket.io.js"></script>
~~~
เข้าไปที่ Page/home/home.html และแก้ไข Code ให้เป็นดังนี้
~~~html
<ion-header>
  <ion-navbar>
    <ion-title>
      Ionic Blank
    </ion-title>
  </ion-navbar>
</ion-header>

<ion-content padding>
  <ion-card *ngFor="let msg of massage">
    <ion-card-content>
      {{ "{{ msg " }}}}

    </ion-card-content>
  </ion-card>
</ion-content>

<ion-footer>
  <ion-toolbar>
    <ion-item>
      <ion-label fixed>Massage: </ion-label>
      <ion-input type="text" [(ngModel)]="msg"></ion-input>
    </ion-item>
    <ion-buttons end (click)="send()">
      <button ion-button icon-right color="royal">
        Send
        <ion-icon name="send"></ion-icon>
      </button>
    </ion-buttons>
  </ion-toolbar>
</ion-footer>
~~~
จาก Code ข้างบนจะมีจุดสังเกตุ 3 จุดดังนี้
~~~html
<ion-card *ngFor="let msg of massage">
    <ion-card-content>
      {{ "{{ msg " }}}}
    </ion-card-content>
</ion-card>
~~~
*ngFor="..." คือการสั่งให้วนลูปของ Angular2 ส่วน {{ "{{ msg " }}}} คือการแสดงค่า Object ของ Angular2 หรือเรียกว่า Two-way binding (จะไว้อธิบายในบทความต่อๆไปนะครับ) และส่วนสุดท้าย (click)="send()" คือการควบคุม event click นะครับถ้าเป็น Javascript ธรรมดาก็อารมณ์ Onclick ประมาณนั้น
รันจากนั้นเมื่อทดสอบรันด้วยคำสั่ง <pre>ionic serve</pre> ionic จะทำการเปิด Browser ขึ้นมาและจะได้หน้าตาเปล่าๆ ประมาณนี้นะครับ
<img src="{{ site.baseurl }}/img/ionic-chat/img02.png" alt="ทดสอบรันแอพ">
<span class="caption text-muted">ทดสอบรันแอพผ่าน Browser</span>
ต่อมาให้เราเปิดไฟล์ Page/home/home.ts ขึ้นมาและแก้ไขให้เป็นดังนี้นะครับ
~~~js
import { Component } from '@angular/core';

import { NavController } from 'ionic-angular';

declare var io;

@Component({
  selector: 'page-home',
  templateUrl: 'home.html'
})
export class HomePage {

  socketHost: string;
  socket: any;
  massage: any;
  msg: string;
  constructor(public navCtrl: NavController) {
    this.socketHost = "http://localhost:5555";
    this.massage = [];
    this.socket = io(this.socketHost);
    this.socket.on("msg", (msg) => {
      this.massage.push(msg);
    });
  }

  send(){
    this.socket.emit("msg", this.msg);
    this.msg = "";
  }

}
~~~
อธิบาย Code นะครับ
~~~js
declare var io;
~~~
คือการประกาศตัวแปร io นะครับจริงๆไม่ต้องประกาศก็ได้แต่ตัว Typescript ไม่รู้จัก io เลยต้องประกาศไม่งั้นจะงอแงเอา
~~~js
  socketHost: string;
  socket: any: any;
  massage: string[];
  msg: string;
~~~
ประกาศตัวแปลสำหรับเก็บค่าต่างๆ นะครับ 
<ul>
<li>socketHost เป็น string เก็บ url ของ server</li>
<li>socket สำหรับเก็บ socket นะครับไม่รู้จะให้ type อะไรเลยใส่เป็น any (any คือท่าไม้ตายคิด type ไม่ออก any ไปก่อน 555)</li>
<li>massage สำหรับเก็บข้อความที่ chat ไปมาเป็น string array</li>
<li>msg สำหรับเก็บข้อความจากช่อง input เป็น string</li>
</ul>
~~~js
this.socketHost = "http://localhost:5555";
this.massage = [];
~~~
กำหนดค่าสำหรับตัวแปลต่างๆ
~~~js
this.socket = io(this.socketHost);
~~~
สร้าง socket ขึ้นมา
~~~js
this.socket.on("msg", (msg) => {
  this.massage.push(msg);
});
~~~
เมื่อได้รับข้อความจาก server ผ่านทางท่อ msg ให้ push ใส่ array massage
~~~js
send(){
    this.socket.emit("msg", this.msg);
    this.msg = "";
  }
~~~
ส่งข้อความไปให้ server ผ่านทางท่อ msg แค่นี้เหลาะครับเสร็จแล้วหลังจากนั้นลองดูที่ terminal ที่รัน server ของเราถ้าขึ้นว่า Client connected... ดังภาพเป็นอันเรียบร้อยโรงเรียนเตรียม 
<img src="{{ site.baseurl }}/img/ionic-chat/img03.png" alt="แอพเสร็จแล้ว">
<span class="caption text-muted">มีการเชื่อมต่อจาก client สู่ server</span>
ลองเปิด Browser ไปที่ <a href="http://localhost:8100/" target="_blank">http://localhost:8100</a> 2 หน้าต่างและทดสอบใช้จะได้ผลลัพท์ดังภาพ
<img src="{{ site.baseurl }}/img/ionic-chat/img04.gif" alt="ทดสอบเล่นแอพ">
<span class="caption text-muted">แชทได้แล้วจ้า</span>
สำหรับใครที่ต้องการทดสอบบน emulator ให้เปิด geanymotion และเปลี่ยน socketHost ไป ip เครื่องตัวเองเช่น
~~~js
this.socketHost = "192.168.1.28:5555";
~~~
และทดสอบรันด้วยคำสั่งข้างล่างนี้ก็เป็นเรียบร้อย
<pre>
ionic platform add android
ionic run android
</pre>

# Conclusion

ศึกษาเพิ่มเติม
<ul>
    <li><a href="https://ionicframework.com/" target="_blank">Ionic2</a></li>
    <li><a href="https://socket.io/" target="_blank">socket.io</a></li>
</ul>
สำหรับ Code บน git แปะไว้ก่อนนะครับเพราะตอนที่เขียนบทความนี้ดึกมากแล้วง่วงมักๆ ถ้าไม่ลืมจะตามมาอัพให้นะครับ
