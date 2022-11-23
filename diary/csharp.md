---
layout: page
title: CSharp (dotnet)
permalink: /diary/csharp
---
## Azure Messaging Servicebus Libary
Using The [Azure.Messaging.ServiceBus](https://github.com/Azure/azure-sdk-for-net/blob/Azure.Messaging.ServiceBus_7.0.1/sdk/servicebus/Azure.Messaging.ServiceBus/README.md) library, NuGet package with retry options
```cs
 ServiceBusClient client = new ServiceBusClient(serviceBusConnectionString, new ServiceBusClientOptions()
            {
                RetryOptions = new ServiceBusRetryOptions()
                {
                    Mode = ServiceBusRetryMode.Fixed,
                    Delay = TimeSpan.FromSeconds(10),
                    MaxDelay = TimeSpan.FromMinutes(3),
                    MaxRetries = 30
                }
            });

ServiceBusProcessor processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());

// throwing an exception in MyMessageHandlerAsync on purpose
// to test out the retries configuration
processor.ProcessMessageAsync += MyMessageHandlerAsync;

// The uncaught exception causes this method to execute.
// Processing is attempted 10 times with
// virtually no delay between each attempt.
// After the 10th attempt, the message goes to deadletter,
// which is expected.
processor.ProcessErrorAsync += MyErrorHandler;
```

 Here are many more [samples](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/servicebus/Azure.Messaging.ServiceBus/samples)

 ## How to change the default URL for a asp .net app
 A very good link about how to [set the default url for your asp.net app](https://andrewlock.net/5-ways-to-set-the-urls-for-an-aspnetcore-app/)

 ## A few examples right here
 ### The command line. Command line arguments override the value of the environment variables if they're set. Simply use the --urls parameter:
```bash
dotnet run --urls "http://localhost:5100"
```
As before, you can pass multiple URLs to listen on by separating them with a semicolon:
```bash
dotnet run --urls "http://localhost:5100;https://localhost:5101"
```

### Use URLS
```cs
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
                webBuilder.UseUrls("http://localhost:5003", "https://localhost:5004");
            });
}
```
