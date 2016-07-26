# Serilog.Sinks.ArchivingFile [![Build status](https://ci.appveyor.com/api/projects/status/75r99ewehvpm2a3r?svg=true)](https://ci.appveyor.com/project/McDeCoderDude/serilog-sinks-archivingfile)

Writes [Serilog](https://serilog.net) events to a text file, one daily with option to arhieve and compress to seperate location. This sink was based on [Serilog.Sinks.RollingFile](https://github.com/serilog/serilog-sinks-rollingfile)

### Getting started

Install the [Serilog.Sinks.ArchivingFile](https://nuget.org/packages/serilog.sinks.archivingfile) package from NuGet. 

```powershell
Install-Package Serilog.Sinks.ArchivingFile
````

To configure the sink in C# code, call `WriteTo.ArchivingFile()` durning logger configuration:

```csharp
var log = new LoggerConfiguration()
    .WriteTo.ArchivingFile("log-{Date}.txt")
    .CreateLogger();

Log.Information("This will be written to the archiving file set");
```

The filename should inlcude the `{Date}` placeholder, which will be replaced with the date of the events contained in file. FileNames use the `yyyMMdd` date format so that files can be ordered using a lexicographic sort:
 
 ```
 log-20160725-1.txt
 log-20160725-2.txt
 log-20160724-3.txt
 log-20160723-4.txt
```

> **Important:** Only one process may write to a log file at a given time. For multi-process scenarios, either use separate files or [one of the non-file-based sinks](https://github.com/serilog/serilog/wiki/Provided-Sinks).

### Limits

To avoid bringing down apps with runaway disk usage the rolling file sink **limits file size to 1GB by default**. The limit can be changed or removed using the `fileSizeLimitBytes` parameter.

```csharp
    .WriteTo.ArchivingFile("log-{Date}.txt", fileSizeLimitBytes: null)
```

For the same reason, only **the most recent 31 files** are retained by default (i.e. one long month). To change or remove this limit, pass the `retainedFileCountLimit` parameter.

```csharp
    .WriteTo.ArchivingFile("log-{Date}.txt", retainedFileCountLimit: null)
```
To move less recent log files to alternative filesystem location.

```csharp
    .WriteTo.ArchivingFile("log-{Date}.txt", fileArchivePath: null, fileArchiveStartCount: null, fileArchiveCompress: false)
```

### XML `<appSettings>` configuration

To use the rolling file sink with the [Serilog.Settings.AppSettings](https://github.com/serilog/serilog-settings-appsettings) package, first install that package if you haven't already done so:

```powershell
Install-Package Serilog.Settings.AppSettings
```

Instead of configuring the logger in code, call `ReadFrom.AppSettings()`:

```csharp
var log = new LoggerConfiguration()
    .ReadFrom.AppSettings()
    .CreateLogger();
```
In your application's `App.config` or `Web.config` file, specify the rolling file sink assembly and required path format under the `<appSettings>` node:

```xml
<configuration>
  <appSettings>
    <add key="serilog:using:ArchivingFile" value="Serilog.Sinks.ArchivingFile" />
    <add key="serilog:write-to:ArchivngFile.pathFormat" value="log-{Date}.txt" />
```
