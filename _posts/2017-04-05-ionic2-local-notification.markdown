---
layout: post
comments: true
title: "วิธีทำ Notification (แจ้งเตือน) ให้ Ionic2 แบบไม่ต้องมี server"
description: "วิธีการ push notification สำหรับ Ionic 2 แบบไม่ต้องมี server มีแค่ Ionic 2 ก็พอแล้วนะจ๊ะ ต่อเนื่องจากบทความที่แล้วนะครับกับการส่งแจ้งเตือนให้แอพพลิเคชันด้วย Firebase และ Node.js หลายคนอาจจะรู้สึกว่าโหยทำไมมันเยอะแยะขนาดนี้เนี่ยแค่อยากแจ้งเตือนให้เข้ามาแอพกูหน่อยวันล่ะครั้งก็ยังดีไรแบบนี้ (คิดถึงแอพเกมที่เวลาไม่ได้เข้านานแล้วส่งแจ้งเตือนมาว่า หมู่บ้านต้องการคุณ! ประมาณนี้จะสังเกตุเห็นว่าการแจ้งเตือนแบบนี้ไม่ได้ต้องการนำเสนอข้อมูลเหมือนกันแจ้งเตือนปกติเราแค่อยากจะเตือนให้ผู้ใช้เข้ามาในแอพเราหรืออะไรก็ว่าไปเป็นที่มาของการทำ Local Notification (แจ้งเตือนจากตัวแอพเองนั่นเองนะจ๊ะ)"
date: 2017-03-28 22:41:00
author: "ป๋าแพะ"
published: true
hero: "/img/ionic-local-notification/cover.jpg"
categories:
 - mobile
tags: 
 - Ionic framework
 - Typescript
 - javascript
---
ต่อเนื่องจากบทความที่แล้วนะครับกับ <a href="{{ site.baseurl}}{% post_url 2017-03-28-ionic2-notification %}">การส่งแจ้งเตือนให้แอพพลิเคชันด้วย Firebase และ Node.js </a> 
{: .lead}

หลายคนอาจจะรู้สึกว่าโหยทำไมมันเยอะแยะขนาดนี้เนี่ยแค่อยากแจ้งเตือนให้เข้ามาแอพกูหน่อยวันล่ะครั้งก็ยังดีไรแบบนี้ (คิดถึงแอพเกมที่เวลาไม่ได้เข้านานแล้วส่งแจ้งเตือนมาว่า หมู่บ้านต้องการคุณ!) ประมาณนี้จะสังเกตุเห็นว่าการแจ้งเตือนแบบนี้ไม่ได้ต้องการนำเสนอข้อมูลเหมือนกันแจ้งเตือนปกติเราแค่อยากจะเตือนให้ผู้ใช้เข้ามาในแอพเราหรืออะไรก็ว่าไปเป็นที่มาของการทำ Local Notification (แจ้งเตือนจากตัวแอพเองนั่นเองนะจ๊ะ)
<!–-break-–>

# Start Ionic

เอาล่ะครับเรามาเริ่มกันเถอะขั้นตอนก็ไม่มีไรมากเหมือนเดิมครับสร้างแอพขึ้นมาและ add cordova plugin ตามนี้ขึ้นไป
{% highlight javascript %}
ionic start ionic-local-notification blank --v2
cd ionic-local-notification
ionic platform add android
ionic plugin add cordova-plugin-background-mode
npm install --save @ionic-native/background-mode
ionic plugin add de.appplant.cordova.plugin.local-notification
npm install --save @ionic-native/local-notifications
{% endhighlight %}

<ul>
<li>cordova-plugin-background-mode ใช้สำหรับให้แอพทำงานได้แม้ user จะออกจากแอพไปแล้ว</li>
<li>local-notification คือพระเอกของงานนี้นะครับใช้สำหรับแจ้งเตือนแบบไม่ต้องมี server นั่นเอง</li>
</ul>
ต่อมาให้เปิดไฟล์ src/app/app.component.ts ขึ้นมาและเพิ่ม Code ไปดังนี้

{% highlight javascript %}
import { BackgroundMode } from '@ionic-native/background-mode';
import { LocalNotifications } from '@ionic-native/local-notifications';
{% endhighlight %}

import module ที่ต้องการใช้นะครับโดยรายละเอียดก็ตามที่เขียนอธิบายไว้ข้างบน

{% highlight javascript %}
constructor(
    ..., 
    private bgMode: BackgroundMode,
    private localNoti: LocalNotifications
) 
{% endhighlight %}

ประการศตัวแปร private ชื่อ bgMode, localNoti โดยนำค่ามาจาก Module BackgroundMode และ LocalNotifications ตามลำดับ ต่อมาเพิ่มการทำงานเข้าไปใน constructor ดังนี้

{% highlight javascript %}
constructor(
    ...
){
    platform.ready().then(() => {
      ...
      this.bgMode.enable();
      this.bgMode.on('activate').subscribe(() => {
          var today = new Date();
          var pm_6 = new Date();
          pm_6.setDate(today.getDate());
          pm_6.setHours(18);
          pm_6.setMinutes(0);
          pm_6.setSeconds(0);
          var at_6_pm = new Date(pm_6);
          this.localNoti.schedule({
            text: 'แจ้งเตือนตอน 6 โมง',
            firstAt: at_6_pm,
            every: 'day'
          });
      })
    });
}
{% endhighlight %}

เพียงแค่นี้ก็เป็นอันเสร็จแล้วนะจ๊ะสำหรับการทำงานที่เราใส่ไปมีดังนี้นะครับ

{% highlight javascript %}
this.bgMode.enable();
{% endhighlight %}

เปิด Mode การทำงานแบบ background

{% highlight javascript %}
this.bgMode.on('activate').subscribe(() => {
...
})
{% endhighlight %}

เป็น event ที่เกิดขึ้นเมื่อ application ทำงานแบบ background mode

{% highlight javascript %}
var today = new Date();
var pm_6 = new Date();
pm_6.setDate(today.getDate());
pm_6.setHours(18);
pm_6.setMinutes(00);
pm_6.setSeconds(0);
var at_6_pm = new Date(pm_6);
{% endhighlight %}

สร้างวันเวลาสำหรับแจ้งเตือนโดยให้ตั้งเป็น 6 โมง

{% highlight javascript %}
this.localNoti.schedule({
    text: 'แจ้งเตือนตอน 6 โมง',
    firstAt: at_6_pm,
    every: 'day'
});
{% endhighlight %}

ตั้งค่าการแจ้งเตือนโดยมีรายละเอียดดังนี้
<ul>
<li>text ข้อความที่จะให้แจ้งเตือน</li>
<li>firstAt ให้แจ้งเตือนเมื่อไหร่</li>
<li>every ให้แจ้งเตือนเมื่อไหร่ใส่ Day ให้แจ้งเตือนทุกวันนั่นเอง</li>
</ul>


