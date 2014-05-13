## iOS

### Requirements

BugSense for iOS works with iOS 6.0 and newer versions, and supports armv7, armv7s and arm64 architectures, as well as the iOS Simulator. The latest stable version of Xcode is recommended.

### Integration

Integrating BugSense in your project is very easy. Just follow the steps below.

1. Download the <a href="">BugSense-iOS[<strong></strong>].zip</a><strong> <a href="/releases/ios" id="releases">(Release Notes)</a></strong> file, and unzip it.
2. In Xcode, select the target you want to use and, in the **Build Phases** tab expand the **Link Binary With Libraries** section, press the + button, then press **Add Other...**. In the dialog box that appears, go to the framework's location and select it. (The framework is a folder and you should add the whole folder into your project.)
3. The framework will appear at the top of the **Link Binary With Libraries** section and will also be added to your project files (left-hand pane).
4. BugSense depends on **SystemConfiguration.framework* and **libz.dylib**. Add them to your project by selecting them from the list of frameworks in **Link Binary With Libraries**.

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

* Strip Debug Symbols During Copy: **No**
* Strip Linked Product: **No**
* Deployment Postprocessing: **No**
* Generate Debug Symbols: **Yes**

Your project should be working fine with those settings. However, if you find that reporting or symbolication is problematic, try adding ```-ObjC``` and ```-all_load``` in the Other Linker Flags in your target's properties.

### Basic Bugsense use

Make sure you include the BugSense library header in every file where you use BugSense.

```objc
#import <Splunk-iOS/Splunk-iOS.h>
```
Or you can add it to the 'APP_NAME'-Prefix.pch file of your project in the Supporting Files group of your project.
Sample prohect example.

```objc
//
//  Prefix header
//
//  The contents of this file are implicitly included at the beginning of every source file.
//

#import <Availability.h>

#ifndef __IPHONE_5_0
#warning "This project uses features only available in iOS SDK 5.0 and later."
#endif

#ifdef __OBJC__
    #import <UIKit/UIKit.h>
    #import <Foundation/Foundation.h>
    #import <Splunk-iOS/Splunk-iOS.h>
#endif
```


Initialize BugSense with one line of code, preferably in the beginning of your application delegates **application:didFinishLaunchingWithOptions**:

```objc
- (BOOL) application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  [[BugSense sharedInstance] initAndStartSession:@"API_KEY"];
  // ...
}
```

The only required argument is the BugSense API key **(NSString)**.

A variable for the crash controller is not required, depending your needs you can hold to one if necessary. The crash controller is a singleton; you can get a reference to it by calling the **+sharedController** static class method.
During the documentation I wil refer to the BugSense singleton as a property set to the Singleton **[BugSense sharedInstance]** for the examples.

If you crash the app while debugging, the unhandled crash will not be reported. You have to deploy the app to your device and then start it outside the debugging environment, as the end-user would.

## Advanced features

### Sending custom data

BugSense also allows you to send custom data along with every crash report. All you need to do is create a CrashExtraData object with the data that you want to send and add it to the global BugSense LimitedCrashExtraData list:

```objc
    [self.bugSense addCrashExtraData:[[CrashExtraData alloc] initWithKey:@"Key1" andValue:@"Value1"]];
```
The maximum count of objects in a LimitedCrashExtraDataList object is 32.

You can also attach another LimitedCrashExtraDataList object to any handled exception request. Please mention that if the count of objects in the finally combined objects of the global and attached local list exceeds the 32 the latest necessary objects of the custom attached to the request objects will be removed.

```objc
    LimitedCrashExtraDataList* limitedCrashExtraData = [LimitedCrashExtraDataList new];
    [limitedCrashExtraData add:[[CrashExtraData alloc] initWithKey:@"TestLogKey1" andValue:@"TestLogValue1"]];
    [limitedCrashExtraData add:[[CrashExtraData alloc] initWithKey:@"TestLogKey2" andValue:@"TestLogValue2"]];

    @try
        {
        NSArray* twoObjsArray = @[@"1", @"2"];
        NSString* objNotExist = [twoObjsArray objectAtIndex:3];
        NSLog(@"%@", [objNotExist description]);
        }
    @catch (NSException *exception)
        {
        [self.bugSense logExceptionAsync:exception limitedCrashExtraDataList:limitedCrashExtraData completionBlock:^(SplunkLogResult *splunkLogResult)
            {
            // Notice the SplunkLogResult in the completion block that you can access various properties of the request operation. Feel free to examine if you need a better debug details.
            NSLog(@"Log exception request: %@", splunkLogResult.clientRequest);
            }];
        }
```

### Add user identifier

BugSense allows to track closely the experience of any given users. Simply use the setUserIdentifier method to provide a user identifiers like an id number from you database, email, push id, username. In your dashboard, you will search for errors that affect that user. This is a great feature for apps with high ARPU or apps deployed in an MDM environment or during QA.

```objc
    // Setup search by username on iOS
    self.bugSense.userIdentifier = @"UserID";
```

### Bypassing IP restrictions

There are cases where our servers are being blocked due to geographic restrictions (e.g. China). As a result the plugin cannot transmit the crash reports successfully. In that case you can enable the use of a proxy, as follows:

```objc
    self.bugSense.useProxy = YES;
```

### Exception logging

Logging exceptions is very simple: use the logExceptionAsync method to log the exception request with the optional extra data to identify it, any logged request is sent in the next successful application start:

```objc
    LimitedCrashExtraDataList* limitedCrashExtraData = [LimitedCrashExtraDataList new];
    [limitedCrashExtraData add:[[CrashExtraData alloc] initWithKey:@"TestLogKey1" andValue:@"TestLogValue1"]];

    @try
        {
        // Throwing an exception on purpose
        NSArray* twoObjsArray = @[@"1", @"2"];
        NSString* objNotExist = [twoObjsArray objectAtIndex:3];
        NSLog(@"%@", [objNotExist description]);
        }
    @catch (NSException *exception)
        {
        // You can pass a nil value to the limitedCrashExtraDataList parameter and in the completionBlock parameter since it is optional.
        [self.bugSense logExceptionAsync:exception limitedCrashExtraDataList:limitedCrashExtraData completionBlock:^(SplunkLogResult *splunkLogResult)
            {
            // Notice the SplunkLogResult in the completion block that you can access various properties of the request operation. Feel free to examine if you need a better debug details.
            NSLog(@"Log exception request: %@", splunkLogResult.clientRequest);
            }];
        }
```

### Exception send immediately

You can also try to send a handled exception immediately. If for any reason the operation fails the request is handled and it will be sent in the next application restart.

```objc
    LimitedCrashExtraDataList* limitedCrashExtraData = [LimitedCrashExtraDataList new];
    [limitedCrashExtraData add:[[CrashExtraData alloc] initWithKey:@"key1" andValue:@"value1"]];

    @try
        {
        NSArray* twoObjsArray = @[@"1", @"2"];
        NSString* objNotExist = [twoObjsArray objectAtIndex:3];
        NSLog(@"%@", [objNotExist description]);
        }
    @catch (NSException *exception)
        {
        [self.bugSense sendExceptionAsync:exception limitedCrashExtraDataList:limitedCrashExtraData completionBlock:^(SplunkResponseResult *splunkResponseResult)
            {
            NSLog(@"Send exception Request: %@\r\nResponse: %@", splunkResponseResult.clientRequest, splunkResponseResult.serverResponse);
            }];
        }
```

### Breadcrumbs

You can use breadcrumbs to keep track of the user's path in the application. These are transmitted along with every exception request. The leaveBreadcrumb: method takes an NSString as argument:

```objc
    [self.bugSense leaveBreadcrumb:@"breadcrumbLog_1"];
```

### Log custom Events

You can use BugSense to Log events to track users' actions in your application. The maximum length for the **NSString** tag describing the event is 256 characters.

```objc
    [self.bugSense logEventAsyncWithTag:@"Event custom tag!" completionBlock:^(SplunkLogResult *splunkLogResult)
        {
        NSLog(@"Log event result: %u", splunkLogResult.resultState);
        }];
```

### Send custom Events

Also you can try to immediately send events to our server

```objc
    [self.bugSense sendEventAsyncWithTag:@"Event custom tag!" completionBlock:^(SplunkResponseResult *splunkResponseResult)
        {
        NSLog(@"Send event result: %u", splunkResponseResult.resultState);
        }];
```

### Notification delegate to get notified after all cached requests have been sent successfully to the server.

```objc
    // Add the SplunkNotificationDelegate to the class you would like to set as a notificationDelegate of the [BugSense sharedInstance].
    // We will present this to a UIViewController for the example, but you may want to use it however you like, e.g. the AppDelegate application class.
    
    @interface SLKBugSenseViewController : UIViewController<SplunkNotificationDelegate>

    @end
    
    @implementation SLKBugSenseViewController
    
    // Implement the loggedRequestHandled selector
    - (void) loggedRequestHandled:(LoggedRequestEventArgs *)args
        {
        // Investigate the LoggedRequestEventArgs param to get various information about the cached request sent to the server.
        NSLog(@"Processed logged client request: %@", args.responseResult.clientRequest);
        NSLog(@"Server response: %@", args.responseResult.serverResponse);
        }
    
    @end
    
    // Don't forget to set the notificationDelegate property to the appropriate listeners
    [BugSense sharedInstance].notificationDelegate = self;
```

### Last sent crash information

You can access the id of the last crash that was sent by BugSense by using the **getLastCrashIdWithBlock** method. Also, you can access the number of crashes since the last reset using **getTotalCrashesNumWithBlock** method. You can reset this number by calling the **clearTotalCrashesNumWithFailureBlock** method.

```objc
    [self.bugSense clearTotalCrashesNumWithFailureBlock:^(NSError *error)
        {
        NSLog(@"Failed to reset total crashes with error: %@", error.description);
        }];
        
    [self.bugSense getTotalCrashesNumWithBlock:^(NSNumber *totalCrashes)
        {
        NSLog(@"TotalCrashes: %@", totalCrashes);
        if (totalCrashes.intValue > 3)
            {
            NSLog(@"TotalCrashes cleared");
            }
        }
    failure:^(NSError *error)
        {
        NSLog(@"Failed to get total crashes with error: %@", error.description);
        }];
        
    [self.bugSense getLastCrashIdWithBlock:^(NSNumber *lastCrashId)
        {
        NSLog(@"Last crash ErrorId: %@", lastCrashId);
        }
    failure:^(NSError *error)
        {
        NSLog(@"Failed to get last crash error id with error: %@", error.description);
        }];
```

### How BugSense works

#### Debugger

When the Debugger (gdb, lldb) is on, the crash controller does not send any reports.

#### Notes

If your application is constantly crashing at start (or immediately after), BugSense may not get the chance to report the crash. BugSense depends on the application to at least launch properly to perform the reporting. In this mode, a demo app would need to run twice for the crash to be reported (not including any runs with the Debugger on).
