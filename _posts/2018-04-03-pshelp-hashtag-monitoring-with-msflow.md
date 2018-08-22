---
title: '#PSHelp hashtag monitoring with MSFlow'
date: 2018-04-03 16:22:21

tags:
  - Microsoft Flow
  - O365
  - Powershell
  - Productivity
---
I spotted that Joshua King ([@WindosNZ](https://twitter.com/WindosNZ)) had a blog post about how to monitor Twitter for the use of the hashtag [#PSHelp](https://twitter.com/search?f=tweets&vertical=default&q=%23PSHelp&src=typd)

You can read his blog post here <https://king.geek.nz/2018/03/20/pshelp-twitter/> where he utilises his BurntToast module for notifications. Bookmarked the module some time ago but not had a chance to play with it yet.

I thought this was a good idea and immediately set up my TweetDeck to have a column for #PSHelp and seen a _little_ activity but more importantly immediate responses are good when you're asking a question on something like Powershell. In order to provide immediate responses I'd have to keep one eye on TweetDeck which isn't always feasible when working so I decided to have a play with Microsoft Flow to see if I could trigger notifications on the use of the #PSHelp hashtag.

### Accessing Microsoft Flow

You can log in to Microsoft Flow by going to <https://flow.microsoft.com> and logging in with your O365 account where you are presented with quite a bit of information on how to create flows and templates that are provided for you to use straight away.

![Flow Templates](/assets/img/FlowTemplates.png)

Looking at the different connectors from the Navigation bar and clicking on the Twitter connector shows you some examples of triggers that can be used to start a flow and one that piqued my interest was the push notifications so that I could be notified on my phone when the hashtag was used and respond quite quickly.

![Flow Push](/assets/img/flow-push.png)

Click on the relevant trigger and it will take you to the trigger settings so you can select a hashtag or keywords to use as the trigger. You will have to log in to Twitter to enable this.

![Flow Settings - 1](/assets/img/FlowSettings-1.png)

Enter the hashtag that you would like to use as the trigger and click the Create Flow. This will then take you to the flow that has been configured. You can customise this with a description and add some further dynamic content to the push notification such as the user who has posted and the text so you can see the content of the tweet immediately.

![FlowAdvancedSettings](/assets/img/FlowAdvancedSettings.png)

This works quite well but in order to receive push notifications you need to have the Microsoft Flow app installed on your mobile device. I am testing sending a Teams Channel notification on the hashtag being used so that I get a Teams toast notification so it's immediately apparent whilst working on my laptop.

This was my first foray into Microsoft Flow but it was really simple to setup and worked really well so I think I'll be looking at some more complex flows with conditional logic to see what other benefits it has.

**Update:** Microsoft Teams notification needs a little bit of work to display dynamic content but it worked all the same.