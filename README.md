# sharepoint-events-parser
A way to parse SharePoint Calendar Event RecurrenceData into individual event objects with JavaScript

### Why

Recurring events on a SharePoint calendar are not stored individually; instead, the parent event contains the recurrence information stored as XML. The only other way I've seen to get recurrence data from a calendar list on the client side is to use the Lists.asmx web service. This is not necessarily a bad way to go, but working with the CAML for the query and the XML returned from that web service can be burdensome.

This library uses pure JavaScript to parse out the RecurrenceData information on events into an array of individual javascript objects representing each instance of the recurring event. You can query calendar lists with a REST query, get back JSON data, and choose to parse it or not to get the recurring events.

This has bee tested with SharePoint 2013 On-premises.

### Usage

Since this is a client-side library, it works with client-side data. I'm using SharePoint's RESTful web services to retrieve calendar data. In order for the parser to parse Recurring Events, you must specify in your REST query that you wish to retrieve the RecurrenceData and Duration properties (by default, these are not returned)

#### REST Query
```
/_api/web/lists/getbytitle('Calendar')/items?$select=*,Duration,RecurrenceData
```

The events parser currently assumes that you are using a standard SharePoint Calendar list, so it uses the EventDate and EndDate properties for parsing events. In the future, I may make this configurable, but for now, if your data does not contain those properties, you can add them to your event object manually prior to calling the parser

There are two basic ways to call the parser - on a single event, or on an array of events. In both cases, the parser will return an array of parsed events (including any recurring events)

#### Parse Single Event

```
var parsedArray = spEventsParser.parseEvent(eventObject);
```

#### Parse Array of Events

```
var parsedArray = spEventsParser.parseEvents(eventArray);
```

#### Optionally, specify a date range for the recurring events

A recurring event may have been created several years ago, but maybe you only care to return data within the past year. Likewise, if an event recurs indefinitely, you may only care to return the recurrence up to one year in the future. You can specify these boundaries by passing a start and end date into the events parser:

```
var dt = new Date();
var startDate = new Date(dt.getFullYear(), dt.getMonth() - 1, 25); //start from the 25th of last month
var endDate = new Date(dt.getFullYear(), dt.getMonth() + 1, 5); //end at the fifth of next month
 
var parsedArray = spEventsParser.parseEvent(eventObject,startDate,endDate);
```