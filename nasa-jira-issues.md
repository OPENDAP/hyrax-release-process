# NASA Jira - Reviewing release tickets and making a JIRA release for Hyrax.

Our NASA efforts are primarily documented in the NASA JIRA system. For a Hyrax release we will use JIRA to identify the closed tickets since the last Hyrax release that are directly related to Hyrax and not some other OPeNDAP/NASA activity.

On our NASA Confluence (Atlassian) Wiki For Hyrax we maintain a set [Hyrax PROD deployment release notes](https://wiki.earthdata.nasa.gov/spaces/HYRAX/pages/290062458/Hyrax+PROD+deployment+release+notes). These each represents a triage and distilment of our NAS JIRA issues for the that deployment. The release engineer can open all the related relase notes up, scrape their content into a faile, and ask an LLM to summerize the things.
That summery will be used in our Hyrax release page at www.opendap.org 



# Appendix
## Issues Discovery- The Hard Way.
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

