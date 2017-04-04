---
layout:     post
comments:   true
title:      "แจ้งเตือนการทำงานของ Server แบบ Low cost ด้วย Line Notify"
subtitle:   "เขียนสคริปแจ้งเตือนการทำงานของ Server แบบไม่มีค่าใช้จ่ายด้วย Line Notify และ Node.js"
date:       2017-03-17 22:41:00
author:     "ป๋าแพะ"
header-img: "img/moniter/cover.jpg"
---
<p>หลายคนคงจะทราบกันอยู่แล้วนะครับว่า Line ได้เปิดตัวบริการใหม่คือ Line Notify เมื่อไม่นานที่ผ่านมา
ซึ่งตัวไลน์ Notify เนี่ยจะแตกต่างกัน Line Bot ตรงที่ว่าจะตอบเราไม่ได้ และเวลาจะใช้ก็ต้องเชิญเข้า Group ก่อน
ซึ่งประโยชน์ของมันคือใช้สำหรับติดตามดูความเคลื่อนไหวของ service หรือ server ของเราก็ได้ ซึ่ง Line ก็มี Partner สำหรับบริการเราชื่อ <a href="https://mackerel.io/" target="_blank">Mackerel</a> แต่ประเด็อคือมันเสียตังน่ะสิ (= =") ซึ่งแน่นอนว่าโปรแกรมเมอร์สายประหยัดอย่างเราไม่ยอมเสียเงินแน่นอน 555+ เพราะฉะนั้นเรามาเขียนเองกันดีกว่า</p>

<h2 class="section-heading">Required & Install</h2>
<p>สิ่งที่ต้องการในการ Monitor เซิฟเวอร์แบบ Low Cost ของเรามีดังนี้</p>
<ul>
    <li>ID Line เชื่อว่าทุกคนคงจะมีอยู่แล้วนะครัคงไม่สอนสมัคร (= =")</li>
    <li>กลุ่มไลน์ที่ต้องการให้ส่งข้อความ (ตรงนี้แล้วแต่นะ)</li>
    <li>ความรู้ Node.js (จริงๆใช้อะไรเขียนก็ได้แต่ผมชอบ Node.js อ่ะมีไรป่ะ)</li>
</ul>

<h2 class="section-heading">Start</h2>
<p>ขั้นตอนแรกให้เราเข้าสู่เว็ปไซด์ <a href="https://notify-bot.line.me/en/" target="_blank">Line Notify</a> และ Login ให้เรียบร้อยหลังจากนั้นกดที่ My Page ตามภาพ</p>
<img src="{{ site.baseurl }}/img/moniter/img01.png" alt="เข้าสู่หน้า My Page">
<span class="caption text-muted">เมนู My Page จะปรากฏเมื่อคลิ๊กที่ชื่อไลน์ของคุณ</span>
<p>เลื่อนลงมาข้างล่างจะเห็นคำว่า Generate Token คลิ๊กโลดเมื่อคลิ๊กแล้วจะขึ้นหน้าตาดังภาพข้างล่างให้กรอกรายละเอียดให้เรียบร้อยในกรณีที่ต้องการให้ส่งข้อความให้เฉพาะตัวเองให้เลือก 1-on-1 chat</p>
<img src="{{ site.baseurl }}/img/moniter/img02.png" alt="สร้าง Token">
<span class="caption text-muted">เลือก 1-on-1 chat ถ้าอยากคุยคนเดียว</span>
<p>เมื่อสร้างเสร็จจะได้ Token มาหนึ่งอันให้ก๊อบปี้เก็บไว้ก่อน (อย่าทำหายล่ะ!) ต่อมาเรามาเริ่มเขียนโค้ดเพื่อส่งการแจ้งเตือนกันเถอะทำตามคุณครูนะค่ะเด็กๆ</p>
<pre>
    mkdir monitor-server
    cd moniter-server
    npm init
    npm install monitor request --save
</pre>
<ul>
    <li>monitor ใช้สำหรับติดตามการทำงานของ server</li>
    <li>request ใช้สำหรับส่ง request ไปยัง Line Notify (จริงๆ เขียนเองก็ได้แต่ขี้เกียจ 555)</li>
</ul>
<p>ต่อมาสร้างไฟล์ index.js และใส่ Code ข้างล่างลงไป</p>
<p><b>index.js</b></p>
{% highlight javascript %}
const Monitor = require('monitor');
const request = require('request')
const LOW_MEMORY_THRESHOLD = 100000000;
const token = 'token';

var options = {
  probeClass: 'Process',
  initParams: {
    pollInterval: 10000
  }
}
var processMonitor = new Monitor(options);

processMonitor.on('change', () => {
  var freemem = processMonitor.get('freemem');
  var msg = "Your Free memory Left "+freemem;
  request({
     method: 'POST',
     uri: 'https://notify-api.line.me/api/notify',
     headers: {
       'Content-Type': 'application/x-www-form-urlencoded',
  },
     'auth': {
       'bearer': token
  },form: {
       message: msg,
    }
  }, (err,httpResponse,body) => {
     console.log(JSON.stringify(err));
     console.log(JSON.stringify(httpResponse));
     console.log(JSON.stringify(body));
  })
});

processMonitor.connect((error) => {
  if (error) {
    console.error('Error connecting with the process probe: ', error);
    process.exit(1);
  }
});
{% endhighlight%} 
<p>อธิบาย Code</p>
{% highlight javascript %}
const Monitor = require('monitor');
const request = require('request')
const LOW_MEMORY_THRESHOLD = 100000000;
const token = 'token';
{% endhighlight %}
<p>เรียกใช้งาน Module Monitor และ request ตั้งค่าแรมที่น้อยที่สุดหากน้อยกว่านี้ให้แจ้งเตือนใส่ token เตรียม (อย่าลืมใส่ token ของคุณล่ะ)</p>
{% highlight javascript %}
var options = {
  probeClass: 'Process',
  initParams: {
    pollInterval: 10000
  }
}
var processMonitor = new Monitor(options);
{% endhighlight %}
<p>เซ็ท Option สำหรับ Monitor และให้วนเช็คทุกๆ 10000 milliseconds และสร้างการ Monitor ใหม่</p>
{% highlight javascript %}
processMonitor.on('change', () => {
  var freemem = processMonitor.get('freemem');
  var msg = "Your Free memory Left "+freemem;
  request({
     method: 'POST',
     uri: 'https://notify-api.line.me/api/notify',
     headers: {
       'Content-Type': 'application/x-www-form-urlencoded',
  },
     'auth': {
       'bearer': token
  },form: {
       message: msg,
    }
  }, (err,httpResponse,body) => {
     console.log(JSON.stringify(err));
  })
});
{% endhighlight %}
<p>เมื่อสถานะของ Monitor เปลี่ยนจะเข้า event change ให้ดึงจำนวนแรมที่เหลือ และส่ง request ไปที่ Line Notify โดยฝัง token ไปกับ  Header และส่งข้อความที่สร้างขึ้นไปด้วย ซึ่งจะเห็นว่าจริงๆแล้ว Line Notify เนี่ยแค่ request ไปให้ถูกก็พอไม่จำเป็นต้องติดตั้ง Library อะไรของ Line เพิ่มเติมพิเศษเลย</p>
<blockquote>Line Notify แค่ส่ง request ไปให้ถูกก็พอไม่จำเป็นต้องไปติดตั้ง Library ให้วุ่นวาย</blockquote>
{% highlight javascript %}
processMonitor.connect((error) => {
  if (error) {
    console.error('Error connecting with the process probe: ', error);
    process.exit(1);
  }
});
{% endhighlight%} 
<p>ให้แสดง error และปิดโปรแกรมหากไม่สามารถดึงข้อมูลมาได้ เมื่อโค้ดเสร็จแล้วก็มาทำสอบกันด้วยคำสั่ง</p>
<pre>node index.js</pre>
<p>จะเห็นว่ามีไลน์ขึ้นมาดังภาพ</p>
<img src="{{ site.baseurl }}/img/moniter/img03.png" alt="ผลการทดสอบ">
<span class="caption text-muted">รันปุ๊บขึ้นปั๊บ ถ้าไม่รับปั๊บก็คงไม่ขึ้นปุ๊บ</span>
<p>ต่อมาจะเห็นว่าตอนนี้มันแจ้งเตือนบ่อยจนหน้ารำคาญให้เราปรับปปรุง Code ใน change event เป็นดังนี้</p>
{% highlight javascript %}
processMonitor.on('change', () => {
  var freemem = processMonitor.get('freemem');
  if (freemem < LOW_MEMORY_THRESHOLD) {
    var msg = 'Low memory warning: ' + freemem;
    request({
         method: 'POST',
         uri: 'https://notify-api.line.me/api/notify',
         headers: {
           'Content-Type': 'application/x-www-form-urlencoded',
         },
         'auth': {
           'bearer': token
         },form: {
           message: msg,
         }
    }, (err,httpResponse,body) => {
         console.log(JSON.stringify(err));
    })
}
});
{% endhighlight %}
<p>เพียงเท่านี้เราก็จะได้รับการแจ้งเตือนเมื่อแรมเราเหลือน้อยกว่าที่เรากำหนดไว้แล้วล่ะครับสำหรับคนที่ต้องการตรวจสอบอย่างอื่นเช่น CPU, heapTotal, heapUsed สามารถดูได้ <a href="http://lorenwest.github.io/node-monitor/doc/classes/ProcessProbe.html" target="_blank">ที่นี่</a></p>
<p>สำหรับคนที่ต้องการใช้ฟังชันอื่นๆของ Line Notify สามารถดูได้ <a href="https://notify-bot.line.me/doc/en/" target="_blank">ที่นี่</a></p>
<p>สามารถดาวโหลด Code ได้ที่นี่ <a href="https://github.com/noob-studio/monitor-server" target="_blank">Github</a></p>