---
layout: post
title: "Weibo Android SDK 3.0.1 load libweibosdkcore.so Crash"
date: 2015-02-25 16:51:47 +0800
comments: true
share: true
categories: crash android gradle weibo-sdk libweibosdkcore.so 
---
---
Question
------
 I was trying to update my App's Weibo-SDK version from 2.x to 3.0.1 *(in order to fix the SSO crash when authorizing apps without Weibo's client, happens on **Lollipop**)*, building process failed with log : Couldn't load sdk from loader dalvik ... **libweibosdkcore.so** file.

After pasting the "libweibosdkcore.so" file into "libs/armeabi-v7a/" folder, gradle still could not pass the building process.

<!-- more -->

Fix
------
Add the following codes into the "build.gradle" file, inside of  the "android" block:

  ```java
    //noinspection all
    task copyNativeLibs(type: Copy) {
        // third party lib so                
        from(new File(projectDir, 'libs')) { include 'armeabi/*.so','armeabi-v7a/*.so' }        
        into new File(buildDir, 'native-libs')
    }
    tasks.withType(JavaCompile) {
        compileTask ->
            //noinspection all
            compileTask.dependsOn copyNativeLibs
    }
    
    //noinspection all
    tasks.withType(com.android.build.gradle.tasks.PackageApplication) {
        pkgTask ->
            pkgTask.jniFolders = new HashSet()
            pkgTask.jniFolders.add(new File(buildDir, 'native-libs'))
    }
  ```

Problem Solved.

From <[镕羽's Blog](http://blog.sina.com.cn/s/blog_92814aa60102vhv1.html)>
