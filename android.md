## Android

### Download & install the SDK

**Requirements**

* Android SDK 1.6 (Version 4) or later

1.    Your first step is to copy the <a href="">bugsense-<strong></strong>.zip</a><strong><a href="/releases/android" id="releases">(Release Notes)</a></strong> or use [Maven](http://blog.bugsense.com/post/43988856467/maven-repo-for-bugsense-now-available) into your project's libs folder. If this folder do not exist, you have to create it. Import it to your project by adding the following line at the top of your Activity's class file:

      ```java
      import com.bugsense.trace.BugSenseHandler;
      ```

      Make sure you also add the line

      ```xml
      <uses-permission android:name="android.permission.INTERNET" />
      ```

      to your app's **AndroidManifest.xml** file. BugSense uses this permission to send the crash reports and performance metrics. 

2.    Next step is to add the BugSenseHandler in your activity before setContentView. Then you are ready to go!

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

      Whenever you want to explicitly start the session, you can use the **startSession** method at the onStart method of your activity, as follows:

      ```java
      BugSenseHandler.startSession(MyActivity.this);
      ```

      Whenever you want to close the session, you can use the **closeSession88 method as follows:

      ```java
      BugSenseHandler.closeSession(MyActivity.this);
      ```

      Close session will close the current session, offering better tracking of the sessions for your users.

      If you want to manually flush all the saved data, use the **BugSenseHandler.flush(Context)** method:

      ```java
      BugSenseHandler.flush(MyActivity.this);
      ```

### Features

#### Add Extra Data

You have the option to add extra custom information to the crash reports. In order to do this, use the crash extra map.

Use the **addCrashExtraData** method below to add key value pairs to the extra map:

```java
BugSenseHandler.addCrashExtraData("level", "second level");
BugSenseHandler.addCrashExtraData("difficulty", "impossibruuu");
```

If you want to add your custom data as a map, use the **addCrashExtraMap** method:

```java
HashMap<String, String> extras = new HashMap<String, String>();
extras.put("level", "second level");
extras.put("difficulty", "impossibruuu");
BugSenseHandler.addCrashExtraMap(extras);
```

Removing a value from the crash extra data is even simpler than adding one, with the **removeCrashExtraData** method. Use **BugSenseHandler.removeCrashExtraData(Key)** for example:

```java
BugSenseHandler.removeCrashExtraData("difficulty");
```

You can get the extra data map by using the **BugSenseHandler.getCrashExtraData** method.

If you want to clear the extra data completely, use the **clearCrashExtraData** method:

```java
BugSenseHandler.clearCrashExtraData();
```

#### Search by username

BugSense allows to track closely the experience of any given users. Simply use the setUserIdentifier method to provide a user identifiers like an id number from you database, email, push id, username. In your dashboard, you will search for errors that affect that user. This is a great feature for apps with high ARPU or apps deployed in an MDM environment or during QA.

```java
// Setup search by username on Android
BugSenseHandler.setUserIdentifier(String userIdentifier);
```

#### Log Handled Exceptions

BugSense also gives you the ability to log handled exceptions, for example:

```java
try{
  String a = null;
  a.toString();
}catch(Exception ex) {
  ex.printStackTrace(); // in case you want to see the stacktrace in your log cat output
  BugSenseHandler.sendException(ex);
}
```

If you want to add information, you can replace the **sendException** method with the **sendExceptionMessage** method:

```java
BugSenseHandler.sendExceptionMessage("level", "second level", ex);
```

Alternatively you can use multiple key values using a HashMap with the **sendExceptionMap** method:

```java
HashMap<String, String> map = new HashMap<String, String>();
map.put("level", "second level");
map.put("difficulty", "impossibruuu");
BugSenseHandler.sendExceptionMap(map, ex);
```

#### Breadcrumbs

By adding breadcrumbs to your code you can easily see the trail the user followed before getting the crash. Just add the following API call at the points if interest in your code.

```java
BugSenseHandler.leaveBreadcrumb("PointA");
```

#### ProGuard Support

If you are using [ProGuard](http://www.saikoa.com/proguard), you should add the following line to your ProGuard configuration file to exclude BugSense from being obfuscated.

```java
-keep class com.bugsense.** { *; }
-libraryjars libs/bugsense-3.6.jar
```

In order to use Proguard with BugSense successfully we suggest you use the following Proguard settings:

```java
-optimizationpasses 25 
-dontusemixedcaseclassnames 
-dontskipnonpubliclibraryclasses 
-dontpreverify 
-verbose 
-optimizations !code/simplification/arithmetic,!field/*,!class/merging/* 
-printmapping out.map 
-renamesourcefileattribute SourceFile 
-keepattributes SourceFile,LineNumberTable 
-keep class com.bugsense.** { *; } 
-keep public class * extends android.app.Activity 
-keep public class * extends android.app.Application 
-keep public class * extends android.app.Service 
-keep public class * extends android.content.BroadcastReceiver 
-keep public class * extends android.content.ContentProvider 
-keep public class * extends android.app.backup.BackupAgentHelper 
-keep public class * extends android.preference.Preference 
-keep public class com.android.vending.licensing.ILicensingService 

-keepclasseswithmembernames class * { 
    native <methods>; 
} 

-keepclasseswithmembers class * { 
    public <init>(android.content.Context, android.util.AttributeSet); 
} 

-keepclasseswithmembers class * { 
    public <init>(android.content.Context, android.util.AttributeSet, int); 
} 

-keepclassmembers class * extends android.app.Activity { 
   public void *(android.view.View); 
} 

-keepclassmembers enum * { 
    public static **[] values(); 
    public static ** valueOf(java.lang.String); 
} 

-keep class * implements android.os.Parcelable { 
  public static final android.os.Parcelable$Creator *;
```

#### LogCat Support

*Available on Entreprise plans*

If you want to send the LogCat output with your exceptions, add the line:

```xml
<uses-permission android:name="android.permission.READ_LOGS" />
```

to your app's **AndroidManifest.xml** file. BugSense uses this permission to read the LogCat output. (Keep in mind that the logcat output may contain sensitive user information from other applications.)

In order to enable the LogCat logging, you can use the **setLogging(Boolean)** method. You can also restrain the number of the lines or the filter using the following methods:

```java
setLogging(int lines)
setLogging(String filter)
setLogging(int lines, String filter)
```

BugSense by default sends the last 5000 lines with no filter. Filtering uses the exact same filtering mechanism as LogCat uses. **You can find documentation** [here](http://developer.android.com/tools/debugging/debugging-log.html#filteringOutput).

```java
// For example if you want to get the last 1000 lines and log all messages 
// with priority level "warning" and higher
BugSenseHandler.setLogging(1000, "*:W");

//Log last 100 messages
BugSenseHandler.setLogging(100);

//Log all the messages with priority level "warning" and higher, on all tags.
BugSenseHandler.setLogging("*:W");

//Log the latest 100 messages with priority level "warning" and higher, on all tags.
BugSenseHandler.setLogging(100, "*:W");

//Log all the messages from the ActivityManager and the Debug and higher of your application.
BugSenseHandler.setLogging(400, "ActivityManager:I MyApp:D *:S");
```

### Sending custom events

If you want to log custom events, use the **sendEvent(String)** method of BugSenseHandler as shown below:

```java
BugSenseHandler.sendEvent("button1 pressed");
```

### Localize Fix Notifications

In order to localize the fix notification, use the **setLocalizedNotification** method passing the Strings for the ticker, the title and the message. For example: 

```java
BugSenseHandler.setLocalizedNotification("Mi app", "Lo hemos corregido", "Por favor, actualice su aplicacion, este error ha sido corregido!");
```

#### Exception Callback

You can use the last breath feature for a callback in your code when a crash occurs.

1. Let your Activity implement BugSense's ExceptionCallback. ex:

```java
public class MainActivity extends Activity implements ExceptionCallback {
}
```

2. This will force you to add a public void method called lastBreath() in your code.

```java
@Override
public void lastBreath(Exception ex) {
//do or save stuff here
}
```

3. You should set your activity as the current ExceptionCallback.

```java
BugSenseHandler.setExceptionCallback(this);
```

You can more details and an example in our related [blog post](http://blog.bugsense.com/post/40089742173/lastbreath-or-how-to-avoid-losing-the-state-of-a-game).

#### Last Error ID

BugSense offers the ability to keep track of the last known error id and the number of the crashes.

In order to find out the last Error ID you can use the method **getLastCrashID()**. You can check the number of the crashed with the method **getTotalCrashesNum()** and you can reset the crashes counter with the method **clearTotalCrashesNum()**.

**Have in mind these methods are blocking so you should use them in an AsyncTask or a thread.**

```java
// Get the total number of crashes
int totalCrashes = BugSenseHandler.getTotalCrashesNum();

// Get last error id
String lastErrorID = BugSenseHandler.getLastCrashID();

// Clear total crashes
BugSenseHandler.clearTotalCrashesNum();
```

#### Running app processes

If you want to keep track of the number of the apps that are running along with your app when a crash occurs, then you only need to add the **android.permission.GET_TASKS** permission to your AndroidManifest file. BugSense will automatically count the running app processes and you will be able to see that number at the error instances at your dashboard.

#### BugSense and ACRA

1.    If you are using ACRA 4.x, you can use BugSense as your backend.

      The only change you need to do is specify in formUri BugSense's url and your API key: 

      ```java
      @ReportsCrashes(formUri = "http://www.bugsense.com/api/acra?api_key=YOUR_API_KEY", formKey="") 
      ```
2.    Custom Data

      If you want to send Custom Data with ACRA, you should use the **putCustomData** method. For example 

      ```java
      ACRA.getErrorReporter().putCustomData("myKey1", "myValue1"); 
      ```

      You can also tag an exception if you add a key named "tag" in the Custom Data, e.g

      ```java
      ErrorReporter.getInstance().putCustomData("tag", "audio player");
      ```

3.    ProGuard and ACRA

      There are some issues with using ProGuard and ACRA. **We advise you to read the [official ACRA wiki](https://github.com/ACRA/acra/wiki/Proguard) about this**.
