---
layout: post
title: "เขียน RESTful API ใน Node.js แบบ 'โคตรไว' ด้วย Loopback"
description: "วิธีการเขียน RESTful API ด้วยความเร็วแสงด้วย Loopback เนื่องจากผมได้มีโอกาศไปรับงานนึงเกี่ยวกับการจัดการ Stock สินค้าซึ่งจาก Scale งานเนี่ยน่าจะใช้เวลาพัฒนาประมาณ 2 อาทิตย์แต่ลูกค้าแบบว่าเร่งมากบอกน้องพี่ขอ 7 วันได้ไหม!! แม่เจ้าาาา รีบไปไหนเนี่ยพ่อคู๊ณณ แต่ถือเป็นโชคดีที่ก่อนหน้าที่ทำงานของผมได้ให้ผมทำ research เกี่ยวกับ node.js framework ตัวนึงชื่อว่า loopback ซึ่งหลังจากได้ลองใช้ขอบอกเลยว่ามันดีมากเลยอ่ะแกรร แบบว่าของมันต้องใช้อ่ะแกรร ว่าแล้วเราก็มาลองใช้งานกันเลยเถอะ"
date: 2017-12-04 13:52
author: "freeweed"
published: true
hero: '{{site.url}}/img/loopback/cover.jpg'
categories:
 - backend
 - framework
tags: 
 - node.js
 - javascript
---
<!-- <img src="{{ site.baseurl }}/img/loopback/cover.jpg" alt="สอนใช้งาน loopback"/> -->
เนื่องจากผมได้มีโอกาศไปรับงานนึงเกี่ยวกับการจัดการ Stock สินค้าซึ่งจาก Scale งานเนี่ยน่าจะใช้เวลาพัฒนาประมาณ 2 อาทิตย์แต่ลูกค้าแบบว่าเร่งมากบอกน้องพี่ขอ 7 วันได้ไหม!! 
{: .lead}

แม่เจ้าาาา รีบไปไหนเนี่ยพ่อคู๊ณณ แต่ถือเป็นโชคดีที่ก่อนหน้าที่ทำงานของผมได้ให้ผมทำ research เกี่ยวกับ node.js framework ตัวนึงชื่อว่า <a href="http://loopback.io" target="_blank">loopback</a> ซึ่งหลังจากได้ลองใช้ขอบอกเลยว่ามันดีมากเลยอ่ะแกรร แบบว่าของมันต้องใช้อ่ะแกรร ว่าแล้วเราก็มาลองใช้งานกันเลยเถอะ 
<!–-break-–>

<p>
    loopback คือ Node.js framework สำหรับสร้าง REST API อย่างรวดเร็วพร้อมเครื่องมือสำหรับสร้าง Model พร้อม validation และอื่นๆ อีกมากมายอย่างครบวงจรเรียกได้ว่าช่วยประหยัดเวลาในการพัฒนาเป็นอย่างมาก โดย ขั้นตอนการใช้ loopback เบื้องต้นมีดังนี้
</p>

# Setup

<p>สำหรับเริ่มใช้งานก็แบบชิลๆ เปิด terminal ขึ้นมา และพิมพ์คำสั่งดังนี้</p>
<pre>
npm install -g loopback-cli
lb
</pre>
<p>เมื่อพิมพ์คำสั่งตามนี้จะปรากฏหน้าต่างให้ setup ข้อมูล api ดังนี้</p>
<pre>
What's the name of your application?  hello-loopback (ชื่อของ api)
Enter name of the directory to contain the project: hello-loopback (ไฟล์ต่างๆไว้ในโฟรเดอร์ชื่ออะไรถ้าไม่ใส่จะใส่ในไดเร็กทอรี่เดียวกับชื่อ  api)
Which version of LoopBack would you like to use? เวอร์ชันของ loopback ในที่นี้เลือกเวอร์ชัน 3.x
What kind of application do you have in mind? เลือกค่าพื้นฐานของ api ในที่นี้เลือก empty-server
</pre>
<img src="{{ site.baseurl }}/img/loopback/setup.png" alt="สอนใช้งาน loopback"/>
<p>เมื่อใส่รายละเอียดต่างๆ เสร็จแล้ว loopback จะทำสร้างไฟล์ต่างๆ ตามที่เรา setup ไว้เป็นอันเสร็จขั้นตอนแรก</p>

# Create Model

<p>เมื่อเรา setup เสร็จแล้วเราก็จะมาสร้าง model แรกกันโดยมีขั้นตอนดังนี้</p>
<pre>
cd hello-loopback
lb model
</pre>
<p>เมื่อเราพิมพ์คำสั่ง lb model บรรทัดแรกจะขึ้นตัวแดงๆ มาไม่ต้องตกใจบรรทัดต่อมามันจะถามว่า</p>
<pre>
Enter the model name: blog (ให้เราใส่ชื่อ model ของเรา)
Select model's base class: PersistedModel (เลือกประเภทของ model ที่เราจะสร้าง)
Expose blog via the REST API? Y (model นี้เป็น REST api หรือเปล่า)
Custom plural form: /blog (path ของ model นี้)
Common model or server only? common (model นี้จะเอาไว้ที่ไหนในไดเรคทอรี่ server หรือ common)
</pre>
<img src="{{ site.baseurl }}/img/loopback/model.png" alt="สอนใช้งาน loopback"/>
<p>เมื่อ setup model เสร็จมันก็จะให้เราใส่ property ของ model โดยมีรายละเอียดดังนี้</p>
<pre>
Property name: name (ชื่อของ property)
Property type: string (ประเภทของ property)
Required? Y (จำเป็นต้องใส่หรือไม่)
Default value[leave blank for none]: (ค่า default ของ property นี้)
</pre>
<img src="{{ site.baseurl }}/img/loopback/property.png" alt="สอนใช้งาน loopback">
<p>เมื่อเราใส่ property เสร็จมันก็จะวนถามใหม่เรื่อยๆ ให้เราใส่เรื่อยๆ จนกว่าจะพอใจเมื่อพอใจแล้วตรง Property name: ให้เว้นว่างไว้มันก็จะออกจากการสร้าง model</p>
<p>เมื่อเราเข้าไปดูในไดเรคทอรี่ common/models ก็จะเห็นไฟล์ชื่อ model ของเรา 2 ไฟล์คือ .js (จะอธิบายต่อไป) และ .json เก็บข้อมูลต่างๆ ของ model เรา</p>

# Connect database

<p>เมื่อเราสร้าง model เสร็จแล้วสิ่งที่เราต้องทำต่อมาคือเชื่อมต่อกับฐานข้อมูลซึ่งสามารถทำได้โดยพิมพ์คำสั่ง </p>
<pre>
lb datasource
</pre>
<p>หลังจากนั้นมันจะขึ้นคำถามดังนี้</p>
<pre>
Enter the datasource name: mysql (ให้ใส่ชื่อ datasource อะไรก็ได้)
Select the connector for mysql: MySQL (ให้เลือกฐานข้อมูลในที่นี้ใช้ MySQL)
Connection String url to override other settings (eg: mysql://user:pass@host/db): mysql://root@127.0.0.1/test (ใส่รายละเอียดการเชื่อมต่อไม่ต้องใส่ก็ได้)
host: 127.0.0.1 (ใส่ host ของฐานข้อมูล)
port: 3306 (ใส่ port สำหรับเชื่อมต่อฐานข้อมูล)
user: root (ใส่ username)
password: (ใส่ password)
database: test (ใส่ฐานข้อมูลที่ต้องเชื่อมต่อ)
Install loopback-connector-mysql@^2.2: Y (ต้องการติดตั้ง loopback-connector-mysql หรือไม่)
</pre>
<img src="{{ site.baseurl }}/img/loopback/datasource.png" alt="สอนใช้งาน loopback">
หลังจากนั้นไปที่ไฟล์ server/model-config.js แก้ตรง
```js
...
datasource: null
...
```

เป็น
~~~js
...
datasource: ‘mysql’ (ชื่อของ datasource ที่เราสร้างขึ้น)
...
~~~
<p>เพียงเท่านี้ก็เสร็จเรียบร้อยในขั้นตอนการเชื่อมต่อกับฐานข้อมูลแล้วต่อไปเราก็จะมาสร้างตารางจาก model ของเรากัน</p>

# Automigrate

<p>เมื่อเรามี model แล้ว เชื่อมต่อฐานข้อมูลแล้ว ต่อมาเราก็จะทำการสร้างตารางสำหรับ model ของเรากันโดยเปิด editor ขึ้นมาแล้วสร้างไฟล์ชื่ออะไรก็ได้ใส่ไว้ในไดเรคทอรี่ server/boot โดยใส่ code ดังนี้</p>
~~~js
module.exports = (app) => {
	app.dataSources.mysql.automigrate('blog', (err) => {
		if (err) throw err;
		app.models.blog.create([{
			name: 'test1',
			author: 'mike',
			date: Date()
		}, {
			name: 'test2',
			author: 'mike',
			date: Date()
		}, {
			name: 'test3',
			author: 'mike',
			date: Date()
		}, ], (err, blog) => {
			if (err) throw err;
			console.log('Models created: \n', blog);
		});
	});
};
~~~
<p>code ด้านบนอธิบายได้ดังนี้</p>
~~~js
app.dataSources.mysql.automigrate('blog', (err) => {
  ...
});
~~~
<p>เป็นฟังชันสำหรับ migrate ฐานข้อมูลของ loopback โดยส่ง parameter เข้าไปสองตัวคือ model ที่ต้องการ migrate และ callback</p>
~~~js
app.models.blog.create([{
	name: 'test1',
	author: 'mike',
	date: Date()
},
        ...
]);
~~~
<p>คือการเพิ่มข้อมูลใส่ในตารางที่เราทำการ migrate <span style="color:red">** มีข้อควรระวังคือหากในตารางนั้นมีข้อมูลอยู่แล้วจะถูกเขียนทับ **</span></p>
~~~js
...
(err, blog) => {
	if (err) throw err;
	console.log('Models created: \n', blog);
}
...
~~~
<p>คือการ handle callback เมื่อทำการ migrate สำเร็จ หลังจากนั้นเมื่อเราเตรียมทุกอย่างพร้อมเราจึงเริ่มทำรัน REST api ของเราโดยพิมพ์คำสั่ง</p>
<pre>
node .
</pre>
<p>เมื่อเข้าไปดูในฐานข้อมูลก็จะพบตารางชื่อตาม model ของเราโดยที่มีข้อมูลตามที่เรา insert เข้าไปดังภาพ</p>
<img src="{{ site.baseurl }}/img/loopback/database1.png" alt="สอนใช้งาน loopback">

# ทดลองใช้ API

<p>หลังจากที่เรารัน REST api แล้วให้ลองเปิด browser เข้าไปที่ path 127.0.0.1:3000/explorer จะพบหน้าตาสำหรับทดลอง api ดังภาพ</p>
<img src="{{ site.baseurl }}/img/loopback/explorer.png" alt="สอนใช้งาน loopback">
<p>หากเราต้องเพิ่มข้อมูลเข้าสู่ตารางให้เลือกที่ post ใส่ข้อมูลลงไป และกดที่ try out ดังภาพ</p>
<img src="{{ site.baseurl }}/img/loopback/post.png" alt="สอนใช้งาน loopback">
<p>หลังจากนั้นเมื่อเราเข้าไปดูที่ฐานข้อมูลก็จะเห็นข้อมูลที่เราเพิ่มไปเมื่อกี้เข้ามา</p>
<img src="{{ site.baseurl }}/img/loopback/database.png" alt="สอนใช้งาน loopback">
<p>หรือหากลองเปิด Browser แล้วเข้าไปที่ http://localhost:3000/api/blog ก็จะเห็นข้อมูลในฐานข้อมูลเราดังภาพเพียงแค่นี้เราก็สร้าง RESTful API สำหรับ CRUD เสร็จแล้วนะครับต่อไปเราจะมาทำ Custom API กัน</p>
<img src="{{ site.baseurl }}/img/loopback/browser_open.png" alt="สอนใช้งาน loopback">

# Custom API

<p>เพราะในชีวิตจริง API เราของไม่ได้มีแต่ CRUD ต้องฟังชันการทำงานอื่นๆ ซึ่งใน loopback เนี่ยเราสามารถทำ API เอกได้ง่ายๆดังนี้เปิดไฟล์ Model ของเราขึ้นมาซึ่งไฟล์ Model ของเราจะอยู่ที่ common/models/ชื่อโมเดล.js ซึ่งพอเราเปิดขึ้นมาก็จะเป็นไฟล์ไม่ได้ทำๆ (ไฟล์เปล่าๆ) ที่มีข้อมูลหล่อมแหล่มดังนี้</p>
~~~js
'use strict';

module.exports = function(Blog) {

};
~~~
<p>ให้เราสอดใส่ Code เข้าไปดังนี้</p>
~~~js
module.exports = function(Blog) {

	Blog.hello = function(msg, callback){
		callback(null, "Hello "+msg)
	}
	Blog.remoteMethod(
		'hello', {
			http: {
				path: '/hello',
				verb: 'get'
			},
			accepts: {
				arg: 'msg', 
				type: 'string'
			},
			returns: {
				arg: 'word',
				type: 'string'
			}
		}
	);
};
~~~
<p>Code ข้างบนมีการทำงานคือเมื่อมีคน route ผ่าน method get มาที่ http://localhost:3000/api/blog/hello พร้อม parameter ชื่อ msg ก็จะตอบกลับดังรูปตัวอย่าง</p>
<img src="{{ site.baseurl }}/img/loopback/example.png" alt="สอนใช้งาน loopback">
<p>โดย Code ข้างบนมีการทำงานดังนี้</p>
~~~js
module.exports = function(Blog) {
...
};
~~~
<p>export module ของเราส่วน Parameter Blog ที่เข้ามาก็คือ model ของเราพร้อมฟังก์ชันเสริมต่างๆ นาๆที่ loopback เพิ่มมาให้</p>
~~~js
Blog.hello = function(msg, callback){
	callback(null, msg)
}
~~~
<p>เพิ่มฟังชันชื่อ hello เข้าไปใน model Blog โดยที่ parameter ที่ส่งเข้าไปตัวแรก msg คือค่าที่รับมาจาก request และ callback นี่ loopback ส่งเข้ามาให้เราสำหรับส่งข้อมูลกลับไปเมื่อเราทำงานเสร็จแล้ว จะสังเกตุเห็นว่าใน loopback รับ parameter 2 ตัว ตัวแรกที่เป็น null คือerr ในกรณีที่มี err ตัวที่สองคือค่าที่เราจะ response ออกไป</p>
~~~js
Blog.remoteMethod(
    'hello', {
        http: {
            path: '/hello',
            verb: 'get'
        },
        accepts: {
            arg: 'msg', 
            type: 'string'
        },
        returns: {
            arg: 'word',
            type: 'string'
        }
    }
);
~~~
<p>คือการตั้งค่า Custom API ของเรา </p>
~~~js
'hello', {
...
}
~~~
<p>คือบอกว่าตั้งค่าของฟังก์ชันไหน</p>
~~~js
...
http: {
	path: '/hello',
	verb: 'get'
},
...
~~~
<p>คือการเซ็ทพาท และ method ที่เข้ามา</p>
~~~js
...
accepts: {
	arg: 'msg', 
	type: 'string'
},
...
~~~
<p>คือการเซ็ท parameter ที่จะเข้ามา arg คือชื่อ type คือประเภท</p>
~~~js
...
returns: {
	arg: 'word',
	type: 'string'
}
...
~~~
<p>คือการเซ็ทข้อมูล response ที่จะออกไปก็เหมือนเดิมครับ arg คือชื่อ และ type คือประเภท</p>

# Conclusion

<p>เป็นอันเสร็จเรียบร้อยแล้วนะครับสำหรับการใช้ loopback สร้าง RESTful API เบื้องต้นในตอนต่อไป (ถ้าไม่ลืม) จะมาสอนใช้งาน loopback ร่วมกับ Angular 2 กันนะจ๊ะจุ๊ปปาก <3 สามารถดูตัวอย่าง source code ได้ <a href="https://github.com/freeweed/loopback-example" target="_blank">ที่นี่</a> นะจ๊ะ</p>