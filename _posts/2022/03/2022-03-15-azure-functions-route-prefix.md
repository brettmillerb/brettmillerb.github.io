---
title: Making Sense of Azure Functions Routes
summary: Basic understanding of Azure Functions route customisations

excerpt_separator: <!--more-->

tags:
    - Azure Functions
    - PowerShell
    - Azure
---

### Route Prefixes
When you create Azure Functions project which has HTTP endpoints all function routes by default are prefixed with `api` e.g. `myFunctionProject/api/<functionName>`. This is fine for most cases, but it can be confusing as it is not very descriptive.

For example when you initialise a new HTTP trigger function using the Azure Functions CLI, you are prompted for the name for your function. This name is used for the folder within your project but also the name of the function itself.

You can however customise this URI route within your Azure Functions project so that it can be more useful.

<!--more-->

### Changing the Route Prefix

Within your PowerShell Azure Functions project you have a `host.json` file which contains the settings for your Functions project.

Full docs on the `host.json` file can be found here: [host.json reference for Azure Functions 2.x and later](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-host-json).

You can customize or remove the prefix using the `extensions.http.routePrefix` property in the `host.json` file.

Below is an example of how to remove the route prefix from your functions by setting the routePrefix property to an empty string:

```json
{
  "extensions": {
    "http": {
      "routePrefix": ""
    }
  }
}
```

Given that there is no real need to have a pre-defined route prefix I tend to do this on most of my projects to make the URI's easier to read and distinguish from each other, especially when there are multiple functions within the project.

### Changing the Route of your Functions

Now that you have removed the routePrefix from your `host.json` file, you also have the option to customise your functions route on a per function basis.

Below is an example of how to change the route for a specific function by setting the `route` property in the `function.json` file:
```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ],
      "route": "descriptiveFunctionName"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}

```

This allows you greater control over the URI for your function and makes it more descriptive.

If I start a local debug of a basic HTTP function you can see the changes you have made to the route prefix and function route:

#### Before:

![RoutePrefixBefore](../../assets/img/routePrefixAfter.png)

#### After:

![RoutePrefixAfter](/assets/img/routePrefixBefore.png)

### Why does it really matter?!

In this contrived example there's no real need to change the route prefix or the function route but if you have a project which has multiple functions with similar names you may want to change the route to group specific functions together.

For example you could have multiple functions within your project and you want to define the route to coincide with the `METHOD` of the HTTP request for each function.

![Elaborate Example of multiple HTTP Endpoints](../../assets/img/2022-03-18-15-50-34.png)

### Conclusion

This blog post shows you some of the basic changes you can make to the route prefix and function route within your Azure Functions project.

There are some other cool things that you can do with routes within your functions projects which I will cover in a follow up post soon.