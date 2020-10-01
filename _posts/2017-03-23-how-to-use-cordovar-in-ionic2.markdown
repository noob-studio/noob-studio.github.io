---
layout: post
title: "วิธีการใช้งาน Cordova Plugin ใน Ionic2"
description: "ขั้นตอนง่ายๆ เพียง 5 นาทีในการนำ Cordova Plugin มาใช้ใน ionic 2 สวัสดีครับเนื่องจากช่วงนี้งานเยอะมากๆเลย วันนี้เลยขอเขียน How To สั้นๆแต่คิดว่าน่าจะมีประโยชน์สำหรับคนที่ใช้งาน Ionic2 ไม่มากก็น้อยนะครับ"
date: 2017-03-23 00:24:00
author: "ป๋าแพะ"
hero: "/img/cordovar-ionic/cover.jpeg"
categories:
 - mobile
tags: 
 - ionic framework
 - Typescript
 - javascript
 - Angular
---

สวัสดีครับเนื่องจากช่วงนี้งานเยอะมากๆเลย วันนี้เลยขอเขียน How To สั้นๆแต่คิดว่าน่าจะมีประโยชน์สำหรับคนที่ใช้งาน Ionic2 ไม่มากก็น้อยนะครับ
{: .lead}

# Problem

สำหรับคนที่เขียน Hybrid Application ไม่ว่ามือใหม่หรือมือเก๋าก็น่าจะรู้กันดีอยู่แล้วนะครับว่าตัว Javascript ของเราเนี่ยไม่สามารถเข้าถึง Hardware ของเครื่องได้ตรงๆ เหมือนพวก Native Application เพราะฉะนั้นเราจึงต้องใช้ตัวกลางที่เรียกว่า <a href="https://cordova.apache.org/" target="_blank">Cordova</a> (ถ้าค่าย Adobe จะเรียกว่า PhoneGAP) 
<!–-break-–>

ซึ่งเป็น Plugin ที่มีคนเขียนไว้ให้เข้าถึง Hardware ของเครื่องมีให้เลือกใช้งานหลากหลาย แต่ทว่าเหมือนบุญมีแต่กำบังด้วยความที่ Ionic2 ล้ำจัดดันทะลึ่งใช้ TypeScript แทน Javascript ล่ะไอ้ Typescript เนี่ยดันทะลึ่งไม่รู้จักพวก Browser Object บ้างตัวอีกเช่น window screen ทำให้พอเรียกใช้ใน TypeScript ก็จะ error นั่นเองนะจ๊ะ

<blockquote>Javascript ไม่สามารถเข้าถึง Hardware ตรงๆได้ต้องอาศัย Cordovar (PhoneGAP) เข้าถึง Hardware</blockquote>

# Fix it

<img src="{{ site.baseurl }}/img/cordovar-ionic/img01.png" alt="การค้นหา Google ด้วย Selenium">
<span class="caption text-muted">มันคือไรอ่ะหนูไม่รู้จัก T^T</span>

ถ้าเจอปัญหาแบบในรูปข้างต้นนะครับสรุปวิธีแก้ง่ายๆเลยนะครับประกาศสิ่งที่มันต้องการในไฟล์ที่จะใช้งานหลัง import ซ่ะเช่น ในรูปข้างบนจะเห็นว่ามันไม่รู้จัก screen.orientation ก็แนะนำให้มันรู้จักซ่ะ เช่น

{% highlight javascript %}
import ...

declare var screen:any;

...
{% endhighlight %}

เป็นอันเรียบร้อยนะจ๊ะพอลองรันอีกครั้งจะเห็นว่าเขียวแล้วหลังจากนั้นก็เขียน Code ได้ตามปกตินะจ๊ะ
<img src="{{ site.baseurl }}/img/cordovar-ionic/img02.png" alt="การค้นหา Google ด้วย Selenium">
<span class="caption text-muted">ไม่งอแงแล้วนะจ๊ะ</span>

# Conclusion 

<ul>
<li>ติดตั้ง Cordovar Plugin ที่ต้องการใช้งาน</li>
<li>ประกาศสิ่งที่มันต้องการตามตัวอย่างข้างบน</li>
<li>เขียน Code อย่างมีความสุข</li>
</ul>
<p>ศึกษาเพิ่มเติม <a href="https://cordova.apache.org/" target="_blank">Cordova</a></p>
<blockquote>
หมายเหตุ บทความนี้ใช้กับ Library ของ Javascript ที่จะใช้งานบน Angular ก็ได้นะจ๊ะ
</blockquote>
