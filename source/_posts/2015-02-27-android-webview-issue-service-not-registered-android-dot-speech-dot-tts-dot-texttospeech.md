---
layout: post
title: "Android WebView Issue - Service not registered: android.speech.tts.TextToSpeech"
date: 2015-02-27 12:14:34 +0800
comments: true
categories: android issue webview exception 
---
---

Problem: 
-----

######{%gemoji cry%}

Noticed a weird crash related with Android TTS, which I didn't wish to use, when I tried to initial or destroy a webview loaded from XML file.
Here is the console output:

{% codeblock lang:java Console Out Put %}
	   java.lang.IllegalArgumentException: Service not registered: android.speech.tts.TextToSpeech$Connection@41519670
       at android.app.LoadedApk.forgetServiceDispatcher(LoadedApk.java:937)
       at android.app.ContextImpl.unbindService(ContextImpl.java:1521)
       at android.content.ContextWrapper.unbindService(ContextWrapper.java:490)
       at android.speech.tts.TextToSpeech$Connection.disconnect(TextToSpeech.java:1321)
       at android.speech.tts.TextToSpeech$1.run(TextToSpeech.java:661)
       at android.speech.tts.TextToSpeech$1.run(TextToSpeech.java:656)
       at android.speech.tts.TextToSpeech$Connection.runAction(TextToSpeech.java:1340)
       at android.speech.tts.TextToSpeech.runAction(TextToSpeech.java:570)
       at android.speech.tts.TextToSpeech.runActionNoReconnect(TextToSpeech.java:557)
       at android.speech.tts.TextToSpeech.shutdown(TextToSpeech.java:656)
       at android.webkit.AccessibilityInjector$TextToSpeechWrapper.shutdown(AccessibilityInjector.java:748)
       at android.webkit.AccessibilityInjector.destroy(AccessibilityInjector.java:187)
       at android.webkit.WebViewClassic.destroyJava(WebViewClassic.java:2618)
       at android.webkit.WebViewClassic.destroy(WebViewClassic.java:2601)
       at android.webkit.WebView.destroy(WebView.java:665)
       at com.douban.dongxi.app.BrowserPurchaseActvitiy$5.run(BrowserPurchaseActvitiy.java:197)
       at android.os.Handler.handleCallback(Handler.java:725)
       at android.os.Handler.dispatchMessage(Handler.java:92)
       at android.os.Looper.loop(Looper.java:137)
       at android.app.ActivityThread.main(ActivityThread.java:5105)
       at java.lang.reflect.Method.invokeNative(Method.java)
       at java.lang.reflect.Method.invoke(Method.java:511)
       at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:793)
       at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:560)
       at dalvik.system.NativeStart.main(NativeStart.java)
{% endcodeblock %}

####And the output from **[CrashLytics](http://crashes.to/s/539a8babf82)**.
![Crash Pie Chart](https://github.com/JohnFeng/johnfeng.github.io/blob/source/source/images/CrashLytics%202015-02-27%20at%2012.22.23.png?raw=true)
*Notice that most crashes happened third-party roms like :MEIZU. Had they changed the webview core?*

---------------------------------------------------

Fix:
----

######{%gemoji smile%}



1. One Method from [stackoverflow](http://stackoverflow.com/questions/27740935/service-not-registered-android-speech-tts-texttospeech)
	
>Have to move all webview from the **xml** file and have added them **dynamically** and passing application context.

 ```java
	
	WebView webView = new WebView(getApplicationContext());
	
 ```

2. Use following code in OnDestory() method.

{% codeblock lang:java Destory a Webview %}
        @Override
        protected void onDestroy() {
	         clearHistory();
             long timeout = ViewConfiguration.getZoomControlsTimeout();
             new Timer().schedule(new TimerTask() {
                     @Override
                      public void run() {
                            destroyWebView(mWebView);
                      }
              }, timeout);
        }
        
	    void clearHistory() {
        if (mWebView != null) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    mWebView.clearHistory();
                }
            });
        }  
        
	    private void destroyWebView(final WebView webView) {
	        if (webView != null) {
            try {
                mWebViewContainer.removeView(webView);

                if (webView != null) {
                    runOnUiThread(new Runnable() {
                        @Override
                        public void run() {
                            webView.stopLoading();
                            webView.removeAllViews();
                            webView.clearCache(true);
                            webView.destroyDrawingCache();
                            webView.destroy();
                        }
                    });
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }  
{% endcodeblock %}

   
Two things should be noticed :

1. all operations related with Webview should be on the **UIThread**
2. webview-destroy operations should happen after removed it from the **container** view.