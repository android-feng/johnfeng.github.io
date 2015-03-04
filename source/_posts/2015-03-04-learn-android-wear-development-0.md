---
layout: post
title: "Learn Android Wear development - 0"
date: 2015-03-04 11:31:11 +0800
comments: true
share: true
categories: androidwear android moto360
---

> Through the first couple days with my Moto360 , I quickly fall in love with the [Android Wear](https://www.android.com/wear/) system. It doesn't only look fancy , but indeed the combination of the Modern Tech & Art as well . Thus, I wanna start a small Android Wear project following days in order to learn more about this wonderful platform and try to be familiar with it. 

First thing first, Let's go thought the official dev guide.

---
##[Official Guide](https://developer.android.com/training/building-wearables.html) --- [1. Adding Wearable Features to Notifications](https://developer.android.com/training/wearables/notifications/index.html) 

<!--more-->

	How to build handheld notifications that are synced to and look great on wearables.

There two types of notifications in wear system, one is the **default** card , which will automatically sync received messages to wearable devices with some simple default actions. Another is the **customized** one , which has developer customized layouts and actions, and can do more thing directly from wearable devices.

- 0.import dependances

*following codes are needed to be set up before starting* :

{% codeblock lang:java in gradle %} compile "com.android.support:support-v4:20.0.+"
{% endcodeblock %}

{% codeblock lang:java in activity || fragment ||... %} import android.support.v4.app.NotificationCompat;
import android.support.v4.app.NotificationManagerCompat;
import android.support.v4.app.NotificationCompat.WearableExtender;{% endcodeblock %}

and what you can do with customized notification:

- 1.[Create Notifications with the Notification Builder
](https://developer.android.com/training/wearables/notifications/creating.html#NotificationBuilder)
	
	**Just the same with the notifications built on handhelds.**
	
- 2.[Add action Buttons](https://developer.android.com/training/wearables/notifications/creating.html#ActionButtons)

 {% codeblock lang:java %}	new NotificationCompat.Builder(this)
        .setSmallIcon(R.drawable.ic_event)
        .setContentTitle(eventTitle)
        .setContentText(eventLocation)
        .setContentIntent(viewPendingIntent)
        
        // which did the job
        .addAction(R.drawable.ic_map,
                getString(R.string.map), mapPendingIntent);
{% endcodeblock %}

- 3.[Specify Wearable Only Actions](https://developer.android.com/training/wearables/notifications/creating.html#SpecifyWearableOnlyActions)

	- use the [ NotificationCompat.WearableExtender.addAction (NotificationCompat.Action action)](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.WearableExtender.html#addAction(android.support.v4.app.NotificationCompat.Action)) method to add your own action to notifications.

- 4.[Add a Big View](https://developer.android.com/training/wearables/notifications/creating.html#BigView)

	- On a handheld device, users can see the big view **content** by expanding the notification. On a wearable device, the big view content is **visible** by default.
	
	- To add the extended content to your notification, call setStyle() on the [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.BigTextStyle.html) object, passing it an instance of either BigTextStyle or InboxStyle.



- 5.[Add Wearable Features For a Notification
](https://developer.android.com/training/wearables/notifications/creating.html#AddWearableFeatures)
- 6.[Deliver the Notification](https://developer.android.com/training/wearables/notifications/creating.html#Deliver)

	- use the NotificationManagerCompat API instead of NotificationManager:

 {% codeblock lang:java %}	// Get an instance of the NotificationManager service
	NotificationManagerCompat notificationManager =
	        NotificationManagerCompat.from(mContext);
	
	// Issue the notification with notification manager.
	notificationManager.notify(notificationId, notif);
{% endcodeblock %}


---
##One more thing - Useful 3rd Party Libs 

####**Give all credits to [Michal Tajchert](https://github.com/tajchert). Really nice work!!!**

###1. [BusWear - EventBus for Android Wear](https://github.com/tajchert/BusWear)
![BusWear](https://raw.githubusercontent.com/tajchert/BusWear/master/mobile/src/main/res/drawable-xxxhdpi/ic_launcher.png)

>BusWear {%gemoji bus%}{%gemoji watch%} is a simple library for EventBus to support Android Wear devices. Just adding one line of code lets you get synchronized event buses on Wear and mobile platform.

![architecture](https://raw.githubusercontent.com/tajchert/BusWear/master/docs/diagram_simple.png)

>####What is EventBus?
A great multi-purpose tool for Android apps, way of triggering some events in separate Activity, Fragment, Service etc. EventBus, origin of that project or Otto.

if you're already familiar with [Android EventBus](https://github.com/greenrobot/EventBus), it works just in the same pattern, syncing data between wear and handhelds like a boss.

###2. [ExceptionWear - library for handling Exceptions on Android Wear](https://github.com/tajchert/ExceptionWear)

>**ExceptionWear** {%gemoji exclamation%}{%gemoji watch%} is very simple library to solve problem of not **passing** exceptions from Android Wear **devices** to the **phone**. So if you release an app for smartwatches to Google Play and it will crash (and it will) you won't get any information about it (sic!).






 
