## HTML5

### Using the Javascript library

Download the <a href="">bugsense-<strong></strong>.js</a><strong> <a href="/releases/html5" id="releases">(Release Notes)</a></strong> file (or download the [minified version](https://github.com/bugsense/bugsense.js/blob/master/bugsense.min.js)) and store in it along with your other Javascript files.

You can also use [Bower](http://bower.io/) to install BugSense in your project.

```
bower install bugsense
```

or add it in your **bower.json** file

```js
{
  "name": "my-project",
  "version": "1.0.0",
  "main": "path/to/main.css",
  "ignore": [
    ".jshintrc",
    "**/*.txt"
  ],
  "dependencies": {
    "bugsense": "2.0.0",
  }
}
```

Alternatively you can use the BugSense hosted version (see the following Gist).

Include the script in your HTML page and initialize it.

```html
<script src="http://www.bugsense.com/static/js/global/bugsense.js" type='text/javascript'></script>
<script type="text/javascript">
  var bugsense = new Bugsense( { apiKey: 'YOUR_API_KEY' } );
</script>
```

*For more initialization options, read our [API docs](https://github.com/bugsense/bugsense.js/blob/master/README.md).*

Older mobile browsers do not support the window.onerror callback and therefore the plugin will not receive any uncaught exception.

When there's only the Error object caught, error.stack will be parsed to get the url and line number.

Deobfuscation or retracing for minified and/or obfuscated Javascript files is not supported yet.

Bugsense.js uses CORS to send crash reports.

### Registering handled exceptions
Bugsense.js allows you to register handled exception as well and append metadata to the crash report.
```js
try {
   rotateScreen();
} catch ( error ) {
   bugsense.notify( error, { rotation: 'not supported' } );
};
```

### ExtraData & Breadcrumbs
You can add extraData to the BugSense crash reports as well as breacrumbs to help you debug your app faster!

#### Managing extraData
In order to add extra data in your instace, you can use the **bugsense.addExtraData** function.
```js
/* add metadata */
bugsense.addExtraData( 'user_level', 'paid' );
bugsense.addExtraData( 'account', 'CEO' );
```

If you want to remove extra data, you can use the **bugsense.removeExtraData** and passing the key as a parameter.
```js
/* Removing metadata by key */
bugsense.removeExtraData('user_level');
```

Or you can clear all metadata by using **bugsense.clearExtraData**.
```js
/* Clear all metadata */
bugsense.clearExtraData();
```

#### Managing Breadcrumbs
By adding breadcrumbs to your code you can easily see the trail the user followed before getting the crash. Just leave a breadcrumb by using **bugsense.leaveBreadcrumb**.
```js
/* leave breadcrumb */
bugsense.leaveBreadcrumb( 'Fetch Friendlist' );
```
Also, you can also clear all breadcrumbs
```js
/* clear breadcrumbs */
bugsense.clearBreadcrumbs();
```

### Registering events
Bugsense.js provides an easy way for developers to use events in order to handle crashes more effectively.

```js
function ooops() {
  alert('Ooops! Our app just crashed. Please send us an email at support@example.com');
}
bugsense.on("crash", ooops);
```
When this is done, you can unregister the event to avoid spamming your users with countless alerts of notifications.
```js
bugsense.off("crash", ooops);
