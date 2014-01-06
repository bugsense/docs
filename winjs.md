## Windows 8 / JS

### Using the Javascript library

Download the <a href="">bugsense-<strong></strong>.js</a><strong> <a href="/releases/html5" id="releases">(Release Notes)</a></strong> file and store in it along with you other Javascript files. Alternatively you can use the BugSense hosted version (see the following Gist). 

Then all you have to do is to include the script in the default.html page and page initialize it. 

![Add bugsense.js to your project](/static/images/landing/screens/VS2012.bugsense-js.png "Add bugsense.js to your project")

```js
// Create the app
var app = WinJS.Application;
 
// Initialize bugsense
var bugsense = new Bugsense( {
apiKey: 'YOUR_API_KEY',
context: app
} );
 
// If you want to display a popup (against MS guidelines) add the message option
var bugsense = new Bugsense( {
apiKey: 'YOUR_API_KEY',
context: app,
message: 'Sorry for the inconvenience. We have been notified about the error'
} );
```

You must enable the **Internet** capability.

Stacktrace is available only for handled exceptions due to WinJS implementation. In this case the url, line and line number will be extracted from the stacktrace.

Bugsense.js uses CORS to send crash reports.

If there is no Internet Connection the report will be stored and sent next time the app is launched.

When an error occurs the user is informed by a slick Popup. This option is customizable. You can:

1. Display nothing and just keep the application running.
2. Display a popup with customizable body.
3. Display a Fix Notification when a user experiences an error that is been fixed in new versions of the app 

![An unexpected error occured](https://1-ps.googleusercontent.com/sx/s.bugsense.appspot.com/www.bugsense.com/static/images/landing/screens/notification-w81.png.pagespeed.ce.BfusI9Rh5U.png)
![Please update](https://1-ps.googleusercontent.com/sx/s.bugsense.appspot.com/www.bugsense.com/static/images/landing/screens/xnotification-w82.png.pagespeed.ic.FOC6wNWwQS.png)

### Registering caught exceptions

Bugsense.js also allows you to register handled exception and append custom data to the crash report.

```js
try {
   rotateScreen();
} catch ( error ) {
   bugsense.notify( error, { rotation: 'not supported' } )
};
```

### Custom data & Breadcrumbs

You can add custom data to the BugSense crash reports as well as breadcrumbs to help you debug your app faster!

```js
// add metadata
bugsense.addExtraData( 'user_level', 'paid' );
bugsense.addExtraData( 'account', 'CEO' );

// Clear all metadata
bugsense.clearExtraData();

// leave breadcrumb
bugsense.leaveBreadcrumb( 'Fetch Friendlist' );
```


