# trial

testing commit changes for the readme
making commit changes
2020 updates
alternative pr method

## Notification...

## Disable appropriate screens

Screens team has an [internal tool](https://screens.mbtace.com/admin#/) to change the configuration of various screens. If subway data is down, some screens should be disabled using the tool. 
[Full tool instructions](https://docs.google.com/document/d/1rFPyd3vxRbpuuRRAJ1iwTgEVhj23TJtzHP_JqYofJLA/edit?usp=sharing#heading=h.ufspnh7giid0) include screenshots for each step.

#### Access the tool
* [screens.mbtace.com/admin#](https://screens.mbtace.com/admin#/) (you may have to log in via ActiveDirectory)

#### Select the appropriate screens
Use the column filter **App ID** to find the correct signs and use the `select all` checkbox in the top left to select them (you may repeat this for multiple App ID types & the tool will retain your selections). As of October 2020, the tool includes three apps that use subway data:
* gl_eink_single
* gl_eink_double
* solari

#### Disable the signs
Click the **Edit Selected Rows** button at the bottom of the table and then set the **Disabled** dropdown to `True`.

#### Apply, validate, and confirm the changes
* Click **Apply changes** to close the dialog box
* Click **Validate** to test the change
* Click **Confirm** to set the change

#### Confirm that the signs are off
The tool will provide a confirmation message, but you can also check in Chrome to see a static image that says **Live updates are temporarily unavailable**. It may take a minute to populate (especially for E-Ink):
* [GL E-Ink single](https://screens.mbta.com/screen/gl_eink_single)
* [GL E-Ink double](https://screens.mbta.com/screen/gl_eink_double)
* [Solari](https://screens.mbta.com/screen/solari?scroll=true)

#### Notify Screens team
Post the following on Slack at [#screens-dev](https://mbta.slack.com/archives/CSZEKL4G4): "Solari & E-Ink screens have been disabled for a subway data issue"

## Identify the cause...

## Close the loop

Once the issue has been resolved:

#### Re-enable disabled signs
Repeat the steps above, but set the **Disabled** dropdown for affected signs to `False`. Apply, validate, and confirm that the signs are back online and showing predictions:
* [GL E-Ink single](https://screens.mbta.com/screen/gl_eink_single)
* [GL E-Ink double](https://screens.mbta.com/screen/gl_eink_double)
* [Solari](https://screens.mbta.com/screen/solari?scroll=true)

#### Close the loop
...
2. Follow up on Slack in [#ctd-incident-response](https://mbta.slack.com/archives/C01B0577NH4), [#realtime-data](https://mbta.slack.com/archives/CF6RECCP7), and [#screens-dev](https://mbta.slack.com/archives/CSZEKL4G4).

[#ctd](https://mbta.slack.com/archives/G7GA11JJW)

----

# Bus Data outage

**Circumstances:** Bus data (for all buses or a large number of buses) is not available to customers, but other modes are unaffected

## Symptoms

* You receive a page or an e-mail from the API checker about `bus-vehicles` or `bus-predictions`
* You receive a page about a Busloc error
* Someone from the OCC reports to you that customers are complaining that bus predictions are not available
* Bus inspectors are reporting issues seeing bus locations in Skate

## Verify the issue

Check the [API Checker Dashboard][api-checker-dashboard] - if this data is down, you'd expect to see failed checks related to it. If it appears to be just a "blip" and you don't see any more failed checks over the next fifteen minutes, you can mark it as resolved.

If the page was due to a Busloc error/warning, further investigation is needed even if the API Checker looks ok.

## Disable affected screens

If data is down, disable affected bus screens by logging into the [Screens tool](https://screens.mbta.com/admin#/devops) and checking the `Disabled?` box for **Bus**. You can confirm that signs have been disabled by checking the [E-Ink app](https://screens.mbta.com/screen/bus_eink) or [Solari app](https://screens.mbta.com/screen/solari?scroll=true).

## Notification

Notify stakeholders via the following methods:

* Email/Slack: [Click here to create draft email from template.][email-template] This will also notify the [#ctd-incident-response](https://mbta.slack.com/archives/C01B0577NH4) and [#ctd](https://mbta.slack.com/archives/G7GA11JJW) Slack channels.
* Separately, if the incident is categorized as SEV1 or SEV2 in PagerDuty, run the "Notify for Executive Escalation" [Response Play](/devops/pagerduty.md#running-a-response-play) to alert the administrative stakeholder on call, who will determine whether to notify executives about the incident.

For other communications, refer to the [contact spreadsheet][contacts] for contact info.

## Identify the cause

1. Check if data is getting from TransitMaster to Busloc:
    1. [This query](https://mbta.splunkcloud.com/en-US/app/search/search?q=search%20index%3Dbusloc-prod%20Vehicle%20v_source%3Dtransitmaster%20block!%3D%22%22%20block!%3D%229990*%22&display.page.search.mode=verbose&dispatch.sample_ratio=1&earliest=-60m%40m&latest=now&display.prefs.events.count=10&display.general.type=events&display.page.search.tab=events&sid=1577715184.7963509) searches Splunk for Busloc receiving location and block data from Transitmaster.
    1. If data is logged with TransitMaster timestamps within the last few minutes, move to the next step.
    1. If nothing is logged, it still may be a problem with Busloc.
    1. [This query](https://mbta.splunkcloud.com/en-US/app/search/search?q=search%20index%3Dbusloc-prod%20passengercount%20load%3E0&display.page.search.mode=verbose&dispatch.sample_ratio=1&workload_pool=standard_perf&earliest=-30h%40h&latest=now&sid=1596809642.6940677) searches Splunk for BusLoc receiving APC messages from TransitMaster by checking non-zero crowding values.
    1. If data is logged with timestamps within the last few minutes, move to the next step. If there is no data logged, contact Dan Salomon or Paul Swartz to determine if it's an issue with BusLoc or TransitMaster.
    1. If the [query of Busloc non-TransitMaster sources](https://mbta.splunkcloud.com/en-US/app/search/search?q=search%20index%3Dbusloc-prod%20Vehicle%20v_source!%3Dtransitmaster&display.page.search.mode=verbose&dispatch.sample_ratio=1&earliest=-60m%40m&latest=now&display.prefs.events.count=10&display.general.type=events&display.page.search.tab=events&sid=1577716085.7965215) has data, or the [TransitMaster API](http://172.29.21.80/TMRealTimeWebService/RealTimeMBTAService.asmx/getVehicleLocations?dataInclude=0) (accessible only from the MBTA network or VPN) is down or lacks blockId or runId for most vehicles, call Trapeze support at (877) 411-8727. During off hours this will go to voicemail, but someone should call back within half an hour.
        1. If the issue started in the early morning, it's likely a crash of the TMDataCube service: ask the Trapeze support engineer to restart TMDataCube and TMTransitDataWcfServiceHost.
        1. If the OCC reports being unable to assign garage roles, ask the Trapeze support engineer to restart the Work Assignment service.
    1. If there is no data in the second query, try restarting Busloc:
        1. Remote to hsrealtime01
        1. Click the Start menu, and type `services.msc`
        1. In the Services dialog, right-click `busloc-prod` and restart it.
        1. If that doesn't work, call Dan Salomon or Paul Swartz.
1. Check if BusLoc data is being published to the JSON feed where Swiftly pulls locations.
    1. Check the [BusLoc JSON feed](https://s3.amazonaws.com/mbta-busloc-s3/prod/VehiclePositions_enhanced.json) to make sure it has data with timestamps from the last few minutes. **You must be on the office network or VPN to access this feed.**
    1. If the feed is empty, restart Busloc (see above), or contact Dan Salomon or Paul Swartz.
2. If data is getting from TransitMaster to Busloc to Swiftly, then there may be a problem within Swiftly.
    1. The data received by Swiftly can be viewed at https://dashboard.goswift.ly. There is a developer@mbta.com login for the Swiftly Dashboard shared in LastPass. First, check the "Real-Time Stats" box in the top right. If there are no "Active Trips," the problem is likely Swiftly. If there are active trips, check specific routes by using the "Select Route(s)" menu in the top left. You can also choose "All Routes" from that menu. If there seems to be a specific group of routes with no data, there may be a problem with a particular garage. Routes for each garage are available on [Wikipedia](https://en.wikipedia.org/wiki/MBTA_Bus#Facilities)
    1. Even if the Swiftly Dashboard is down, Swiftly may still be processing data successfully. To check if the data coming out of Swiftly is complete, make an API call. We typically use stop_id 79 (Hynes Convention Center on the Route 1 and CT1) to check with this API call: https://api.goswift.ly/real-time/mbta-bus/predictions?number=3&stop=79 but you can use other stop_ids which are available in our API or GTFS. **You will need to set the production API key in the request header It cannot just be added in the URL.** This is in the notes field of the Swiftly account in LastPass. The Header is called "Authorization" and the value is the API key. Using [Postman](https://www.getpostman.com/) is likely the easiest way to send an API request with the right header. We do not have a shared account at this time, but this is a free service.
    1. You can check the [Concentrate logs](https://mbta.splunkcloud.com/en-US/app/search/search?q=search%20index%3D%22concentrate-prod%22%20goswift.ly%20%3Aunexpected_code&display.page.search.mode=fast&dispatch.sample_ratio=1&earliest=-15m&latest=now&sid=1553126361.1439168) to see if we're receiving unexpected error codes from them.
    1. If the issue seems to be with Swiftly based on the above:
        1. Check the #mbta-swiftly Slack channel to see if they are already aware of the issue. Contact them in the Slack channel first, tagging @stanparkford. If you do not receive a reply in a few minutes (or less depending on the severity of the problem) call the emergency line.
        1. If you are not getting a response in the Slack channel and the problem appears to be Swiftly, call their emergency line: (888)-777-7430‬. This will cycle through their contacts (starting with Stan Parkford). Inform them that bus predictions/locations appear to be down.
1. If Swiftly seems okay, the GTFS may have the wrong schedule for today
    1. Look at the [Busloc Vehicles](https://mbta.splunkcloud.com/en-US/app/search/search?q=search index%3Dbusloc-prod Vehicle trip%3D* age<600&display.page.search.mode=fast&dispatch.sample_ratio=1&earliest=-30m%40m&latest=now&sid=1594147278.2716828) in Splunk and grab one of the trip IDs.
    1. Check the service data in the API: https://api-v3.mbta.com/trips/TRIP_ID?include=service (replacing `TRIP_ID` with the ID you got from Splunk).
    1. Under "included", there should be a "service" entry.
        1. The current date should either be present in "added_dates" OR
        1. The current day of the week (Monday is 1, Sunday is 7) should be present in "valid_days" AND "start_date" is today or in the past AND "end_date" is today or in the future AND the current date is not present in "removed dates".
    1. If neither is the case, or the trip ID isn't found in the API, GTFS thinks that that the trip the bus is running is not active today. Contact someone from the TRC team for further debugging. They're available with the `@trc` alias in the `#realtime-data` Slack channel.
1. If the schedule looks good, then the problem must be in the v3 API or the GTFS. Call Paul Swartz and wake him up. He will solve all your problems.

## Send an email update

Once you have narrowed down the cause or taken an action, send a reply email/Slack post explaining what you have done.

## Close the loop

Once the issue has been resolved:

1. Re-enable affected screens with the [Screens tool](https://screens.mbta.com/admin#/devops) by unselecting the `Disabled?` checkbox for **Bus**. Confirm that signs are enabled with the [E-Ink app](https://screens.mbta.com/screen/bus_eink) and [Solari app](https://screens.mbta.com/screen/solari?scroll=true).
1. Send another follow-up email confirming that the issue is resolved
1. Resolve the incident in PagerDuty, and add a brief note with a description of the problem and resolution to the incident. The note should attempt to answer the following questions:
    1. What do we know now?
    1. What do we not know now?
    1. What do we think we know now?
    1. When do we think we will know?

