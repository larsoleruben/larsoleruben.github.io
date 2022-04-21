---
layout: page
title: C#
permalink: /diary/csharp
---
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