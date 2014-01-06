## Android

### Download & install the SDK

**Requirements**

* Android SDK 1.6 (Version 4) or later

1. Your first step is to copy the <a href="">bugsense-<strong></strong>.zip</a><strong><a href="/releases/android" id="releases">(Release Notes)</a></strong> or use [Maven](http://blog.bugsense.com/post/43988856467/maven-repo-for-bugsense-now-available) into your project's libs folder. If this folder do not exist, you have to create it. Import it to your project by adding the following line at the top of your Activity's class file:

```java
import com.bugsense.trace.BugSenseHandler;
```

Make sure you also add the line

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

to your app's **AndroidManifest.xml*8 file. BugSense uses this permission to send the crash reports and performance metrics. 

2. Next step is to add the BugSenseHandler in your activity before setContentView. Then you are ready to go!

```java
public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);
  BugSenseHandler.initAndStartSession(Context, APIKEY);
  setContentView(R.layout.main);
  //rest of your code here
}
```

The **InitAndStartSession** method installs the BugSense exception handler and the performance monitor. It then sends all the previously saved crash reports and performance metrics. At the same time, it starts a new session for your activity.

Here's an example on how to use the **InitAndStartSession**:

```java
BugSenseHandler.initAndStartSession(MyActivity.this, "YOURAPIKEY");
```
