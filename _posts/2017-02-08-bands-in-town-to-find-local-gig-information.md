---
title: Bands in Town to Find Local Gig Information
date: 2017-02-08T01:29:55+00:00
tags:
  - API
  - Powershell
---

I help administer a local community website for the town where I used to live <https://www.bedlington.co.uk/> and it got me thinking about how we could automate the calendar entries so that people could use the calendar to see which local events were taking place.

Up until this point we had been manually adding posts when we seen them on Facebook or Twitter or around the town which was laborious and time-consuming to say the least.

I used some popular search engines to look for gig guides or calendars and it turns out that Bands in Town website has a developer API. Sweet!

So I got about reading the API details provided to see what I could do with it.

<https://www.bandsintown.com/api/overview>

> The Bandsintown V2 API currently does not require authentication but does require that an application ID (app_id) parameter be passed with every request to identify yourself. See the example below:
> 
> http://api.bandsintown.com/artists/Skrillex/events.json?api\_version=2.0&app\_id=YOUR\_APP\_ID
> 
> The application ID can be anything, but should be a word that describes your application or company.

So using the provided examples I should be able to use Invoke-RestMethod to get details from the API and receive responses in XML or JSON format. The site gives you some pretty good examples to get you started and the _app_id_ provides authentication and just has to be a unique identifier for your application.

```powershell
Invoke-RestMethod "http://api.bandsintown.com/artists/LiveInjection/events.json?api_version=2.0&app_id=Bedlington"
```

```powershel
id                 : 12738749
artist_id          : 941660
artist_event_id    : 15964405
title              : Live Injection @ The Mill House Inn in Hartlepool, United Kingdom
datetime           : 2017-02-11T19:00:18
formatted_datetime : Saturday, February 11, 2017 at 7:00PM
formatted_location : Hartlepool, United Kingdom
ticket_url         : 
ticket_type        : 
ticket_status      : unavailable
on_sale_datetime   : 
facebook_rsvp_url  : http://www.bandsintown.com/event/12738749?app_id=Bedlington&artist=Live+Injection&came_from=67
description        : 
artists            : {@{id=941660; name=Live Injection; mbid=; image_url=https://s3.amazonaws.com/bit-photos/large/7198171.jpeg; 
                     thumb_url=https://s3.amazonaws.com/bit-photos/thumb/7198171.jpeg; facebook_tour_dates_url=http://www.bandsintown.com/LiveInjection/facebookapp?came_from=67; 
                     facebook_page_url=https://www.facebook.com/LiveInjectionOfficial; tracker_count=212; url=LiveInjection; website=}}
venue              : @{name=The Mill House Inn; place=The Mill House Inn; city=Hartlepool; region=England; country=United Kingdom; latitude=54.688244; longitude=-1.214992}
```

This is very detailed and parsing JSON I can begin to look for relevant gigs based on my location from the 34 events returned for the artist Live Injection.

After doing some digging around in the JSON object I can see that the band are playing a gig in Bedlington so I can look at extracting the information from this gig and add it to a variable using the following.

```powershell
$BedEvent = $events | where {$_.venue.city -match "Bedlington"}
```

As I will likely be using this information to add a calendar entry to the community calendar, I need to compare the form fields from the calendar to see which information I need to capture.

- Event Start (including Date and Time)
- Event End (including Date and Time)
- Title (The name of the Event)
- Description (Elaborate on the event information)
- Event Location (This is actually a Google Location Lookup Field)


So now that I know which fields I require, I can start building my properties to collect the relevant information for the event.

```powershell
$properties = [ordered]@{
    Title = $BedEvent.title
    Artist = $BedEvent.artists.name
    Date = [datetime]$BedEvent.datetime
    Town = $BedEvent.venue.city
    Venue = $BedEvent.venue.name
    Country = $BedEvent.venue.country
    BandPage = $BedEvent.artists.facebook_page_url
}
```

This should provide me with enough information to create a basic calendar entry.

Here is the full code to obtain an event and output the information to create a calendar entry in the community calendar.

```powershell
$events = Invoke-RestMethod  "http://api.bandsintown.com/artists/LiveInjection/events.json?api_version=2.0&app_id=Bedlington"

$events | where {$_.venue.city -match "Bedlington"}

$properties = [ordered]@{
    Title = $BedEvent.title
    Artist = $BedEvent.artists.name
    Date = [datetime]$BedEvent.datetime
    Town = $BedEvent.venue.city
    Venue = $BedEvent.venue.name
    Country = $BedEvent.venue.country
    BandPage = $BedEvent.artists.facebook_page_url
}

$outputobj = New-Object -TypeName psobject -Property $properties
Write-Output $outputobj
```

This provides the following output

```powershell
Title    : Live Injection @ Market Tavern in Bedlington, United Kingdom
Artist   : Live Injection
Date     : 17/03/2017 19:00:11
Town     : Bedlington
Venue    : Market Tavern
Country  : United Kingdom
Bandpage : https://facebook.com/LiveInjectionOfficial
```

I haven't tackled how to get this into the calendar yet, this may have to be via direct database access but I'll try and cover that in another post when I get around to it and probably turn this into a function.

The other drawback of this method is that the Bands in Town API only provides artist search rather than geographical so I would need to know a list of artists to search for gigs which isn't the ideal scenario but if I were to obtain a list of local artists who were utilising Bands in Town then I could likely iterate through those artists with a _foreach_ loop and grab all local events for the calendar.