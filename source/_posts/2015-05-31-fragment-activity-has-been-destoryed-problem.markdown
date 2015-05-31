---
layout: post
title: "Fragment - Activity has been destoryed Problem"
date: 2015-05-31 23:40:18 +0800
comments: true
categories: android bug fragment 
---
###[Bug]
When fixing projects' bugs, I found a really wired one with error info shown as below:

{% codeblock lang:java Error Info %}
java.lang.IllegalStateException: Activity has been destroyed
    at android.support.v4.app.FragmentManagerImpl.enqueueAction(Unknown Source)
    at android.support.v4.app.BackStackRecord.commitInternal(Unknown Source)
    at android.support.v4.app.BackStackRecord.commitAllowingStateLoss(Unknown Source)
    at android.support.v4.app.FragmentStatePagerAdapter.finishUpdate(Unknown Source)
    at android.support.v4.view.ViewPager.dataSetChanged(Unknown Source)
    at android.support.v4.view.ViewPager$PagerObserver.onChanged(Unknown Source)
    at android.database.DataSetObservable.notifyChanged(DataSetObservable.java:35)
    at android.support.v4.view.PagerAdapter.notifyDataSetChanged(Unknown Source)
    at com.douban.frodo.activity.AlbumPhotoActivity$AlbumPhotoPagerAdapter.b(Unknown Source)
    at com.douban.frodo.activity.AlbumPhotoActivity$10.a(Unknown Source)
    at com.douban.frodo.activity.AlbumPhotoActivity$10.onResponse(Unknown Source)
    at im.amomo.volley.OkRequest.deliverResponse(Unknown Source)
    at com.android.volley.ExecutorDelivery$ResponseDeliveryRunnable.run(Unknown Source)
    at android.os.Handler.handleCallback(Handler.java:605)
    at android.os.Handler.dispatchMessage(Handler.java:92)
    at android.os.Looper.loop(Looper.java:148)
    at android.app.ActivityThread.main(ActivityThread.java:4529)
    at java.lang.reflect.Method.invokeNative(Native Method)
    at java.lang.reflect.Method.invoke(Method.java:511)
    at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:832)
    at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:599)
    at dalvik.system.NativeStart.main(Native Method)
{% endcodeblock %}

---

###[Situation]
The situation is : I tried to put a viewpager with a fragmentAdapter inside of another fragment, which is inside of a activity. And I tested these code on my Nexus5(5.1.1), they worked like a charm .And so did on our QA's phones.

But this morning , when I was 'enjoying' my regular online-app-running-data reading work, I found this crash happened over 150 times and continuely growing. I tried every method I could get, including the nest-fragments crashes we usually easy to get, and after a few searching on Internet, I concluded this result: "It's Google's mistake."

###[Reason]
In Google's Android Source Code, something wierd found like this:

[Android Source Code](http://grepcode.com/file_/repository.grepcode.com/java/ext/com.google.android/android/5.0.0_r1/android/support/v4/app/Fragment.java/?v=diff&id2=4.4.4_r1#1223)

```java
mChildFragmentManager = null;
```
It means before 5.0.1, everytime a fragement invokes its onDestory or onDetach Method, its mChildFramentManager has never been reset. WOW. And I guess that's the reason.

What Marcus Forsell Stahre said on [StackOverflow](http://stackoverflow.com/questions/15207305/getting-the-error-java-lang-illegalstateexception-activity-has-been-destroyed):

>If you look at the implementation of Fragment, you'll see that when moving to the detached state, it'll reset its internal state. However, it doesn't reset mChildFragmentManager (this is a bug in the current version of the support library). This causes it to not reattach the child fragment manager when the Fragment is reattached, causing the exception you saw.

And I also found a [Google Issue](https://code.google.com/p/android/issues/detail?id=42601) for this problem.

Where the crash been throw:


{% codeblock lang:java android.support.v4.app.FragmentManagerImpl.enqueueAction %}
    /**
     * Adds an action to the queue of pending actions.
     *
     * @param action the action to add
     * @param allowStateLoss whether to allow loss of state information
     * @throws IllegalStateException if the activity has been destroyed
     */
    public void enqueueAction(Runnable action, boolean allowStateLoss) {
        if (!allowStateLoss) {
            checkStateLoss();
        }
        synchronized (this) {
            if (mDestroyed || mActivity == null) {
                throw new IllegalStateException("Activity has been destroyed");
            }
            if (mPendingActions == null) {
                mPendingActions = new ArrayList<Runnable>();
            }
            mPendingActions.add(action);
            if (mPendingActions.size() == 1) {
                mActivity.mHandler.removeCallbacks(mExecCommit);
                mActivity.mHandler.post(mExecCommit);
            }
        }
    }
{% endcodeblock %}

###[Fix]
the answer is simple, we need to reset its child fragmentManager manully on devices carrying systems below Android 5.0.1.Here is the code , inside the onDetach method:

{% codeblock lang:java Fix%}

@Override
public void onDetach() {
    super.onDetach();

    try {
        Field childFragmentManager = Fragment.class.getDeclaredField("mChildFragmentManager");
        childFragmentManager.setAccessible(true);
        childFragmentManager.set(this, null);

    } catch (NoSuchFieldException e) {
        throw new RuntimeException(e);
    } catch (IllegalAccessException e) {
        throw new RuntimeException(e);
    }
}
{% endcodeblock %}





 
  