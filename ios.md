## iOS

### Installing Bugsense

Requirements

1. BugSense for iOS works with iOS 4.0+, armv7+armv7s, with both device and simulator support.
2. BugSense can be used with Xcode 4.2/iOS 5.0 and later.

### Integration

Integrating BugSense in your project is very easy. Just follow the steps below.

1. Download the <a href="">BugSense-iOS[<strong></strong>].zip</a><strong> <a href="/releases/ios" id="releases">(Release Notes)</a></strong> file, and unzip it.
2. In Xcode, select the target you want to use and, in the **Build Phases** tab expand the **Link Binary With Libraries*8 section, press the + button, then press **Add Other...**. In the dialog box that appears, go to the framework's location and select it. (The framework is a folder and you should add the whole folder into your project.)
3. The framework will appear at the top of the **Link Binary With Libraries** section and will also be added to your project files (left-hand pane).
4. BugSense depends on **SystemConfiguration.framework** and **libz.dylib**. Add them to your project by selecting them from the list of frameworks in **Link Binary With Libraries**.

### Configuring your project for server-side symbolication

*Please make sure that your binary name (usually the Target name) contains only latin characters, otherwise symbolication may not be successful.*

1.    In your target's Build Settings, verify the following are set:

      Strip Debug Symbols During Copy: **Yes**
      Strip Linked Product: **Yes**
2.    For every build you release to testers or users, you need to store the dSYM file and upload it to BugSense through your dashboard (Project > Settings > dSYMs).

      If you build and archive your project from XCode (Product > Archive), your build is stored in the XCode Organizer (Window > Organizer, Archives tab).

      Right-click on the archive of your choice and Show it in Finder. This will lead you to the file that represents your archived build.

      By right clicking on the file and selecting Show Package Contents, you open a package that contains a folder named dSYM. There lies a file named your-project-name.app.dSYM Copy this to your Desktop, compress it (since it is essentially a folder) and upload it to Bugsense (Project Settings > dSYMs).

### Configuring your project for on-device symbolication

You can also symbolicate the crash reports on the device. (Keep in mind that by using on-device symbolication you won't be receiving useful info such as the line of code which crashes your application.)

In your target's Build Settings, set the following values for all configurations (typically Debug and Release)

Strip Debug Symbols During Copy: **No**
Strip Linked Product: **No**
Deployment Postprocessing: **No**
Generate Debug Symbols: **Yes**

*Your project should be working fine with those settings. However, if you find that reporting or symbolication is problematic, try this: * In your target's properties, * in Other Linker Flags add -ObjC * additionally, in Other Linker Flags add -all_load*

### Basic Bugsense use

Make sure you include the BugSense library headers in every file where you use BugSense.

```objc
#import <BugSense-iOS/BugSenseController.h>
```

Initialize BugSense with one line of code, preferably in the beginning of your application delegates **application:didFinishLaunchingWithOptions**:

```objc
- (BOOL) application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  [BugSenseController sharedControllerWithBugSenseAPIKey:@"<Your BugSense API Key>"];
  // ...
}
```

The only required argument is the BugSense API key **(NSString)**. There are two additional factory methods that take extra parameters, if you need them.

A variable for the crash controller is not required. (If you specify one, the compiler might show you a warning.) The crash controller is a singleton; you can get a reference to it by calling the **+sharedController** method. You can create one using the factory methods.

If you crash the app while debugging, the crash will not be reported. You have to deploy the app to your device and then start it outside the debugging environment, as the end-user would.

## Advanced features

### Sending custom data

BugSense also allows you to send custom data along with the crash report. All you need to do is create an NSDictionary with the data that you want to send:

```objc
- (BOOL) application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    NSDictionary *aNiceLittleDictionary = [NSDictionary dictionaryWithObjectsAndKeys:@"myObject", @"myKey", nil];
    [BugSenseController sharedControllerWithBugSenseAPIKey:@"<Your BugSense API Key>"
                                            userDictionary:aNiceLittleDictionary];

    //...
}
```

The first argument is the **BugSense API key (NSString)**. The second argument is an **NSDictionary** with custom data. Calling this method again will not let you redefine the data for this instance of the crash controller.

There are two ways to use custom data in your application. In standard mode, the crash controller sends the crash report on relaunch and uses the custom data that you provide in the factory method, as is. In immediate dispatch mode, it often makes sense to use an **NSMutableDictionary**, keep a reference to it and update the data as you go along.

### Add user identifier

BugSense allows to track closely the experience of any given users. Simply use the setUserIdentifier method to provide a user identifiers like an id number from you database, email, push id, username. In your dashboard, you will search for errors that affect that user. This is a great feature for apps with high ARPU or apps deployed in an MDM environment or during QA.

```objc
// Setup search by username on iOS
[BugSenseController setUserIdentifier:@"239859"];
```

### Enabling immediate dispatch

BugSense allows you to send reports as soon as the crash happens:

```objc
- (BOOL) application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [BugSenseController sharedControllerWithBugSenseAPIKey:@"<Your BugSense API Key>"
                                            userDictionary:nil
                                           sendImmediately:YES];

    // ...
}
```

This method executes async unsafe code inside the crash handler that could lead to unexpected results. Therefore, we strongly advise against it and suggest that it should only be used for debugging purposes.

### Bypassing IP restrictions

There are cases where our servers are being blocked due to geographic restrictions (e.g. China). As a result the plugin cannot transmit the crash reports successfully. In that case you can enable the use of a proxy, as follows:

```objc
[BugSenseController setUsesProxy:YES];
```

### Exception logging

Logging exceptions is very simple: use the BUGSENSE_LOG macro to post the exception and its stacktrace along with the optional extra data to identify it, e.g:

```objc
@try {
    // some exception throwing code
} @catch (NSException *exc) {
    NSDictionary *data = [NSDictionary dictionaryWithObjects:[NSArray arrayWithObjects:@"sample-value", nil]
                                                     forKeys:[NSArray arrayWithObjects:@"sample-key", nil]];
    BUGSENSE_LOG(exc, data);
}
```

The BUGSENSE_LOG macro is a shorthand for this:

```objc
[[BugSenseController sharedController] logException: withExtraData:]
```

This uses the **logException:withExtraData:** method on the shared controller singleton, which is returned by the +sharedController method. BugSense automatically captures the key values of the exception's userDictionary as well.

### Breadcrumbs

You can use breadcrumbs to keep track of the user's path in the application. These are transmitted along with crash reports. The leaveBreadcrumb: method takes an NSString as argument:

```objc
[BugSenseController leaveBreadcrumb:@"DetailViewController"];
```

### Sending custom Events

 You can use BugSense events to track users' actions in your application. The maximum length for the **NSString** tag describing the event is 256 characters.

 ```objc
 [BugSenseController sendCustomEventWithTag:@"custom_event"];
 ```

### Callback after all errors have been sent

Using **+setErrorNetworkOperationsCompletionBlock:** you can set a block to be called by the plug-in when there are no more errors to be transmitted. Bear in mind that this does not guarantee that they have been successfully transmitted, they may have been cached for future transmission.

### Last sent crash information

You can access the id of the last crash that was sent by BugSense by using the **+lastCrashId** method. Also, you can access the number of crashes since the last reset using **+crashCount** method. You can reset this number by calling the **+resetCrashCount** method.

These functions, combined with the callback method described in the previous section, enable you to provide better management of your applicationâs errors. For example, you can apologize to users that have been hit by lots of crashes, so that they know you are actively working on fixing them!

```objc
[BugSenseController setErrorNetworkOperationsCompletionBlock:^() {
   NSLog(@"Application crashed with errorId: %ld with an error count since last reset: %d", [BugSenseController lastCrashId], [BugSenseController crashCount]);

   if ([BugSenseController crashCount] > 5) {
      UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"We're sorry!" message:@"We are aware of the crashes that you have experienced lately, and are actively working on fixing them for the next version!" delegate:nil cancelButtonTitle:@"OK" otherButtonTitles:nil];
      [alertView show];
      [BugSenseController resetCrashCount];
   }
}];
```

### Dispatch of system log messages

Along with your crash reports, you can send the last log messages from when the crash happened so that you have a better idea of the environment at the time of the crash. You can specify how many messages you want logged and at which logging level. For example, to keep the last 10 messages at logging level 8 you should write:

```objc
[BugSenseController setLogMessagesCount:10];
[BugSenseController setLogMessagesLevel:8];
```

### How BugSense works

#### Debugger

When the Debugger (gdb, lldb) is on, the crash controller does not send any reports.

#### Standard mode

Implications: This means that if your application is constantly crashing at start (or immediately after), BugSense may not get the chance to report the crash. BugSense depends on the application to at least launch properly to perform the reporting. In this mode, a demo app would need to run twice for the crash to be reported (not including any runs with the Debugger on).

#### Immediate dispatch mode

BugSense sends the crash data immediately after the crash. The application is kept alive temporarily, until the crash gets reported or the connection times out.

Implications: Even if your application is constantly crashing at start, BugSense will report the crash. This is extremely satisfying when testing with a demo app, as you can see BugSense in action in the first run (excluding any Test runs with the Debugger on).

#### Console logs

BugSense prints out some debugging messages on the Console. You will typically see entries such as:

```objc
Oct 18 17:48:11 unknown BugSenseDemo[176] <Warning>: BugSense --> Symbols are being retained...
Oct 18 17:48:11 unknown BugSenseDemo[176] <Warning>: BugSense --> Symbols have been retained.
Oct 18 17:49:22 unknown BugSenseDemo[176] <Warning>: BugSense --> Processing crash report...
Oct 18 17:49:22 unknown BugSenseDemo[176] <Warning>: BugSense --> Crashed on 2011-10-18 09:02:49 +0000, with signal SIGABRT (code #0, address=0x309c332c)
Oct 18 17:49:22 unknown BugSenseDemo[176] <Warning>: BugSense --> Generating JSON data from crash report...
Oct 18 17:49:22 unknown BugSenseDemo[176] <Warning>: BugSense --> Posting JSON data...
Oct 18 17:49:24 unknown BugSenseDemo[176] <Warning>: BugSense --> Server responded with status code: 200
```
