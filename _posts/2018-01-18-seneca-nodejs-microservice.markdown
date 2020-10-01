---
layout: post
title: "เขียน Microservice ด้วย Node.js และ Seneca.js"
description: "วิธีใช้งาน Seneca.js ช่วยให้สามารถสื่อสารระหว่าง Microservice ได้ดีขึ้น ในช่วง 2-3 ปีที่ผ่านกระแส Microservice ได้มาแรงมากๆ โดยที่ทำงานผมปัจจุบันแอพพลิเคชัน แอพเดียวนี่มี Microservice support อยู่หลังบ้านเป็น 10 ระบบแล้ว ดังนั้นถ้าผมไม่พูดถึงมันจะไม่เทรนนะจ๊ะ แต่จะไม่ลงรายละเอียดในส่วนของทฤษฎีเยอะนะครับเพราะมีพี่ๆ เขียนไว้เยอะแล้ว เรามันสายบู๊อยู่แล้วว่ะฮ่ะฮ่ะ"
date: 2018-01-18 17:11
author: "ป๋าแพะ"
published: true
hero: '{{site.url}}/img/seneca/cover.jpg'
overlay: green
tags: 
 - node.js
 - microservice
 - javascript
 - backend
---

ในช่วง 2-3 ปีที่ผ่านกระแส Microservice ได้มาแรงมากๆ โดยที่ทำงานผมปัจจุบันแอพพลิเคชัน แอพเดียวนี่มี Microservice support อยู่หลังบ้านเป็น 10 ระบบแล้ว ดังนั้นถ้าผมไม่พูดถึงมันจะไม่เทรนนะจ๊ะ แต่จะไม่ลงรายละเอียดในส่วนของทฤษฎีเยอะนะครับเพราะมีพี่ๆ เขียนไว้เยอะแล้ว เรามันสายบู๊อยู่แล้วว่ะฮ่ะฮ่ะ
{: .lead}

# Microservice คืออะไร
<p>
Microservice คือแนวคิดการพัฒนาโปรแกรมแบบแยกโปรแกรมเป็นส่วนเล็กๆ ที่สามารถทำงานแยกออกจากกันได้มี environment แยกออกจากกัน แต่ก็สามารถทำงานร่วมกันได้เพื่อรวมกันกลายเป็นระบบที่ใหญ่ขึ้น ยกตัวอย่างเช่น ระบบธนาคาร (คลาสสิคสุดๆ) 
<!–-break-–>

ในระบบธนาคารหนึ่งธนาคารก็จะมีระบบต่างๆ ประมาณนี้<br/><br/>
	1. payment โอนเงิน / จ่ายเงิน<br/>
	2. balance จัดการยอดเงินผู้ใช้<br/>
	3. deposit ฝากเงิน<br/>
	4. sms แจ้งเตือนฝากถอน<br/><br/>
	อันนี้คือตัวอย่างคร่าวๆ นะครับจากตัวอย่างเราจะเห็นว่าถ้าเป็นสมัยก่อนเขียนไว้ในระบบเดียวกัน (Monolithic) ซึ่งจะว่าดีก็ดีในแบบของเค้านะครับแต่ว่าเราจะไม่พูดเพราะก็คงรู้ๆ กันอยู่แล้ว ข้อเสียที่สำคัญของสถาปัตยกรรมลักษณะนี้คือ<br/><br/>
	1. ถ้าระบบล่มจะล่มหมดเลย<br/>
	2. ทั้งทีมจำเป็นต้องถนัดเครื่องมือเดียวกันเช่น ถ้าใช้ Node.js ก็ต้อง Node.js ทั้งระบบ<br/>
	3. ดูแลรักษายาก<br/><br/>
	นี่แค่ที่ผมคิดออกนะครับเพราะเหตุนี้แนวคิด Mircroservice จึงเกิดขึ้นเพื่อมาแก้ปัญหาของสถาปัตยกรรมแบบเก่าโดยเขียนระบบที่กล่าวมาข้างต้น แยกกันดังนี้<br/><br/>
	1. payment โอนเงิน / จ่ายเงิน อย่างเดียวสายเปย์ ฐานข้อมูลก็เก็บแค่ transaction ที่โอนอย่างเดียว<br/>
	2. balance จัดการยอดเงิน วันๆทำแต่บัญชีอย่างเดียวไม่ต้องทำอะไรละ ฐานข้อมูลเก็บแค่ยอดเงินของผู้ใช้แต่ละคนพอ<br/>
	3. deposit ฝากเงิน ฝากเดียวจ้างกใครให้เงินมาเก็บๆ อย่างเดียวไม่สนใจอะไร ฐานข้อมูลก็เก็บแค่ transaction ที่ถอนอย่างเดียว<br/>
	4. sms แจ้งเตือนฝากถอนฟ้องอย่างเดียวจ้าใครฝากใครถอนโอนเงินให้ใครรู้หมดเก็บแค่ข้อมูล sms ที่ออกไปก็พอ<br/><br/>
	จะเห็นว่าถ้าออกแบบสถาปัตยกรรมระบบในรูปแบบนี้จะช่วยแก้ปัญหาที่กล่าวมาข้างต้นได้ถ้าระบบใดระบบหนึ่งล่มระบบอื่นก็ยังสามารถทำงานอยู่ได้ เช่น โอนเงินล่ม แต่ก็ยังสามารถฝากเงินได้อยู่ดี ทีม balance อยากใช้ PHP ทีม sms จะใช้ node.js ก็ไม่มีปัญหาระบบไหนพังก็ดูแลเป็นระบบๆ ไป
</p>

# ปัญหาของ Microservice

<p>
ถึง Microservice จะดูเทพมากมายขนาดไหนก็ยังมีปัญหาเรื่องความซ้ำซ้อนของระบบและการคุยกันระหว่าง service อยู่ดีเช่น การถอนเงินจากสถาปัตยกรรมของระบบข้างต้นถ้าผู้ใช้จะถอนเงินก็จะมี flow ประมาณนี้
</p>
<img src="{{ site.baseurl }}/img/seneca/seneca-1.png" alt="เขียน Microservice ด้วย Node.js และ Seneca.js"/>
<p>
จาก flow จะเห็นว่าเมื่อโอนเงินเสร็จระบบ payment ต้องส่งข้อความไปถึงสองครั้งเพื่อบอกระบบ sms กับ balance ว่าโอนเงินเสร็จแล้วนะ แล้วถ้าเกิดวันนึงอยากจะเพิ่มระบบ log เพื่อมาเก็บ transaction เข้าออกของทั้งระบบ แต่ละทีมก็ต้องไป implement เพิ่มเพื่อให้ส่งข้อความไปบอกระบบ log ว่าโอนแล้วนะ ฝากแล้วนะ ส่ง sms แล้วนะ อีกวุ่นวายเข้าไปใหญ่
</p>

# แนวคิดของ Seneca.js

<p>
จากปัญหาข้างบนจึงมีคนคิดขึ้นมาได้ว่าถ้างั้นแทนที่เราจะส่งต้องข้อความไปบอกทีละ Microservice ว่าทำนั่นแล้วนะทำนี่แล้วนะ เราก็ให้ Microservice ของเราตะโกนไปว่าฉันทำเสร็จแล้วนะ ทีนี้ Microservice ตัวไหนที่รออยู่ก็สามารถรับข้อความไปได้เลยไม่ต้องไล่ส่งข้อความไปบอกทีละคนอยู่ ถ้าต่อไปมี Microservice อื่นที่ต้องใช้ข้อความนี้ก็เอามาประกอบได้เลยไม่ต้องไปแก้อันเก่าแค่รอรับข้อความก็พอ ซึ่งก็คือ Seneca.jsนั่นเอง Seneca.js เนี่ยเค้าบอกว่าตัวเองคือ
</p>
<blockquote>
Seneca is a microservices toolkit for Node.js.
It helps you write clean, organized code that you can scale and deploy at any time.
</blockquote>
<p>
แปลไทยก็ประมาณว่า seneca คือเครื่องมือที่ช่วยให้สถาปัตยกรรม Microservice ที่ implement ด้วย Node.js ชีวิตดีขึ้นว่างั้นเถอะ (สรุปเอาดื้อๆ) มันจะดีอย่างที่เค้าว่าหรือเปล่า ต่อไปเรามาลองใช้ Seneca.js กันนะครับ โดยเพื่อความสมจริง เราจะทำ Microservice 2 ตัวดังนี้ <br/><br/>
1. interface รับชื่อมาจาก Microservice<br/>
2. hello เอาชื่อมาส่งข้อความกลับไปว่า Hello ‘...'<br/>
</p>

# Install

<pre>
//interface service
mkdir interface
cd interface
npm init
npm install seneca express body-parser --save

//plus service
mkdir plus
cd plus
npm init
npm install seneca --save
</pre>

# Implement Seneca

{% highlight javascript %}
//interface service
const app = require('express')(),
      seneca = require('seneca')();
                    
app.get('/', (req, res) => {
   let name = req.query.name;
   seneca.client().act({service: 'hello', name: name}, (err, result) => {
       if (err) return console.error(err)
       res.send(result.answer)
    });
})
app.listen(3000, function() {
    console.log('Listening on port: 3000');
});
{% endhighlight %}

<p>
จาก Code ข้างบนจะเห็นว่าก็ใช้ express ธรรมดาสิ่งที่อยากให้โฟกัสก็คือ
</p>
{% highlight javascript %}
seneca.client().act({service: 'hello', name: name}, (err, result) => {
	if (err) return console.error(err)
    res.send(result.answer)
});
{% endhighlight %}
<p>
คำสั่ง
</p>
{% highlight javascript %}
seneca.client().act({service: 'hello', name: name}); 
{% endhighlight %}
<p>
คือการสั่งให้ seneca ตะโกนออกไปนะครับ ซึ่งค่าที่ส่งไปเป็นแบบ key-value
</p>
{% highlight javascript %}
(err, result) => {
    if (err) return console.error(err)
    res.send(result.answer)
});
{% endhighlight %}
<p>
คือการ handle สิ่งที่ตอบกลับมา <br/>ต่อมาเราจะมาดูในส่วนของ Microservice hello กันนะครับ
</p>
{% highlight javascript %}
//hello service
const seneca = require('seneca')();
let hello = (msg, reply) => {
    reply(null, {answer: ('Hello ' + msg.name)})
};

seneca.add('service:hello', hello).listen();
{% endhighlight %}
<p>คำสั่ง </p>
{% highlight javascript %}
seneca.add('service:hello', hello).listen(); 
{% endhighlight %}
<p>
คือการบอกให้รอฟังสิ่งที่ service อื่นตะโกนมานะครับโดยจะต้อง match กับ <pre>'service:hello'</pre> เท่านั้นถึงจะทำงาน แล้วเราก็ส่ง function hello เข้าไปเพียงแค่นี้พอเราลองรัน service ทั้งสองตัวและเรียกไปที่ <a href="http://localhost:3000?name=noob" target="_blank">http://localhost:3000?name=noob</a> ก็จะมี response ตอบกลับมาตามรูปนะจ๊ะ
</p>
<img src="{{ site.baseurl }}/img/seneca/reponse.png" alt="เขียน Microservice ด้วย Node.js และ Seneca.js"/>
<p>หากต้องการ fix port, protocol, ip ก็สามารถทำได้ดังนี้ </p>
{% highlight javascript %}
//interface service
.client({ port: 8080, host: '192.168.0.2',type: 'http' })
//hello service
.listen({ port: 8080, host: '192.168.0.2',type: 'http' })
{% endhighlight %}
<p>นอกจากนี้ seneca.js ยังมีลูกเล่น และ plugin อีกเยอะแยะมากมายบรรยายไม่หมดเลยนะครับโดยสามารถเข้าไปดูเพิ่มเติมได้ที่ <a href="http://senecajs.org/" target="_blank">http://senecajs.org/</a></p>

# Conclusion

<p>
จากที่ลองใช้งานดู seneca.js เป็นเครื่องมือที่สามารถประยุกต์ใช้งานได้หลากหลายมากๆ แล้วแต่ว่าจะเอาไปทำอะไร ไม่ว่าเป็น healthcheck, log system, แชร์ config, แชร์ library ระหว่าง service, ส่ง message ไปหา service อื่น, etc. เรียกว่าของมันต้องมี เชื่อผมเถอะแล้วชีิวิตจะง่ายขึ้น! เหมือนเดิมครับตัวอย่าง code สามารถดูได้ <a href="https://github.com/noob-studio/seneca-example" target="_blank">ที่นี่</a> ถ้าชอบก็อย่าลืมกดไลค์กดแชร์ด้วยนะครับบ จุ๊บปาก <3
</p>