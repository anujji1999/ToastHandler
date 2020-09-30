# ToastHandler
Reference from [StackOverFlow](https://stackoverflow.com/questions/51532449/fatal-exception-android-view-windowmanagerbadtokenexception-unable-to-add-wind)

An Android library wrapper for handling Toast BadTokenException happening on Android API level 25 and showing Toast smoothly on All Android versions. Have also handled some memory leaks and created ToastHandler as Singleton(lazy-loading) for better performance.


### Usage 

Add this to your app build.gradle:

```groovy
implementation "com.toastfix:toastcompatwrapper:1.1.1"
```

Use this wherever you are showing Toast:

Java
```java
ToastHandler.INSTANCE.showToast(this, "Hello,I am Toast", Toast.LENGTH_SHORT);
```

Kotlin
```kotlin
ToastHandler.showToast(this, "Hello,I am Toast", Toast.LENGTH_SHORT)
```

Please refer the demo for usage.


### Why

From API 25, Android added a new param `IBinder windowToken` for `Toast#handleShow()`, and It brought an exception. 
As Android said on API 26: 

```java
// Since the notification manager service cancels the token right
// after it notifies us to cancel the toast there is an inherent
// race and we may attempt to add a window after the token has been
// invalidated. Let us hedge against that.
```

So they try-catch the `mWM.addView(mView, mParams)` on API 26. **However, API 25 is still at risk. Our applications will continue to produce such an exception, and can not capture it**: 

```java
Fatal Exception: android.view.WindowManager$BadTokenException: 
Unable to add window -- token android.os.BinderProxy@1c4411f is not valid; is your activity running?
       at android.view.ViewRootImpl.setView(ViewRootImpl.java:679)
       at android.view.WindowManagerGlobal.addView(WindowManagerGlobal.java:342)
       at android.view.WindowManagerImpl.addView(WindowManagerImpl.java:94)
       at android.widget.Toast$TN.handleShow(Toast.java:459)
       at android.widget.Toast$TN$2.handleMessage(Toast.java:342)
       at android.os.Handler.dispatchMessage(Handler.java:102)
       at android.os.Looper.loop(Looper.java:154)
       at android.app.ActivityThread.main(ActivityThread.java:6236)
       at java.lang.reflect.Method.invoke(Method.java)
       at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:891)
       at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:781)
```

This exception occurs regardless of whether the `Context` you passed to `Toast` is an `Activity` or `ApplicationContext` or `Service`. And you can not `try-catch` it. 
 
 See the detail diff of **Android Toast sources**: 

![ToastDiff.png](art/ToastDiff.png)

You can raise PR for any contribution and optimization. :-)






