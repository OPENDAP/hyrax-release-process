## The Release Process

**Tip**: If, while working on the release, you find you need to make
changes to the code, and you know the CI build will fail, do so on a
*release branch* that you can merge and discard later. Do not make a
release branch unless you need to since it complicates making tags.

### Verify the code base

1.  We release using the *master*|*main* branch. The code on
*master*|*main* must pass the CI build.
2.  Make sure that the source code you're using for the following steps
    is up-to-date. (*git pull*)

### Update the Version Numbers
The Hyrax components each maintain their own version numbers. Each component 
has its own version change process/instructions. For the purposes of these
general instructions, do that part now if you have not already. The version
numbers are needed in the next bit.

### Update Release Files
Update the text documentation files for humans

#### What Issues Were Closed That Affect The Released Software 
It's helpful to have, in the **NEWS** file, the Website and the release
notes, a list of the tickets that have been closed since the last
release. There are two ticketing systems in play for our work, NASA's Jira and
out GitHub repository. 

##### NASA Jira Issues
For the Jira issues, goto the *[NASA Jira Issues](https://bugs.earthdata.nasa.gov/secure/RapidBoard.jspa?rapidView=901&projectKey=HYRAX&view=planning.nodetail&issueLimit=100)* page and
look at the *Tickets closed recently* item. From there, click on
*Advanced* and edit the time range so it matches the time range since
the past release to now, then *Export* that info as an excel spreadsheet
(the icon with a hat and a down arrow). YMMV regarding how easy this is
and Jira's UI changes often. 

##### GitHub Issues
To see the GitHub issues got to the Issues page for the repository and edit the 
search bar contents so that it reads: `is:issue state:closed closed:>2026-01-01`, 
substituting the date of the last release for `2026-01-01`.

#### Update the **ChangeLog** file.

Use the script [`gitlog-to-changelog`](./bin/gitlog-to-changelog) (which can be found with Google 
or there's a copy checked in to the bin directory of this repository.) 
To update the **ChangeLog** file by running it using the
`--since="`<date>`"` option with a date one day later in time than the
newest entry in the current ChangeLog.

**gitlog-to-changelog --since="1970-01-01"** (*Specify a date one day
later than the one at the top of ChangeLog*)

Save the result to a temp file and combine the two files:
: **cat tmp ChangeLog \> ChangeLog.tmp; mv ChangeLog.tmp ChangeLog** If
you're making the first ChangeLog entries, then you'll need to create
the ChangeLog file first.
**Tip**: *When you're making the commit log entries, use line breaks so
ChangeLog will be readable. That is, use lines \< 80 characters long.*

#### Update the NEWS file

To update the NEWS file, just read over the new ChangeLog entries and
summarize. 
- Look at the way the NEWS file is formatted and continue with same.
- Start a new section for this release at the top of the file.
- Add the version number and the date at the beginning of the section

