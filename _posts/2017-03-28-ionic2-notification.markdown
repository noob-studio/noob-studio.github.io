---
layout:     post
comments:   true
title:      "วิธีทำ Notification(แจ้งเตือน) ให้ Ionic2 ด้วย Firebase และ Node.js"
subtitle:   "วิธี push notification สำหรับ Ionic 2 แบบง่ายๆด้วย Firebase และ Node.js"
date:       2017-03-28 22:41:00
author:     "ป๋าแพะ"
header-img: "img/ionic-notification/cover.jpg"
---
<p>สวัสดีครับหลังจากคราวก่อนได้เขียนวิธีสร้าง <a href="{{ site.baseurl}}{% post_url 2017-03-18-ionic-chat %}">แอพพลิเคชันแชทแบบง่ายๆ ด้วย Ionic2 Node.js</a> ก็มีคนบอกว่าอยากให้เขียนวิธีการแจ้งเตือนให้มือถือและผมก็คิดเรื่องจะเขียนไม่ออกด้วยครับช่วงนี้เพราะฉะนั้นวันนี้ีเราทำแจ้งเตือนกันเถอะ 555 โดยแจ้งเตือนเนี่ยมี 2 แบบคือแบบ Local Notification และไม่ Local โดยจะแตกต่างกันดังนี้</p>
<ul>
<li>Local Notification (เกมเตือนให้รับของประจำวัน) ไม่จำเป็นต้องมี Server เป็นการตั้งค่าให้แจ้งเตือนตามเวลาที่ตั้งไว้ข้อดีคือง่ายแต่มีข้อเสียคือข้อมูลจะตายตัวไม่มีการเปลี่ยนแปลง (ไว้บทความถัดไปนะจ๊ะ)</li>
<li>ไม่ Local (facebook, line) จำเป็นต้องมี Server เพื่อส่งแจ้งเตือนให้แอพพลิเคชันมีข้อดีคือสามารถแจ้งเตือนเมื่อไหร่ก็ได้ข้อมูลสามารถยืดหยุ่นตาม Server ส่งมาให้ (สอนในบทความนี้)</li>
</ul>
<blockquote>แจ้งเตือนที่มีข้อมูลแบบไม่ตายตัว(app แชททั่วไป) ต้องมี server แจ้งเตือนแบบตายตัว(เช่น เกมเตือนให้รับของประจำวัน) ไม่จำเป็นต้องมี server</blockquote>
<h2 class="section-heading">Required & Install</h2>
<p>สิ่งที่จะใช้ในการทำแจ้งเตือนของบทความนี้มีดังนี้</p>
<ul>
    <li>Node.js เวอร์ชันล่าสุด</li>
    <li>gmail (คิดว่าทุกคนคงจะมีนะจ๊ะ)</li>
    <li>มือถือ Android (ใช้ทดสอบ)</li>
    <li>ในการทดสอบโทรศัพท์มือถือและคอมเครื่องที่ใช้เป็น server ต้องต่อเน็ตวงเดียวกันนะจ๊ะ (Wifi อันเดียวกัน)</li>
</ul>

<h2 class="section-heading">Init Firebase</h2>
<p>ขั้นแรกให้เราเปิดเว็ปไซด์ <a href="https://firebase.google.com/?hl=en" target="_blank">Firebase</a> ขึ้นมาพร้อม Login ให้เรียบร้อยหากยังไม่มีโปรเจคให้สร้างโปรเจคด้วยนะครับ</p>
<img src="{{ site.baseurl }}/img/ionic-notification/img01.png">
<span class="caption text-muted">สร้างโปรเจคขึ้นมาใน Firebase</span>
<p>หลังจากนั้นกดเข้ามาใน Project และไปที่ Project Settings > Cloud Messaging จะเห็นหน้าจอแบบภาพข้างล่างนะครับให้จดเลข Server Key และ Sender ID ไว้นะจ๊ะ</p>
<img src="{{ site.baseurl }}/img/ionic-notification/img02.png">
<span class="caption text-muted">จด Server Key กับ Sender ID ไว้ให้ดีนะจ๊ะต้องใช้</span>
<ul>
    <li>Server Key ใช้สำหรับใส่ใน Server ของเรา</li>
    <li>Sender ID ใช้สำหรับใส่ในแอพของเรา</li>
</ul>
<h2 class="section-heading">Start Server</h2>
<p>เมื่อเราตั้งค่า Firebase เสร็จแล้วต่อไปเรามาเริ่มสร้าง Server สำหรับส่งแจ้งเตือนกันนะจ๊ะ</p>
<pre>
    mkdir ionic-notification
    cd ionic-notification
    npm init
    npm install node-gcm express --save
</pre>
<p>node-gcm ใช้สำหรับส่งแจ้งเตือนนะครับส่วน express ใช้เป็น server</p>
<p><b>index.js</b></p>
{% highlight javascript %}
const express = require('express');
const gcm = require('node-gcm');
const bodyParser = require('body-parser');
const app = express();
app.use( bodyParser.json() );

const server_key = 'XXXXXXXXXXX'; // server key

var send = (token) => {
  let retry_times = 3;
  let sender = new gcm.Sender(server_key);
  let message = new gcm.Message();
  message.addData('title', 'Hello Lady~!');
  message.addData('message', 'Hello Beutiful Girl!');
  sender.send(message, token, retry_times, (result) => {
    console.log('send noti!');
  }, (err) => {
    console.log('cant send noti T^T');
  })
}

app.get('/', (req, res) => {
    res.send("This is basic route");
});


app.post('/open', (req, res) => {
  /*insert token to DB*/
  let token = req.body.token.registrationId;
  console.log(JSON.stringify(token));
  send(token);
})

app.get('/send', (req, res) => {
  let token = 'XXXXXXXXXXXXX'; //token
  send(token);
  res.send("send notification");
})

app.listen(5555, () => {
    console.log('Program running on port : 5555');
});

{% endhighlight %}
<p>เรียบร้อยครับสั้นดีใช่ไหมครับสำหรับ Code ฝั่ง Server ของเราต่อไปอธิบาย Code นะครับ</p>
{% highlight javascript %}
const express = require('express');
const gcm = require('node-gcm');
const bodyParser = require('body-parser');
const app = express();
app.use( bodyParser.json() );

const server_key = 'XXXXXXXXXXX'; // server key
{% endhighlight %}
<p>เรียกใช้งาน Module ที่จำเป็นนะครับ node-cgm ใช้ส่งแจ้งเตือนส่วน bodyParse ใช้แปลงค่า input ที่ส่งผ่าน Post มานะครับ และสุดท้ายสร้าง server โดยใช้ express นะครับ ตรง server_key ให้ใส่ server_key ที่เราจดมาตะกี้ลงไปนะครับ (อันที่ยาวๆอ่ะ)</p>
{% highlight javascript %}
app.get('/', (req, res) => {
    res.send("This is home page");
});

app.post('/open', (req, res) => {
  ...
})

app.get('/send', (req, res) => {
  ...
})
app.listen(5555, () => {
    console.log('Program running on port : 5555');
});
{% endhighlight %}
<p>app.get ต่างๆคือการ route นะครับว่าเข้ามาผ่าน url นี้ให้ทำอะไรส่วน app.listen คือให้เริ่มทำงานที่ port ไหน</p>
<ul>
<li> เข้ามาผ่าน / ให้ตอบกลับไปว่านี่คือ Home Page นะย๊ะ</li> 
<li> /open รับ token เข้ามาผ่าน Post /open จริงๆตรงนี้ให้เราเก็บข้อมูล token เข้าสู่ฐานข้อมูลเพื่อใช้แจ้งเตือนในโอกาศต่อไป</li>
<li> /send เมื่อมีการส่ง Get เข้ามาผ่าน /send ให้ส่งแจ้งเตือนไปตาม token ที่เรากับไว้ในฐานข้อมูล</li>
</ul>
{% highlight javascript %}
  let retry_times = 3;
  let sender = new gcm.Sender(server_key);
  let message = new gcm.Message();
  let token = req.params.token;
  message.addData('title', 'Hello Lady~!');
  message.addData('message', 'Hello Beutiful Girl!');
{% endhighlight %}
<p>เตรียมตัวแปรสำหรับส่งแจ้งเตือน</p>
<ul>
<li>retry_times ถ้าส่งไม่สำเร็จจะให้ลองส่งใหม่กี่ครั้งสำหรับลูกผู้ชายอย่างผมให้โอกาศใครไม่เกิน 3 ครั้ง</li>
<li>sender สร้าง sender ใช้สำหรับส่งแจ้งเตือน</li>
<li>message สร้างข้อความสำหรับส่งไปให้มือถือของเรา</li>
<li>token รหัสของมือถือที่มีการเชื่อมต่อเข้ามา</li>
<li>message.addData ตั้งค่าต่างๆของ message เช่น หัวข้อ ข้อความ เสียงเป็นต้น</li>
</ul>
{% highlight javascript %}
sender.send(message, token, retry_times, (result) => {
    console.log('send noti!');
    res.send('you get message');
  }, (err) => {
    console.log('cant send noti T^T');
    res.send('cant send message');
  })
{% endhighlight %}
<p>sender.send ส่งแจ้งเตือนไปที่มือถือเมื่อทดสอบรันด้วยคำสั่งข้างล่างนี้เป็นอันเสร็จนะแจ๊ะ</p>
<pre>
  node index.js
  Program running on port : 5555
</pre>
<h2 class="section-heading">Start Ionic</h2>
<p>เอาล่ะไม่ต้องบรรยายมากมาเริ่มกันเตอะ</p>
<pre>
ionic start ionic-notification-client blank --v2
cd ionic-notification-client
ionic platform add android
cordova plugin add phonegap-plugin-push --variable SENDER_ID="XXXXXXX"
</pre>
<p>SENDER_ID คือ id ที่ได้มาจาก Firebase ตอนแรกนะครับ(อันสั้นๆ) ต่อมาเราเปิดไฟล์ src/app/app.component.ts เพิ่ม Code ดังนี้เข้าไป</p>
{% highlight javascript %}
import { Push, PushObject, PushOptions } from '@ionic-native/push';
import { Http } from '@angular/http';
{% endhighlight %}
<p>import module ที่ต้องการใช้โดยมีรายละเอียดดังนี้</p>
<ul>
<li>Push, PushObject, PushOptions ใช้สำหรับส่ง Notification ไปที่มือถือ</li>
<li>Http สำหรับส่ง Http ไปที่ Server ของเรา</li>
</ul>
{% highlight javascript %}
constructor(
    ..., 
    private push: Push
) 
{% endhighlight %}
<p>ประกาศตัวแปร private ชื่อ push โดยนำค่ามาจาก Module Push (ที่ import มาข้างบนอ่ะ)</p>
{% highlight javascript %}
constructor(
    ...
){
    platform.ready().then(() => {
      ...
      this.initPushNotification();
    });
}
{% endhighlight %}
<p>เรียกใช้ฟังชันชื่อ initPushNotification เมื่อแอพพร้อมทำงาน</p>
{% highlight javascript %}
initPushNotification(){
    const options: PushOptions = {
       android: {
           senderID: '492553210615',
           sound: true,
           vibrate: true
       }
    };
    const pushObject: PushObject = this.push.init(options);

    pushObject.on('registration').subscribe(
      data => {
        this.http.post('http://192.168.1.11:5555/open',{token: data}).subscribe(res => {
              console.log('register send');
        });
    });
    pushObject.on('notification').subscribe(
      notification => {
        alert('You are in app');
      });
  }
{% endhighlight %}
<p>สร้างฟังชันชื่อ initPushNotification สำหรับรับและส่งแจ้งเตือน <br/><b style="color:red;">หมายเหตุ</b> http://192.168.1.11 ให้ใส่เป็น ip  เครื่องตัวเองนะจ๊ะ</p>
{% highlight javascript %}
const options: PushOptions = {
       android: {
           senderID: '492553210615',
           sound: true,
           vibrate: true
       }
    };
{% endhighlight %}
<p>เตรียมค่า option ต่างๆสำหรับการแจ้งเตือน</p>
<ul>
<li>senderID รหัสที่ได้มาจาก Firebase (รหัสเดียวกับตอนที่ add plugin อันสั้นๆ)</li>
<li>sound ตั้งค่าเสียง</li>
<li>vibrate ตั้งค่าสั่น</li>
</ul>
{% highlight javascript %}
const pushObject: PushObject = this.push.init(options);
{% endhighlight %}
<p>ตั่งค่า option สำหรับแจ้งเตือน</p>
{% highlight javascript %}
pushObject.on('registration').subscribe(
  data => {
    ...
  }
);
{% endhighlight %}
<p>เป็น event ที่เกิดขึ้นเมื่อเปิด app โดยจะ return data ซึ่งมี token ของมือถือเครื่องนั้นๆ</p>
{% highlight javascript %}
  this.http.post('http://192.168.1.11:5555/open',{token: data}).subscribe(res => {
    console.log('register send');
  });
{% endhighlight %}
<p>ส่ง token ไปที่ server ของเราเพื่อบันทึกข้อมูลหรืออะไรก็ว่าไป</p>
{% highlight javascript %}
pushObject.on('notification').subscribe(
    notification => {
    alert('You are in app');
});
{% endhighlight %}
<p>เป็น event ที่เกิดขึ้นเมื่อได้รับการแจ้งเตือนจาก server เพียงแค่นี้เป็นอันเรียบร้อยโรงเรียนเตรียมแล้วจ้าทดสอบรันแอพด้วยคำสั่ง</p>
<pre>
ionic run android
</pre>
<img src="{{ site.baseurl }}/img/ionic-notification/img03.jpg">
<span class="caption text-muted">ผลลัพท์เมื่อรันแอพ</span>
<p>เมื่อแอพเริ่มทำงานจะมีการ alert แบบนี้ปรากฎขึ้นมาแบบนี้</p>
<img src="{{ site.baseurl }}/img/ionic-notification/img04.png">
<span class="caption text-muted">token ของแอพที่แสดงใน server</span>
<p>ส่วนใน server จะแสดง token ของเราที่ได้รับมาจากแอพให้ก๊อป token มาใส่ใน route /send แบบข้างล่างนี้</p>
{% highlight javascript%}
...
app.get('/send', (req, res) => {
  let token = 'fF6tMEynuYg:APA91bH8phSueoBfdOVMchBvBaxyyJVx4fe5msoZVbGAwLqAf...'; //token
  ...
})
{% endhighlight %}
<p>หลังจากนั้นให้ลองออกจากแอพแล้วเปิด Browser ไปที่พาท <a href="http://localhost:5555/send" target="_blank">http://localhost:5555/send</a> จะมีแจ้งเตือนขึ้นดังภาพ</p>
<img src="{{ site.baseurl }}/img/ionic-notification/img04.jpg">
<span class="caption text-muted">เย้ๆ มีแจ้งเตือนแล้วจ้า</span>
<p>เพียงเท่านี้ก็เสร็จแล้วจ้าสำหรับการแจ้งเตือนในบทความนี้ยังเป็นพื้นฐานมากๆ นะครับเพื่อนๆต้องเอาไปปรับปรุงต่อเพื่อนำมาใช้จริงต่อไปนะจ๊ะ</p>
<p>ศึกษาเพิ่มเติม
    <ul>
        <li><a href="https://ionicframework.com/" target="_blank">Ionic2</a></li>
        <li><a href="https://firebase.google.com/docs/cloud-messaging/" target="_blank">Firebase</a></li>
    </ul>
</p>
<p>สำหรับ Code บน git ก็แปะไว้ก่อนเหมือนเดิมนะครับเพราะช่วงนี้ผมงานเยอะมากๆเลยอ่ะ = ="</p>