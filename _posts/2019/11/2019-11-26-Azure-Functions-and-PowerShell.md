---
title: Azure Functions - Event Driven, Serverless Functions
summary: Using Az Functions for a while, some initial thoughts.

excerpt_separator: <!--more-->

tags:
    - Powershell
    - Azure Functions
    - Serverless
---

I've been spending a signficant amount of time developing and playing with PowerShell for Azure Functions since they were V1 and they were not as good as they could have been.

Fortunately they have been in V2 for quite some time and went to General Availability as of Ignite in November '19. This means that they can "officially" be used for production workloads with official support.

### Drawing a distinction between other Azure Offerings

So my initial reaction when I started playing with PowerShell in Azure Functions was that they were so much nicer to work with than Azure Automation but the more I played with them, the more I realisd that they were **NOT** a direct replacement but an accompaniment.

Azure Automation is still very useful for automation and long running tasks such as reports and Azure Functions really shine when you want event driven actions.

<!--more-->

### Event Driven - Say What?!

Events in Azure can mean a number of things and 9 times out of 10 the first Azure Function you will create is an HTTP trigger which is a manual interaction. As you delve deeper you start seeing situations where event driven is at the forefront of what you want to react to.

### Example Scenario

Azure Key Vault Secret monitoring. This has been in private preview for a while and is still in public preview ([Read the docs here](https://docs.microsoft.com/en-us/azure/key-vault/event-grid-overview)).

> Key Vault integration with Event Grid is currently in preview. It allows users to be notified when the status of a secret stored in key vault has changed. A status change is defined as a secret that is about to expire (within 30 days of expiration), a secret that has expired, or a secret that has a new version available. Notifications for all three secret types (key, certificate, and secret) are supported.

With access to the private preview I setup a test Key Vault with some secrets that had various expiry dates. When the secrets approached expiry there was an event posted to Event Grid. I created an Azure Function which subscribed to this event and sent a notification to Slack to notify that there was a secret about to expire.

The initial evolution of this was to trigger an Azure DevOps pipeline and renew a Let's Encrypt certificate then release a product in a staging environment with an automatically updated certificate.

### Integration With Other Azure Services

Azure Functions has first class support for integration with other services and at time of writing this there are numerous triggers and bindings that can be utilised as part of an Azure Functions. [List can be found here](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings#supported-bindings) and the number of services are growing.

This means that you can be reactive to any events that are raisd from your infrastructure in Azure.

- An Azure IaaS VM is created Event can trigger a Function to automatically tag that VM
- Someone adds an image to blob storage. Azure Function is triggered and uploads that image to congnitive services API and reports back on sentiment analysis for a face in the image.

The options are astounding.

### Summary

In my humble, and often incorrect, opinion I think that Azure Functions with PowerShell is a match made in heaven for Ops people. Being able to setup automation that automatically reacts to events raised in such a seameless way opens up a new world of managing infrastructure at scale and the cost is pennies.

I will follow this post up with some additional content around lessons learned and developing Az Functions that hopefully you will get some use out of.

### Additional Reading Material

- [Create your first PowerShell function in Azure](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-function-powershell)
- [Triggers & Bindings](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings)
- [Azure Functions PowerShell developer guide](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-powershell)