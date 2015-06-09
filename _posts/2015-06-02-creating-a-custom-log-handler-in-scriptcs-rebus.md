---
layout: post
title: Creating A Custom Log Handler In scriptcs.rebus
date: 2015-06-09
categories:
- scriptcs
- scriptcs.rebus
published: false
---

In the [latest release of scriptcs.rebus](http://madstt.dk/scriptcs-rebus-0-6-0/), it is possible to receive the log entries from a remote script execution process. On the client side you can define a number of log entry handlers, and specify at which log level they should receive the log entries.

Out of the box there is a console log handler, the `ToConsole()`, which you can add like this:

	Require<RebusScriptBus>()
		.ConfigureBus("someBus")
		.With.AScriptFile("scriptfile_simple.csx")
			.Log.ToConsole()
	.Send()

You can specify the at which log level you would like to receive logs entries in the console like this:

	Require<RebusScriptBus>()
		.ConfigureBus("someBus")
		.With.AScriptFile("scriptfile_simple.csx")
			.Log.ToConsole(LogLevel.Debug)
	.Send()

Now this would make all log entries with log level `Debug` send to the console of the client. This is great, but what if I'd like to get all log entries of `LogLevel.Error` put into a rolling file? Well, easy, you just implement the `IReceiveLogEntries` interface, and then add it.

This is an example of a simple, and yet naive, rolling file implementation:

	public class RollingFileLogger : IReceiveLogEntries
	{
		private StreamWriter _writer;

		public RollingFileLogger()
		{
			_writer = new StreamWriter(new FileStream("logs.txt", FileMode.OpenOrCreate));
			_writer.AutoFlush = true;
		}

		public void Log(string logEntry)
		{
			_writer.WriteLine(logEntry);
		}

		public LogLevel LogLevel { get; set; }
	}

Now we can add this rolling file logger to our list of log entry handlers like this:

	Require<RebusScriptBus>()
		.ConfigureBus("someBus")
		.With.AScriptFile("scriptfile_simple.csx")
			.Log.ToConsole(LogLevel.Debug)
			.Log.To<RollingFileLogger>(LogLevel.Error)
	.Send()

This sends the all `LogLevel.Debug`, and below, entries to the console, while all `LogLevel.Error`, and below, are sent to a rolling file.

Other examples of log entry handlers could be SQL Server, MongoDB, RavenDB, Azure Blob Storage, etc. I might post examples of these in futures posts.

Throw me a comment below or ping me on Twitter, to let hear what you think of this feature.

 

