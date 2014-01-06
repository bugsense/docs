### Using the Windows 8 library

**Requirements**

1. Visual Studio 2012 for Windows Phone (Update 1 or later)
2. NuGet (latest version)
3. .NET Framework 4.5 or later.

**Installation**

To install the BugSense NuGet package by using the [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console), do the following:

1. Open the project you want to work with BugSense.
2. On the TOOLS menu, point to Library Package Manager, and then click Package Manager Console.
3. In the Package Manager Console at the PM> prompt, type the following: 

```Install-Package BugSense.W8```

To install the BugSense SDK by using the Package Manager window in Visual Studio:

1. Open the project you want to work with BugSense.
2. On the **TOOLS** menu, point to **Library Package Manager**, and then click **Manage NuGet Packages for Solution**
3. In the **Manage NuGet Packages** window, click **Online** from the list on the left, and then enter "BugSense.W8" into the Search Online field in the upper-right corner. Several BugSense packages appear in the list.
4. Click the **Install** button for the BugSense package that you want to install.
5. In the **Select Projects** window, select the checkboxes next to the projects in which you want to install the package, and then click **OK**

You can also use the [Package Management Dialog](http://docs.nuget.org/docs/start-here/managing-nuget-packages-using-the-dialog) in Visual Studio to install the 'BugSense.W8' package.

![Installing BugSense via NuGet in Visual Studio 2012](/static/images/landing/screens/install.jpg "Installing BugSense via NuGet in Visual Studio 2012")

Alternatively, you can download the <a href="">BugSense-W8-<strong></strong>.zip</a><strong><a href="/releases/windows8" id="releases">(Release Notes)</a></strong> file & add a reference to **BugSense-W8.dll** in your Windows Project.

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

