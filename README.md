# trial

testing commit changes for the readme
making commit changes
2020 updates
alternative pr method

## Notification...

## Disable appropriate screens

Screens team has an [internal tool](https://screens.mbtace.com/admin#/) to change the configuration of various screens. If subway data is down, some screens should be disabled using the tool. [Full tool instructions](https://docs.google.com/document/d/1rFPyd3vxRbpuuRRAJ1iwTgEVhj23TJtzHP_JqYofJLA/edit?usp=sharing#heading=h.ufspnh7giid0) include screenshots for each step.

#### Access the tool
* [screens.mbtace.com/admin#](https://screens.mbtace.com/admin#/) - you may have to log in via ActiveDirectory.

#### Select the appropriate screens
Use the column filter **"App ID"** to find the correct signs and use the 'select all' checkbox in the top left to select them (you may repeat this for multiple App ID types & it will retain your selections). As of October 2020, the tool includes Solari and E-Ink signs that use subway data:
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
Post the following on Slack at #screens-dev: "Solari & E-Ink screens have been disabled for a data issue"

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
