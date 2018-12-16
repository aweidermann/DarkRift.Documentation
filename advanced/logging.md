# Logging
## Writing to Logs
As we've said before, there’s quite a bit of code to the logging system in order to facilitate it outputting to multiple sources and with different priorities. To plugins, however, there is a single method for logging: Plugin.WriteEvent.

WriteEvent takes a message, log type and an optional exception (if one occurred) and will handle the write out to the necessary source. The log type allows you to specify how important the event is and hence will also determine what files it is output to, dependent on the configuration file.

It’s recommended that you use WriteEvent fairly sparingly so that it’s easy to find the events later in your server logs but it is up to you how much you want to output!

## Configuring Log Output</title>  
There are 3 log writers built into DarkRift: the ConsoleWriter, the DebugWriter and the FileWriter. The ConsoleWriter will output any messages to `Console.WriteLine` and so will appear in the standard out stream or console window if it's not been redirected. The DebugWriter will output any messages to `Debug.WriteLine` and so will appear in VisualStudios debug windows for example.

The FileWriter will output messages to the file that you specify allowing you to retain the logs easily. The file will be created at startup based on the path specified in the `file` attribute of the `settings` child element. E.g
```xml
<logWriter name="FileWriter1" type="FileWriter" levels="trace, info, warning, error, fatal">
  <settings file="Logs/{0:d-M-yyyy}/{0:HH-mm-ss tt}.txt" />
</logWriter>
```
The file path is formatted using `String.Format` where the first argument is DateTime.Now so files based on the date or time are possible (as shown above).

## Defining a Custom Log Writer
LogWriters are implemented in the same way as plugins are but instead of inheriting from Plugin they inherit from LogWriter and instead of taking PluginLoadData they take LogWriterLoadData. They're loaded in using the same code system as plugins so you just need to drop them into your Plugins directory for them to be loaded.

Each LogWriter has a WriteEvent argument that should handle writing the message to their output location. In most cases you should use WriteEventArgs.FormattedMessage as this is well optimized, will only be run once across all log writers and by keeping the same format for logs it is easier to read back.

Of course it's possible to access the individual components of the event such as the sender, message, the time that it was logged etc.