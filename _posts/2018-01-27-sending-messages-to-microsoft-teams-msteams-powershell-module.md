---
title: Sending Messages to Microsoft Teams &#8211; MSTeams Powershell Module
date: 2018-01-27 11:41:00

tags:
  - JSON
  - MSTeams
  - O365
  - Powershell
---
As part of our daily jobs we like to keep up to date with the automation tasks that we have running and the results of those automation tasks. This can become very cumbersome very quickly with some scheduled tasks running at 15 minute intervals, daily, weekly, fortnightly and monthly reports and checks.

In most cases these scripts send emails with attachments that nobody reads and get filtered into a sub-folder of their inbox and disappear into an oblivion of ITSM tickets, read receipts and emails from people organising a whip around for their next door neighbours poorly cat.

I decided that (since we don't have slack at work) that I would try and move some of our "Daily Checks/Reports" out of our inboxes to Microsoft Teams utilising the Incoming Webhook Connector.

### Incoming Webhooks

If you've not played with the webhooks yet, they are really easy to setup.

Browse to the Team you wish to receive the notifications and then select a channel. Click the three dots for the options and select Connectors

![TeamsConnector](/_screenshots/TeamsConnector.png)

This will take you to the connectors section where you have a whole host of connectors you can add from 3rd party providers to relay messages to your Teams channels.

Find and select the Incoming webhook from the list of connectors.

![TeamsConnector2](/_screenshots/TeamsConnector-2.png)

click the Configure Button and give your Webhook a name and you can upload your own image which will be displayed with each message in the channel.

![TeamsConnector3](/_screenshots/TeamsConnector-3.png)

Once this has been entered and you click Create you will be presented with a Webhook URL which you will use in your scripts for interacting with your Webhook. Keep this safe and don't commit it to public Git. Yes I did.

Once this is configured you can begin sending messages to your Webhook so that your scripts output and reports can all be seen in Teams.

### Sending Messages To Your Webhook.

When I first began to play with incoming webhooks at the start of 2017, there was very little in the terms of useful documentation and the information that did exist was a little conflicting with the bot framework and O365 message cards.

That was when I found the [MessageCard Playground](https://messagecardplayground.azurewebsites.net/) which showed you at least a guideline of the format required to relay messages to many of the O365 applications and it turns out it was in this scary JSON format. I'd never played with JSON on it's own let alone played with Powershell and JSON.

Turns out it's very much a similar object structure to the Powershell objects that we're used to and love, we just need to understand how to generate the JSON in the correct format.

After much deliberation, removing bits from the MessageCards playground samples, refreshing the page and sending hundreds of tests to my webhook (many with bad payload responses) and figuring out that alot of the sample structure just point blank refused to be accepted, I settled on a final_(ish)_ sort of object structure.

```json
{
    "@type":  "MessageCard",
    "@Context":  "http://schema.org/extensions",
    "Summary":  null,
    "themeColor":  null,
    "title":  null,
    "text":  null,
    "sections":  [
        {
            "activitytitle":  null,
            "activitysubtitle":  null
        },
        {
            "facts":  null,
            "potentialAction":  [
                {
                    "name":  null,
                    "@type":  "OpenUri",
                    "targets":  [
                        {
                            "os":  "default",
                            "uri":  null
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### Building this object with Powershell.

I tried several ways to manipulate the JSON to add the values that I required to the JSON structure but most of the ways I tried meant that the content wasn't dynamic enough to be used in various formats.

That's when I reached out in the Powershell Slack for a solution and [Mark Kraus MVP](https://twitter.com/markekraus) suggested using the `Import-LocalizedData` then converting the JSON object at runtime to a `PSObject` which meant the values could be dynamically assigned with the parameters in my function.

#### Using the MSTeams Module.

You can clone or download the module from my Github: <https://github.com/brettmillerb/MSTeams>

The only function required to be used is the `New-TeamsMessage` function and can be used to pass simple messages or more complex objects to the webhook.

#### Simple Message.

`New-TeamsMessage -Message 'test message for teams' -Color Red -WebhookURI $WebhookURI`

![SimpleTeamsMessage](/_screenshots/SimpleTeamsMessage.png)

#### Detailed Message.

Building a hashtable of information so you can pass this into the `-Facts` parameter.

```powershell
$info = @{
    Enabled = $true
    givenname = 'Brett'
    emailaddress = 'brettm@millerb.co.uk'
    surname = 'Miller'
}
```

Splatting the parameters because it's easier to read and is easier to build dynamically. There's also a proxy switch which I haven't included in this post but can be used for environments which can't talk directly to the internet.

```powershell
$params = @{
    Title = 'Title of the Connector Card'
    Text = 'Text of the Connector Card'
    ActivityTitle = 'Activity title of the card'
    ActivitySubtitle = 'Activity Subtitle of the card'
    Facts = $info
    color = 'green'
    webhookuri = $webhookURI
}
```

Sending the information

```powershell
New-TeamsMessage @params
```
![Detailed Teams Message](/_screenshots/DetailedTeamsMessage.png)

#### Adding Buttons

The next logical step was to allow people to interact with the messages or the ability to provide more detail to people than can be portrayed in a MessageCard. I have only included clickable buttons but there are other types of interaction that could be included if I decide to develop this further.

```powershell
New-TeamsMessage @params -Button {
    Button -Name 'millerb' -Url 'http://millerb.co.uk'
    Button -Name 'google' -Url 'http://google.co.uk'
    Button -Name 'BBC Online' -Url 'http://bbc.co.uk'
}
```

![Detailed Teams Message with Buttons](/_screenshots/DetailedTeamsMessage-buttons.png)

#### Outgoing Webhooks

There was a tweet yesterday which was an announcement of outgoing webhooks which is an amazing feature to be adding to Teams and will allow greater interaction with other systems.

{% twitter https://twitter.com/bill_bliss/status/956236590015160320 %}

Hopefully I will get to have a play with this at some point in the not so distant future, learn some node.js and someone will create a reminder bot which I use a great deal in Slack.

**Link for the announcement**
  
<https://docs.microsoft.com/en-us/microsoftteams/platform/concepts/outgoingwebhook>