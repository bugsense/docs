## POST API

You may want to create your library to send your bugs to BugSense. We have a tendency to love mobile apps and any distributed apps running on PCs, Macs, Flex, Adobe Air, WPF or Unity.

You can send your errors from server side apps but we don't encourage this type of usage as BugSense is designed for the needs of the mobile (or any distributed) platform developer.

Sending an error to Bugsense is quite easy. All you have to do is to send a POST request to [http://www.bugsense.com/api/errors](http://www.bugsense.com/api/errors) or [https://www.bugsense.com/api/errors](https://bugsense.appspot.com/api/errors) if you prefer HTTPS.

The POST data should have a data param containing a (URL escaped) stringified JSON object with the error data. Sample JSON object for Android:

```json
{
   "client": {
      "name": "bugsense-android", // Obligatory
      "version": "0.6"
   },
   "request": {
      "custom_data": {
         "key1": "value1",
         "key2": "value2"
      }
   },
   "exception": {
      "message": "java.lang.RuntimeException: exception requested", // Obligatory
      "where": "MainActivity.java:47", // Obligatory
      "klass": "java.lang.RuntimeException", // Type of exception
      "backtrace": "java.lang.RuntimeException: exception requested\r\nat com.sfalma.trace.example.MainActivity$1.onClick(MainActivity.java:47)\r\nat android.view.View.performClick(View.java:2408)\r\nat android.view.View$PerformClick.run(View.java:8816)\r\nat android.os.Handler.handleCallback(Handler.java:587)\r\nat android.os.Handler.dispatchMessage(Handler.java:92)\r\nat android.os.Looper.loop(Looper.java:123)\r\nat android.app.ActivityThread.main(ActivityThread.java:4627)\r\nat java.lang.reflect.Method.invokeNative(Native Method)\r\nat java.lang.reflect.Method.invoke(Method.java:521)\r\nat com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:868)\r\nat com.android.internal.os.ZygoteInit.main(ZygoteInit.java:626)\r\nat dalvik.system.NativeStart.main(Native Method)\\n" // Obligatory
   },
   "application_environment": {
      "phone": "android", // Device model (could be PC or Max) Obligatory
      "appver": "1.2", // Obligatory
      "appname": "com.sfalma", // Obligatory
      "osver": "2.3", // Obligatory
      "wifi_on": "true",
      "mobile_net_on": "true",
      "gps_on": "true",
      "screen_dpi(x:y)": "120.0:120.0",
      "screen:width": "240",
      "screen:height": "400",
      "screen:orientation": "normal",
      "uid": "234243-2sdf345-sdfs3452345" // Unique identifier
   },
   "feedback": {
      "email": "test@bugsense.com",
      "description": "I just had a crash :("
   }
}
```

Please note that some fields are obligatory.

Finally you will need to send your API Key. You should add an HTTP header to your request.

```X-BugSense-Api-Key: YOUR_API_KEY```

If POST is not available or your want a better grouping algorithm then [create a ticket](http://bugsense.zendesk.com/).

We look forward to know more about your plugins!
