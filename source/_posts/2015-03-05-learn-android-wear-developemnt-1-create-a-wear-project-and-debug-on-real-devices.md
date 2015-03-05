---
layout: post
title: "Learn Android Wear Developemnt - 1. Create a wear Project and Debug on Real Devices"
date: 2015-03-05 12:09:24 +0800
comments: true
share: true
categories: androidwear android debug 
---

##1. Create a new Project

It's quite easy when you wanna create a new Android wear project using Android studio. All you need is just hit the new project and choose the "Android wear" label.

![](https://github.com/JohnFeng/johnfeng.github.io/blob/master/images/post/2015-03-05/creat.png?raw=true) 

The relationship between Handheld App and Wear App will be told in the "**Package**" Section.

And now you can browser all available items in project stucture:
![](https://github.com/JohnFeng/johnfeng.github.io/blob/master/images/post/2015-03-05/create1.png?raw=true)

This project contains two modules: Mobile & Wear. And Wear module should be wrapped inside the gradle file of the Mobile Module.

<!--more-->

##2. Debug & Run on Real Device(Moto 360)
Since Moto360 don't have any usb port, apps have to be  debugged over bluetooth.

####[a.  Set up Debugging](https://developer.android.com/training/wearables/apps/bt-debugging.html#SetupDevices)
>####1. Enable USB debugging on the handheld:
>* Open the Settings app and scroll to the bottom.
>* If it doesn't have a Developer Options setting, tap About Phone (or >About Tablet), scroll to the bottom, and tap the build number 7 times.
>* Go back and tap Developer Options.
>* Enable USB debugging.
><hr>
>####2.Enable Bluetooth debugging on the wearable:
>* Tap the home screen twice to bring up the Wear menu.
>* Scroll to the bottom and tap Settings.
>* Scroll to the bottom. If there's no Developer Options item, tap About, >and then tap the build number 7 times.
>* Tap the Developer Options item.
>* Enable Debug over Bluetooth.

####[b.Setup Devices](https://developer.android.com/training/wearables/apps/bt-debugging.html#SetupDevices)

After you enabled **Debugging over Bluetooth** in handhelds Android wear -> "settings" page ,and add following code:

{%codeblock %}adb forward tcp:4444 localabstract:/adb-hub
adb connect localhost:4444
{%endcodeblock%}

And here is the output:
![](https://github.com/JohnFeng/johnfeng.github.io/blob/master/images/post/2015-03-05/debug.png?raw=true) 

And in Android Studio's DDMS:
![](https://github.com/JohnFeng/johnfeng.github.io/blob/master/images/post/2015-03-05/debug1.png?raw=true)

####c.Run Hello World

Since  there are two sub-modules in this project, the specific module has to been selected before run, here is in Android Studio:

![](https://github.com/JohnFeng/johnfeng.github.io/blob/master/images/post/2015-03-05/debug2.png?raw=true)

###[d. Package](https://developer.android.com/training/wearables/apps/packaging.html)

Both sub modules have to use the same package ,and wearable module has to be wrapped inside of the handheld app in this way:

{%codeblock %}dependencies {
   compile 'com.google.android.gms:play-services:5.0.+@aar'
   compile 'com.android.support:support-v4:20.0.+''
   wearApp project(':wearable')
}
{%endcodeblock%}

And then set up the **Signed APK** follow the [official guide](https://developer.android.com/training/wearables/apps/packaging.html).

And See the Hello World:

{% img https://github.com/JohnFeng/johnfeng.github.io/blob/master/images/post/2015-03-05/debug3.jpg?raw=true 400 400 %}