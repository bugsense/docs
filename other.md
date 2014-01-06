## JIRA 5 Integration

If you are using [BugSense](http://www.bugsense.com/features/jira) for error tracking and JIRA for project tracking/issue management this feature will make your life a lot easier. Forget the time-consuming process of manually creating your issues in [JIRA](http://www.atlassian.com/software/jira).

BugSense will not only take care of creating the issue for you, but depending on the number of times the error has occured, BugSense can increase the priority of the issue. In addition, when an issue is closed and you set a fix version BugSense will make sure your users know about it!

And one more thing: unlike JIRA Mobile Connect, we group the same errors together so that you won't be overwhelmed by them!

#### Implementation

First things first: before connecting JIRA with BugSense make sure that the following fields are enabled: 

* Affects Version/s
* Project
* Issue Type
* Environment
* Summary (The error message)
* Description ( Error stack trace)

Now just follow the steps below and leave the rest to us:

1. Log into [BugSense](http://www.bugsense.com/account) as the owner of the project.
2. Go to 'My Account' and click the [JIRA 5](http://www.bugsense.com/account#jira) option on the sidebar.
3. Enter your [JIRA 5](http://www.atlassian.com/software/jira) server URL.
4. Enter the username and password of your [JIRA 5](http://www.atlassian.com/software/jira) account (preferably project owner or any user who has the right to create issues).
5. Click the submit button.
6. Now go to your project settings and click on [JIRA 5](http://www.atlassian.com/software/jira) on the sidebar.
7. You should be able to see all the projects that you have on JIRA.
8. Choose the project you want to link with the [BugSense](http://www.bugsense.com/signup) project.
9. Choose the occurrences threshold (default throttle is set at 10).
10. Choose the limit to increase priority.
11. That's it! You're set to go.

A [Remote Issue Link](https://developer.atlassian.com/display/JIRADEV/JIRA+Remote+Issue+Links) is also created in JIRA, just in case you miss the awesome BugSense dashboard. 

*We recommend that JIRA versions be the same as the app's release version, so that when you release a new version, the new version in JIRA is created as well.*
