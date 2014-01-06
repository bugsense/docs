## API v1

BugSense provides a RESTful API in order to read data and upload dSYM files. BugSense provides an API that can be used to retrieve crash reports and insights but also upload dSYMs and manage projects and teams. This interface incorporates several elements of the REST tradition, making access to these actions consistent and intuitive. All responses are in JSON format.

This is the first version of the API, and we look forward to your feedback.

### Access Token

You will find your access token under your [account page](https://www.bugsense.com/account).

### Available API Actions

Here are some examples of supported API commands, and the corresponding responses. These examples are shown in the form of 'curl' commands. Curl is a command line utility used to perform HTTP requests, and is available on most Unix systems, but you can use any tool or language which can submit a corresponding HTTP request.

#### Get projects

```curl --header "X-BugSense-Auth-Token: YOUR_API_TOKEN" https://www.bugsense.com/api/v1/project/YOUR_API_KEY/errors.json```

Response

```json
{
    "order": "created",
    "total": 1,
    "filters": {
        "status": "new",
        "appver": null,
        "tag": null,
        "osver": null
    },
    "error": null,
    "per_page": 20,
    "pages": 1,
    "page": 0,
    "data": [
        {
            "resolved": null,
            "updated": "2013-02-20T17:38:24.348280",
            "os_versions": ["4.0.3"],
            "reopened": 0,
            "tags": [],
            "is_open": 1,
            "id": 112535524,
            "resolved_at": null,
            "created": "2013-02-20T17:38:24.346940",
            "counter": 1,
            "app_versions": ["1.4"],
            "klass": "java.lang.IllegalStateException",
            "msg": "java.lang.IllegalStateException: Can not perform this action after onSaveInstanceState",
      "where": "FragmentManager.java:1265"
        }
    ],
    "results": 1,
    "more": 0
}
```

Additional Parameters

* days, ex 7
* status, [new, resolved]
* appver, ex: 1.6
* osver, ex: 4.1
* tag
* message, ex: outofmemory
* order [created, updated, total]

#### Get Error

```curl --header "X-BugSense-Auth-Token: YOUR_API_TOKEN" https://www.bugsense.com/api/v1/project/YOUR_API_KEY/errors/ERROR_ID.json```

Response

```json
{
  "stacktrace": [
    "java.lang.IllegalStateException: Can not perform this action after onSaveInstanceState",
    "at android.app.FragmentManagerImpl.checkStateLoss(FragmentManager.java:1265)",
    "at android.app.FragmentManagerImpl.popBackStackImmediate(FragmentManager.java:451)",
    "at android.app.Activity.onBackPressed(Activity.java:2121)",
    "at dalvik.system.NativeStart.main(Native Method)"
  ],
  "error": {
    "resolved": null,
    "updated": "2013-02-15T19:29:50.698890",
    "os_versions": ["4.0.4"],
    "reopened": 0,
    "tags": [],
    "is_open": 1,
    "id": 110230249,
    "resolved_at": null,
    "created": "2013-02-15T19:29:50.696440",
    "counter": 1,
    "app_versions": ["1.3"],
    "klass": "java.lang.IllegalStateException",
    "msg": "java.lang.IllegalStateException: Can not perform this action after onSaveInstanceState",
    "where": "FragmentManager.java:1265"
  }
}
```

#### Get Insights

```curl --header "X-BugSense-Auth-Token: YOUR_API_TOKEN" https://www.bugsense.com/api/v1/project/YOUR_API_KEY/analytics.json```

Response

```json
{
   "unique_affected_users": [3, 0, 4, 3, 2, 1, 0],
   "crashes": [3, 0, 4, 3, 2, 1, 0],
   "sessions": [986, 987, 1069, 1368, 1421, 1288, 401],
   "days": 7,
   "unique_users": [564, 535, 588, 769, 765, 653, 234],
   "overquotas": false
}
```

 In order to get other Insights you should replace **analytics.json** in the URL with one of the following:

* analytics/devices.json
* analytics/versions.json?category=app_versions
* analytics/versions.json?category=app_versions
* analytics/versions.json?category=os_versions
* analytics/top_errors.json
* analytics/trending_errors.json

#### Upload dSYM

```python bugsense_cli.py YOUR_API_KEY YOUR_API_TOKEN -dsym ../dsyms/crash-me3.app.dSYM```

 In order to upload a dSYM use the sample [Python BugSense API client](https://gist.github.com/PanosJee/5004886).
