# NASA Jira - Reviewing release tickets and making a JIRA release for Hyrax.

Our NASA efforts are primarily documented in the NASA JIRA system. For a Hyrax release we will use JIRA to identify the closed tickets since the last Hyrax release that are directly related to Hyrax and not some other OPeNDAP/NASA activity.

## Issues Discovery
For the Jira issues, goto the *[NASA Jira Issues](https://bugs.earthdata.nasa.gov/secure/RapidBoard.jspa?rapidView=901&projectKey=HYRAX&view=planning.nodetail&issueLimit=100)* page and look at the *Tickets closed recently* item. 
From there, click on
*Advanced* and edit the time range so it matches the time range since
the past release to now, then *Export* that info as an Excel spreadsheet
(the icon with a hat and a down arrow). YMMV regarding how easy this is
and Jira's UI changes often. 
1. This JIRA filter: `project = HYRAX AND status = Completed AND resolutiondate  > "2025/01/21" ORDER BY KEY DESC` Can be modified using the previous release date to get the list of relevant issues to triage.
   * This JIRA filter: `project = HYRAX AND status = Completed AND resolutiondate  > "2025/01/21" ORDER BY KEY DESC` Can be modified using the previous release date to get the list of relevant issues to triage.

2. Categorize/triage each ticket in the list of closed tickets. If the ticket is one that relates directly to code that is part of Hyrax, make sure it is labeled "hyrax". If a ticket is obviously not related to Hyrax (such as Sprint planning, WSJF, PI planning etc.), make sure it does not carry a "hyrax" label. While doing this task make note of any "highlights" that deserve to called out in the release notes. Save the notable ticket URLs/descriptions so that they can be in the public release notes.
   * To see just the hyrax labeled tickets in the same time frame, the JIRA query would be: `project = HYRAX AND status = Completed AND labels = hyrax AND resolutiondate  > "2025/01/21" ORDER BY KEY DESC`

## Create JIRA Release Page

Once the categorization/triage is completed the labeled issues can be utilized to create a JIRA release to be associated with theis Hyrax release. 
 * From our [NASA JIRA Hyrax project page](https://bugs.earthdata.nasa.gov/projects/HYRAX) choose "Releases" from the left hand sidebar.
 * Create a new JIRA release (name, version, dates, etc.) and use a JIRA query to populate it with the relevant tickets. We can link to the NASA JIRA release page from our Hyrax release page with a note that the link is meant for NASA persons.
