---
layout: post
title: Squirrel - The Basics
date: 2016-03-11
categories:
- squirrel
published: true
---

> Squirrel is like ClickOnce, but works. 

This intriguing thesis made me wanna know more. By coincidence, I got the opportunity to spend the last few days fiddling with [Squirrel](https://github.com/Squirrel/Squirrel.Windows). Squirrel is an install and update framework. It comes in a few flavours, for Mac, Windows, and iOS. In this post we'll focus on Squirrel.Windows, the Windows flavour.

If you've ever had the pleasure of working with [ClickOnce](https://msdn.microsoft.com/en-us/library/t71a733d.aspx), you know that it has its flaws and that it can really be a pain to work with and maintain.

This post takes you through all steps of packing your application for release, distribution, installation, and updating.

## Packaging
Squirrel is based upon NuGet. Your application is therefore wrapped into a NuGet package before Squirrel can make an installer. 

I would recommend creating a `.nuspec` file and use the `nuget pack` command to create the `.nupkg` file. For a quick demo you can create the `.nupkg` using a tool like [NuGet Package Explorer](https://github.com/NuGetPackageExplorer/NuGetPackageExplorer).

You `.nupkg` file should contain a single `lib` folder with a `net45` folder inside. .Net 4.5 is a requirement for Squirrel, but it is not restrained to only be used with .Net 4.5 projects. Actually, you can use Squirrel to distribute and install any sort of desktop application on Windows and Mac.

Then inside your `net45` folder you put the content you want to install. For .Net projects, this is often the output of a Release build. You can remove all IDE related files and debugging symbols, such as `.pdb`, `.vshost` etc. Set the appropriate metadata on the `.nupkg` file. The icon will be used on the application, and the package summary will be used as Name in the Programs and Features list.

Once you have a nice `.nupkg` file, you use Squirrel to Releasify the application:

    > squirrel.exe --releasify myapp.1.0.0.nupkg
    
 This means that Squirrel wraps it into a folder, a Releases folder per default, next to the `.nupkg` file. The `Squirrel.exe` is part of the `squirrel.windows` NuGet package. This means that an easy way to access it, is through the NuGet Package Manager Console in Visual Studio. `Squirrel.exe` comes with a number of command line options, which are worth looking into.
 
 Inside the Releases folder is the `.nupkg` file, a `Setup.exe`, a `Setup.msi`, and a RELEASES file. The two Setup files are used for installation. The RELEASES file contains information, a SHA, name, and size of the releases in the folder.
 
 The content of the Releases folder is your installation pieces. Just run the `Setup.exe` or the `Setup.msi` to install your application. 
 
## Distribution
One of the key features of Squirrel is that you can distribute your application from almost anywhere.

You can copy the content of the Releases folder to a local path, a Dropbox folder, Amazon S3, Azure Blob Storage, Microsoft IIS, etc. There are no requirements what so ever. You can even change the path or url and move the installation files. If you wanna use the background updating mechanism, you just have to update the path or url in your application.

## Updating
To get started with updating your application you would need to pick up the [Squirrel.Windows NuGet package](https://www.nuget.org/packages/squirrel.windows/):

    > install-package squirrel.windows

This will give you the Squirrel namespace. Now you have two options, you can take the Easy-Mode or the Step-Wise path.

### Easy Mode
When you have referenced the NuGet package you use the 'Easy Mode', which will run all the tasks necessary to check, download and apply new releases.

You can use the 'Easy Mode' with the following snippet:

    using (var mgr = new UpdateManager("C:\\Projects\\MyApp\\Releases"))
    {
        await mgr.UpdateApp();
    }

You can place this snippet somewhere that makes sense. Some applications checks for updates on startup others have a separate UI for this.

If there is a new release for the application it will be installed silently in the background, and when the user restarts the application he will be using the new version.

### Step-Wise
Opposite to the Easy-Mode, the Step-Wise updating mechanism is the solution if you would like to notify the user that there is a new release, and ask the user for permission to install. 

As with Easy-Mode it is good practice to put the `UpdateManager` into a using statement. It is required that the `UpdateManager` is disposed prior to application termination. You can either handle this manually or use a using statement.

#### CheckForUpdate()
First step is to call the `CheckForUpdate()` method on the `UpdateManager` object:

    using (var mgr = new UpdateManager("C:\\Projects\\MyApp\\Releases"))
    {
        var updateInfo = await mgr.CheckForUpdate();
    }

You can then use the `updateInfo` object to check the installed version and whether there is any new releases available. Using the `FetchReleaseNotes()` method you can retrieve the release notes from the `.nupkg`, display them to the users and let them decide whether they'd like to install the update.

#### DownloadReleases()
So if you want to install the release, we can extend our small sample with the following:


    if (updateInfo.ReleasesToApply.Any())
    {
        newVersion = updateInfo.FutureReleaseEntry.Version;

        Console.WriteLine("New version: {0}", newVersion);

        Console.Write("New version available. Install? (y/n) ");
        if (Console.ReadKey().Key == ConsoleKey.Y)
        {
            Console.WriteLine("Beginning to download...");

            mgr.DownloadReleases(updateInfo.ReleasesToApply).Wait();
            Console.WriteLine("... done downloading.");
        }
    }
What happens is that we check the `updateInfo` for any releases to apply. This would be any new releases. If this is the case we ask the user whether to install the latest release. If this is the case to call the `DownloadReleases()` method on the `UpdateManager` object. Upon downloaded, we can apply the release.

The downloaded release will be put into a folder next to the current.

#### ApplyReleases()
Once we have downloaded our releases, we can apply the releases using the `ApplyReleases()` method. This would extend our sample with this:

    Console.WriteLine("Beginning to apply release...");
    mgr.ApplyReleases(updateInfo).Wait();
    Console.WriteLine("... done applying.");
This step will change shortcut pointers from the current installed release to the newly downloaded.

#### CreateUninstallerRegistryEntry()
It is by all good practice to create an uninstaller for the user to uninstall the application in the Programs and Features page. This is done with the following:

    Console.WriteLine("Creating uninstaller...");
    mgr.CreateUninstallerRegistryEntry().Wait();
    Console.WriteLine("... done creating uninstaller.");

### Restarting Your Application
Whether you use the easy-mode or step-wise, you can restart your application after applying a new release. Squirrel has a buit-in feature for this. It is a static method on the `UpdateManager`:

    UpdateManager.RestartApp();
    
This is the final step in creating a step-wise updating process for your application.

## Gotchas
During my experimentation with Squirrel I came across some gotchas, which could be of interest to others.

There is an `ArgumentException` with a message saying, `You must dispose UpdateManager!`

You will run into this issue if you wrap your `UpdateManager` into a using statement and awaits the any of the async methods on `UpdateManager`, like this:

    using (var mgr = new UpdateManager("C:\\Projects\\MyApp\\Releases"))
    {
        var updateInfo = await mgr.CheckForUpdates();
    }

The runtime will not be able to dispose your `UpdateManager` object before the application terminates. 

To resolve this you can either make the `CheckForUpdate()` call synchronous, applying `.Result` to `CheckForUpdate` or make sure to dispose the `UpdateManager` when finalizing the AppDomain, like this:

    AppDomain.CurrentDomain.ProcessExit += () => DisposeUpdateManager(); 
    
The other gotcha I ran into was a `AbandonedMutexException` with a message saying, `Leaked a Mutex!`

Again this is related to the locking mechanism of the `UpdateManager`. I got this exception when I tried to call the `UpdateManager.RestartApp()` method inside my using statement. This would close the running application before the `UpdateManager`'s mutex is released.
 
To resolve this, move the `UpdateManager.RestartApp()` outside the using statement.

## Conclusion
Coming with some experience using ClickOnce, it is a relief to use Squirrel. It is much more lean, and requires so much less effort. Both in maintenance and development.

I have put my sample application on [GitHub](https://github.com/madstt/SquirrelTest).

If you have any comments or feedback on this post, throw them into the comments. 


