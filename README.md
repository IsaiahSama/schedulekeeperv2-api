# Schedule Keeper v2 API

## What is ScheduleKeeper?

ScheduleKeeperv2 is a webapp developed using Flask, HTML and CSS, for the purpose of managing schedules and events. It exists to be an upgrade of the original project I created which can be found [here](https://github.com/IsaiahSama/schedulekeeper "ScheduleKeeperv1").

## What does the API Offer
ScheduleKeeperv2's API allows developers to manipulate their schedules, and expand on functionality using their own means. 
An example of this is receiving their schedule from the API, then creating a script to send them a notification when the time for the event has come.

## Using the API
The base url for the api is:
```
https://schedulekeeperv2.herokuapp.com/api/v1/
```

To use the API, you must first create an account here: https://schedulekeeperv2.herokuapp.com/

When interacting with any of the provided routes, a JSON dictionary is returned unless EXPLICITLY stated otherwise. It is also expected that any interactions from you to the server, will also be a valid JSON dictionary.

The below routes will only accept POST requests.

To use these services, a JSON dictionary MUST include:
```json
{
    "SCHEDULEKEEPER": {
        "HEADER": {
            "EMAIL": "your_registered_email@example.com",
            "PASSWORD": "your_registered_password"
        }
    }
}
```

When accessing the below routes, `SCHEDULEKEEPER` is the outermost Key.

### Creating A Schedule
To create a schedule, the url to be used is:
```cmd
https://schedulekeeperv2.herokuap.com/api/v1/schedules/create/
```

Note: An example of `%Y-%m-%dT%H%M` is `'2021-05-30T14:40'`

Your JSON should follow the below structure:
```JSON
{
    "SCHEDULEKEEPER": {
        "HEADER": {
            "EMAIL": "your_registered_email@example.com",
            "PASSWORD": "your_registered_password"
        },
        "DATA": {
            "EVENT_NAME": "name-of-event",
            "EVENT_DATE": "A date in valid ISO Format matching: %Y-%m-%dT%H%M",
            "EVENT": "Description of the event",
            "STATE": "Check Below"
        }
    }
}
```
STATE may be any ONE of the following 5 options: UPCOMING, PASSED, ONGOING, PENDING, CANCELED.

A good response will be in the form of:
```JSON
{
    "SCHEDULEKEEPER": {
        "RESPONSE": {
            "MODE": "CREATE",
            "DATA": {
                "CREATED_EVENT_NAME": "Name of Created Event",
                "CREATED_EVENT_ID": "ID of Created Event"
            },
            "EXTRA": "A Success Message",
        }
    }
}
```

If an error occurred, the response will be in the form of:
```json
{
    "SCHEDULEKEEPER": {
        "RESPONSE":{
            "MODE": "CREATE",
            "ERROR": "Error",
            "EXTRA": "Extra"
        }
    }
}
```
Where ERROR is the Error that occurred, and EXTRA is any extra information.

### Getting schedules
To get x number of your schedules, the following url will be used:
```
https://schedulekeeperv2.herokuap.com/api/v1/schedules/get/
```
Your JSON should follow the below structure

```JSON
{
    "SCHEDULEKEEPER": {
        "HEADER": {
            "EMAIL": "your_registered_email@example.com",
            "PASSWORD": "your_registered_password"
        },
        "METADATA":{
            "AMOUNT": 0,
            "RANDOM": "TRUE/FALSE"
        },
        "DATA": {
            "EVENT_IDS_OR_NAMES": [],
            "CAN_RANDOM": "TRUE/FALSE"
        }
    }
}
```
Where EVENT_IDS_OR_NAMES is a list containing integers representing EVENT_IDs or strings representing EVENT_NAMEs or both.

Note: EVENT_IDS_OR_NAMES, are only required if you want to specify which entires you want.

A good response will return the specified amount of schedules. If AMOUNT is 0, then all schedules are returned.

When sending your JSON, if RANDOM is True, then the other keys in DATA will not be checked, and x random events of yours will be returned.

If RANDOM is false, then selection will first check to see if EVENT_IDS either exists or is valid. If no schedules are found with that ID, or EVENT_IDS isn't in the dictionary, then EVENT_NAMES is checked. If that is also missing, or no matches were found with that name, then CAN_RANDOM is checked. If CAN_RANDOM exists, and is True, then a random entry will be returned. If not, but some of the data was valid, then along with the valid response, an extra entry: EXTRA, will be added, returning a list of IDS/NAMES for which events could not be found.

A good response will be in the form of:

```JSON
{
    "SCHEDULEKEEPER": {
        "RESPONSE": {
            "MODE": "GET",
            "DATA": [
                {
                    "ID": "The ID of the event",
                    "EVENT_NAME": "name-of-event",
                    "EVENT_DATE": "date-of-event in ISO format matching %Y-%m-%dT%H%M",
                    "OWNER": "The email of the owner",
                    "EVENT": "Details of the event",
                    "STATE": "The STATE of the event. Refer above for the possible states",
                },
            ],
            "EXTRA": "A Success Message",
            "UNFOUND": ["list", "of", "unfound", "ids", "or", "names"]
        }
    }
}
```
Where DATA will be a list of dictionaries, representing your Schedules. If the given number is more than the amount of schedules you have created, then all of your schedules will be returned, and nothing else, and UNFOUND is a list of unfound IDS or NAMES.

If an error occurred, the response will be in the form of:
```json
{
    "SCHEDULEKEEPER": {
        "RESPONSE":{
            "MODE": "GET",
            "ERROR": "Error",
            "EXTRA": "Extra"
        }
    }
}
```
Where ERROR is the Error that occurred, and EXTRA is any extra information.

### Updating a schedule
To update a schedule, you would want to use the below url:
```
https://schedulekeeperv2.herokuapp.com/api/v1/schedules/update
```
This route allows you to update a singular schedule.

To update, the expected JSON is:
```JSON
{
    "SCHEDULEKEEPER": {
        "HEADER": {
            "EMAIL": "your_registered_email@example.com",
            "PASSWORD": "your_registered_password"
        },
        "METADATA":{
            "EVENT_ID": "Id of Event",
        },
        "DATA": {
            "EVENT_NAME": "new-name-of-event",
            "EVENT_DATE": "new-date-of-event",
            "EVENT": "New Description Of Event",
            "STATE": "New State Of Event"
        }
    }
}
```

Where EVENT_DATE is the new date of the event in ISO Format, following the layout of %Y-%m-%dT%H%M, and STATE is one of the following 5 options: UPCOMING, PASSED, ONGOING, PENDING, CANCELED.

Note: All Keys for this DATA entry are optional. Therefore, to only change one attribute of a schedule, only that key needs to be specified.

A Successful response will be:
```json
{
    "SCHEDULEKEEPER":{
        "RESPONSE": {
            "MODE": "UPDATE",
            "DATA": {
                "EVENT_NAME": "name-of-event",
                "EVENT_DATE": "A date in valid ISO Format matching: %Y-%m-%dT%H%M",
                "EVENT": "Description of the event",
                "STATE": "State of the event"
            },
            "EXTRA": "A Success Message"
        }   
    }
}
```

However if something goes wrong, the resulting response will be:
```json
{
    "SCHEDULEKEEPER": {
        "RESPONSE": {
            "MODE": "UPDATE",
            "ERROR": "The Error",
            "EXTRA": "Extra information if any"
        }
    }
}
```

### Deleting a Schedule
A schedule can be deleted by sending your request to the url below:
```
https://schedulekeeperv2.herokuapp.com/api/v1/schedules/delete
```

This route allows you to delete x amount of schedules

Your JSON is expected to be formatted as follows:

```json
{
    "SCHEDULEKEEPER": {
        "HEADER": {
            "EMAIL": "your_registered_email@example.com",
            "PASSWORD": "your_registered_password"
        },
        "DATA": {
            "EVENT_IDS_OR_NAMES": []
        }
    }
}
```
Where EVENT_IDS_OR_NAMES is a list containing integers representing EVENT_IDs, or strings representing EVENT_NAMEs or both. Entering 0 in place of a list, will delete all schedules.

A successfull response will be formatted as follows:
```json
{
    "SCHEDULEKEEPER": {
        "RESPONSE":{
            "MODE": "DELETE",
            "DATA": {
                "DELETED": []
            },
            "EXTRA": "A Success Message",
        }
    }
}
```
Where DELETED will be a list of names of the items deleted

However, if something did go wrong, you will receive the below response

```json
{
    "SCHEDULEKEEPER": {
        "RESPONSE": {
            "MODE": "DELETE",
            "ERROR": "The Error",
            "EXTRA": "Extra information if any"
        }
    }
}
```

##
And that's all for now. I do hope that this API comes in handy in some way.