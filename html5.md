## HTML5

### Using the Javascript library

Download the <a href="">bugsense-<strong></strong>.js</a><strong><a href="/releases/html5" id="releases">(Release Notes)</a></strong> file and store in it along with your other Javascript files.

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
    "bugsense": "1.1.1",
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

Older mobile browsers do not support the window.onerror callback and therefore the plugin will not receive any uncaught exception.

When there's only the Error object caught, error.stack will be parsed to get the url and line number.

Deobfuscation or retracing for minified and/or obfuscated Javascript files is not supported yet.

Bugsense.js uses CORS to send crash reports.

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
