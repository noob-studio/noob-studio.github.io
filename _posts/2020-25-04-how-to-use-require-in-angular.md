---
layout: post
title: "วิธีใช้งาน require('...') ใน Angular 8"
description: > 
    บทความนี้เป็นวิธีง่ายๆ สำหรับคนที่ต้องการเรียกใช้งาน node module ต่างๆ ใน Angular 8 ผมเชื่อว่าหลายๆ คนก็น่าจะรู้แล้วแต่ว่า บางคนก็ยังไม่รู้นะครับ
date: 2020-04-25 15:00:00
author: freeweed
hero: "/img/node-require/cover.jpg"
tags: 
 - Tip&Trick
 - Angular 8
 - Ionic
 - Typescript
  - Programming101
---

# Problem
เชื่อว่าหลายๆ คนคงเคยประสบปัญหาในการพยายามจะใช้ node module บางตัวใน angular 8 นะครับเพราะว่าพอเราใช้ ***require*** เรียก module เข้ามาพอรันก็จะเจอ error ว่า
{: .lead}

{% highlight javascript %}
Cannot find name 'require'. Do you need to install type definitions for node? 
Try `npm i @types/node` and then add `node` to the types field in your tsconfig.
{% endhighlight %}

<!–-break-–>

# Solution

วิธีแก้ก็ง่ายๆ เปิด Termianl ขึ้นมา พิมพ์ตามที่มันบอกเลยจ้า

{% highlight javascript %}
npm i @types/node --save
{% endhighlight %}

หลังจากนั้นให้เปิดไฟล์ ***tsconfig.app.json*** ขึ้นมาแล้วเพิ่มโค้ดเข้าไปดังนี้

{% highlight json %}
"compilerOptions": {
    ...
    "types": [ "node" ],
    "typeRoots": [ "node_modules/@types" ]
    ...
}
{% endhighlight %}

แค่นี้ก็เรียบร้อยแล้วจ้าา หวังว่าเพื่อนๆจะได้ความรู้จากบทความนี้ไม่มากก็น้อยนะครับ

ปล. ขอให้เพื่อนๆ พี่ๆ น้องๆ ทุกคนอยู่บ้านปลอดภัยหายจาก Covid นะครับ