### Using the Windows 8 library

**Requirements**

1. Visual Studio 2012 for Windows Phone (Update 1 or later)
2. NuGet (latest version)
3. .NET Framework 4.5 or later.

<a href="">BugSense-iOS<strong></strong>.zip</a><strong><a href="/releases/windows8" id="releases">(Release Notes)</a></strong>

**Installation**

To install the BugSense NuGet package by using the Package Manager Console, do the following:

1. Open the project you want to work with BugSense.
2. On the TOOLS menu, point to Library Package Manager, and then click Package Manager Console.
3. In the Package Manager Console at the PM> prompt, type the following: 

```Install-Package BugSense.W8```

Then, inside the **App.xaml.cs** file, add the following code inside the constructor.

```c#
public App()
{
  // Initialize BugSense
  BugSenseHandler.Instance.InitAndStartSession(new ExceptionManager(Current), "API_KEY");
  // Other Windows Store specific operations
}
```

#### Use BugSense along with the Visual Studio debugger

You can use BugSense alongside the Visual Studio debugger.

