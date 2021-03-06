---
layout: post
title: scriptcs.rebus 0.6.0
date: 2015-05-06
categories:
- scriptcs
- scriptcs.rebus
published: true
---

I'm really excited to finally announce the release of scriptcs.rebus 0.6.0. The 0.5.0 version was release back in November 2014, so this release has been under development for some time. This release is feature packed, its the version with the most new features since 0.1.0. It is a major step towards 1.0. It has been a great and fun ride, and I could have dumped more features in there, but on the other hand I'd also like to share these new features with you guys.

At the time of writing, scriptcs.rebus has been downloaded more than [850 times on nuget.org](https://www.nuget.org/packages/ScriptCs.Rebus/). Still, I haven't received any feedback what so ever. The numbers count for themselves, there is someone out there using it. So, basically, I'm doing the features and the fixing the issues I find. If you're using scriptcs.rebus, please let me know how you're using it and if there is anything you're missing. 

## What's New
A bunch. In the following subsections I outline all the new features that is packed into 0.6.0.

### Dynamic WebAPI Controller Generation
scriptcs.rebus now has built-in support for dynamically creating new controllers inside a running WebAPI application. Sounds awesome? It is. How does this work? I've extended scriptcs.rebus.hosting with the capabilities to take some controller logic and on-the-fly wrapped it into a controller class and served for the API. The scripting logic is saved to disk, so that if the server is restarted, the controllers are still available.

In which context could this feature become relevant, you may ask? Well, instead of having to take your API down for maintenance, you create new controllers without. You could also use this in the initial phases of creating your WebAPI. Just throw an empty WebAPI in the air, and dynamically create controllers as you explore the domain.  

Let's look at an example. The following code sends some controller logic to a WebAPI:

	Require<RebusScriptBus>()
		.ConfigureBus("hostedBus")
		.With.AWebApiController("Customer")
			.AsAScript("public string Get() {return \"Customer name is Adam!\";}")
		.Send()

The `AWebApiController()` extension method takes a single parameter, which is the desired controller name. The `Get()` method is sent to the WebAPI. scriptcs.rebus.hosting saves this script, and on the next request to the WebAPI, a new controller is dynamically created and served. On all subsequent calls WebAPI serves the controller from memory. Script execution only happens on server restart or if a new controller script is sent to the WebAPI.

It is also possible to send your controller script as file, like this:

	Require<RebusScriptBus>()
		.ConfigureBus("hostedBus")
		.With.AWebApiController("Customer")
			.AsAScriptFile("CustomerController.csx")
		.Send()

Now to set this script execution feature in your WebAPI, you should reference the [`scriptcs.rebus.hosting` NuGet package](https://www.nuget.org/packages/ScriptCs.Rebus.Hosting/). Then add the following piece to your `WebApiConfig.cs`

	var bus = new MsmqBus("hostedBus");
	bus.RegisterHandler(() => new ReceiveWebApiControllerHandler());
	bus.Start();

This will enable your server to receive and correctly save the controller script appropriately. Now, also in your `WebApiConfig.cs` add:

	var builder = new WebApiControllerBuilder();
	builder.Build(config);

This will pick up the scripts and turn them into actual WebAPI controllers. 

The credits for this feature goes to [Glenn Block](https://twitter.com/gblock), who made some initial spiking on this. I basically teared it apart and put it into a messaging and scriptcs.rebus context.

You still have the same options with regard to downloading and referencing NuGet dependencies, local dependencies, etc. as you are used to from previous versions. See more [here](https://github.com/scriptcs-contrib/scriptcs-rebus#sending-scripts-to-a-host). Just as it is now:

	Require<RebusScriptBus>()
	.ConfigureBus("hostedBus")
		.With.AWebApiController("Customer")
			.AsAScript("public string Get() {return \"Listen to your customers!\";}")
		.AddFromNuGet("MongoDB.Driver")
	.Send()

This would download and reference the MongoDB driver for usage in your controller.

### Returning Script Execution Output
This is a great feature, and nonetheless quite unique in the script execution context. Currently, we can send scripts to host application for execution. We can use this feature to extend running applications with new features, dynamically. This is possible already today, but in this release I've added a feature to receive any script execution output in the client. This is possible since the backbone in scriptcs.rebus is [Rebus](https://github.com/rebus-org/Rebus/), which makes it possible for the server, the host application, and the client, the script execution initiator, to communicate.

Currently all script execution output is sent to the console of the host. Often, or in many cases, the host application is blackbox and it is not possible to see any output.

Lets take a look at an example:

	Require<RebusScriptBus>()
	.ConfigureBus("someBus")
		.With.AScriptFile("scriptfile_simple.csx")
			.Log.ToConsole()
	.Send()

In this example a script file, `scriptfile_simple.csx`, is sent the a host application for execution. The `.Log.ToConsole()` extension method tell the server to send all output back to the console of the client. This is a great way to get some insights into how your scripts are executed.

By default the log level is set to `LogLevel.Info`, other options are `LogLevel.Debug`, `LogLevel.Error`, and `LogLevel.Trace`, with raising density. The log level is set as a parameter on the `ToConsole()` method like this:

	Require<RebusScriptBus>()
	.ConfigureBus("someBus")
		.With.AScriptFile("scriptfile_simple.csx")
			.Log.ToConsole(LogLevel.Debug)
	.Send()

This release ships with the built-in `.ToConsole()`, but there is an extension point to adding your log receiver. This is done through the `To<T>`:

	Require<RebusScriptBus>()
	.ConfigureBus("someBus")
		.With.AScriptFile("scriptfile_simple.csx")
			.Log.To<RollingFile>()
	.Send()

Where T is an implementation of the `IReceiveLogEntries` interface. In this case a rolling file. Other examples could the Azure Web Storage or SQL Server. This interface contains a single `Log()` method and a `LogLevel` property.

This opens up for some pretty advanced logging scenarios. Like the following:

	Require<RebusScriptBus>()
	.ConfigureBus("someBus")
		.With.AScriptFile("scriptfile_simple.csx")
			.Log.ToConsole()
			.Log.To<RollingFile>(LogLevel.Debug)
	.Send()

Here all log entries of log level `Info` is sent to both the console and the rolling file, but the rolling file also receives log entries of log level `Debug`. So cool..

### New Hosting API
As you can see from the above examples, I've made some breaking changes to the hosting API. These changes were necessary, so I could add both logging and incorporate the WebAPI handler. 

Previously you could only specify whether to use logging, which meant that the host would spit out logging entries to the host's console. This is in many cases not a great solution. And since version 0.4.0, were scriptcs.rebus.hosting introduced, you would use the `.WithAScriptFile()` or `.WithAScript()` to send a scriptfile or script to a host. This is now changed, so there is now an extension point for future specific handlers.   

### Bug Fixes
This release of scriptcs.rebus also contains the usual number of bug fixes. There is a bunch of minor fixes that I'm not going to mention here, but focus on the larger and rather more important fixes.

#### Rebus Patterns
Rebus is designed with a number of messaging patterns. You can read more on these [here](https://github.com/rebus-org/Rebus/wiki/Different-bus-modes). So far, I've relied on the [Server](https://github.com/rebus-org/Rebus/wiki/Different-bus-modes#server) pattern, but in the context of scripting this pattern has some unfortunate side-effects like the sender handling its own messages etc. This has sort of changed in 0.6.0. For simple messaging, it has changed to [One-way Client Mode](https://github.com/rebus-org/Rebus/wiki/Different-bus-modes#one-way-client-mode), but when specifying to use logging, we still use Server. This makes it possible to to provide a return address for the host to reply with log entries.

In short, no more weird behavior, were messages disappear due to bad messaging design.

#### Multiple Handlers
The idea of having multiple handlers is great, but it seemed rather buggy. It turned out that it was only the last added handler that was actually registered. This is now fixed, so that all handlers registered are actually active.

### Azure Service Bus and RabbitMQ as Script Packs
A rather annoying thing about using the Azure Service Bus or RabbitMQ extensions for scriptcs.rebus, has been the necessity of adding a using statement. This is now fixed by turning the extension projects into pure script packs. This means that the necessary namespaces are specified inside the script pack, and it should not be necessary to add the using statement to your script anymore. 

### Latest Dependencies
Last but not least, I've updated scriptcs.rebus to the latest versions of scriptcs and rebus.

Should you have any questions regarding any of the above, don't hesitate contacting me. I'm available on [Twitter](https://twitter.com/madstt), but also on [Gitter](https://gitter.im/scriptcs-contrib/scriptcs-rebus).