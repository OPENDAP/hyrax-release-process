# OLFS Release Process

This document describes how to make releases of the OLFS.

About version numbers:
- We use \<major>.\<minor>.\<revision\> version numbers for the olfs code
  version.

New versions of a release (only the \<revision\> field in the version
number is changed) should contain:

- Bug fixes.
- Updated tests.
- Changes to production rules.

## Make sure the code is ready to release

Make sure that all the developers working on the code have checked in
their working copies of the trunk, and that all development branches
whose functionality/features/codeBase are included in the release
have been merged to the trunk and committed.

### Check out the master branch of the OLFS code from GitHub

In a new directory get a fresh copy of the trunk:

``` bash
git clone https://github.com/opendap/olfs
git checkout master
git pull
```

<!--
### Build the OLFS

In the checked out OLFS directory run the command:
``` bash
% ant server
```

### Run the tests

*Updated Dec 29th 2020*

The Hyrax regression tests utilize an autotools/automake/autotest test
system. In order to run the tests you must have the Hyrax server
running. Build and install the BES first. Then build and install the
OLFS into a Tomcat instance and start that instance.

Once you have the OLFS built, installed, and running do the following:

- If you have not done so already, clone the hyrax_regression_tests
  project:


`gitclone `[`https://github.com/opendap/hyrax_regression_tests`](https://github.com/opendap/hyrax_regression_tests)

- Change directory into the top of `hyrax_regression_tests` and perform
  the following:


`autoreconf -vif`

`./configure`

`make check`

The tests may also be run directly:


`./testsuite`

Either command will run the tests in a traditional autotest test
harness, against the server you should have running at
http://localhost:8080/opendap/

The `testsuite` supports several notable command line options:

- `--besdev=yes|no` Was the BES built using --enable-developer?
- `--hyraxurl=hyrax-service-endpoint-url` Run the various tests (DAP2/4,
  w10n, wcs, etc.) against the Hyrax instance located at the specified
  endpoint URL. (default: <http://localhost:8080/opendap>)
- `--netrc=netrc_file_name` Run tests using the specified netrc file
  (ala cURL) so that the test can authenticate to access the server.
  (default: ~/.netrc)
-->

## Determine OLFS Version Numbers
**_Every version has a number!_**

1.  Look in the NEWS and README.md files to determine the previous
    version number.
2.  Determine new version number by looking at the changes to the code
    (start by reviewing the ChangeLog file)
    - Versioning rules: What the *MAJOR.MINOR.REVISION* string means:''
      - No interfaces changed, only implementations (good): ==\>
        Increment REVISION.
      - Interfaces added, none removed (good): ==\> Increment MINOR, set
        REVISION to 0.
      - Interfaces removed or changed (BAD, breaks upward
        compatibility): ==\> Increment MAJOR, set MINOR and REVISION to 0.
3.  Remember the new version number so that it can be used to:
    - Update the release related files
    - Build the versioned software distribution bundles.


## [Common Release Tasks](common_release_tasks.md)
Perform the human driven [Common Release Tasks](common_release_tasks.md)
and then come right back here.

<!--

README.md
Update version number for the OLFS and HYRAX

Update the version number and DOI markup for the BES.

Update the version number and DOI markup for libdap.

Review content to make sure it's current.


NEWS
Add new version number and,

Summarize the new additions to the change log. There may be many
checkins associated with new feature development. You can summarize
those as "Added 'X' feature."


install.html
Update version number.

Review content to make sure it's current.
-->


## Update the Build Offset

*Setting the build offset correctly will set the build number for the
new release to "0".*

The file `travis/versions_and_build_offsets.sh` is used by our TravisCI
production to set the values of the:

- `OLFS_VERSION`
- `HYRAX_VERSION`
- `TRAVIS_OLFS_BUILD_OFFSET`
- `TRAVIS_HYRAX_BUILD_OFFSET`

Set the `OLFS_VERSION` and the `HYRAX_VERSION` to the values that you
used in the NEWS, README, and other files.

Set the values of `TRAVIS_OLFS_BUILD_OFFSET` and
`TRAVIS_HYRAX_BUILD_OFFSET` to the number of the last OLFS TravisCI
build plus one. The previous commit and push will have triggered a
TravisCI build. Find the build number for the previous commit in [the
TravisCI page for the
OLFS](https://app.travis-ci.com/github/OPENDAP/olfs) and use that build
number plus 1.

## Check in and push the changes

In the local copy of the OLFS master branch in which you updated the
**ChangeLog** and other files:


``` bash
git commit -a -m "olfs: Updating olfs version and release files to version 1.2.3"
git push
```


Once this is done, use GitHub to tag the code you just committed both
with its version and the version of hyrax to which it belongs:

#### Tag the release
``` bash
git tag -m "olfs-<numbers>" -a <numbers>
git push origin <numbers>
```


### Make The Release

- Make the release in GitHub.
  - Look at previous releases for title format.
  - Use the NEWS entry for the description.
- Get The DOI for the release from Zendodo.
  - Use the search api to find `olfs xx.yy.zz`
  - Copy the markup for the DOI badge and then
- Update the Release Page in GitHub with the DOI markup.

<!--

## Build and Sign the versioned distribution bundles

In an up-to-date local copy of the OLFS software:

``` bash
ant -DOLFS_VERSION=1.2.3 -DHYRAX_VERSION=4.5.0 DISTRO
```


This will create the three compressed tar files for the release in the
\$olfs/build/dist directory.

olfs-1.2.3-src.tgz: Contains the source files for the release
olfs-1.2.3-webapp.tgz: Contains the instructions and .war file that will be used in the tomcat webapps directory.
robots-olfs-1.2.3-webapp.tgz: Contains just the robots webapp that can supply site inventory to bots.

### Use **gpg** to sign the tar bundle

\#: One File:
`gpg --detach-sign --local-user security@opendap.org olfs-####.tgz`

\#: All Files:
`for i in build/dist/*.tgz; do gpg --detach-sign --local-user security@opendap.org $i; done`

1.  Use **sftp** to push the signature file and the tar bundle to the
    /httpdocs/pub/source directory on www.opendap.org

    *(Assuming your current working directory is the top of the **bes**
    project)*

    `sftp opendap@www.opendap.org`

    `cd httpdocs/pub/source`

    `put olfs-####.tgz.sig`

    `put olfs-####.tgz`

    `quit`
2.  Check your work!
    1.  Download the source tar bundle and signature from
        www.opendap.org.
    2.  Verify the signature:

        `gpg --verify olfs-####.tgz.sig olfs-####.tgz`

Use [GPG to detach sign](SecureEmail#Encrypting_files "wikilink") each
of these three files, and you're ready to upload them to the website for
distribution.
-->

## OLFS Release Assets 
Internal: The CICD will automatically build the OLFS WAR files when the release file changes are pushed/merged to master/main. These assets will then be utilized by hyrax-docker to build the public released docker images.

External:
Source bundle tied to the GitHub release page.

> The OLFS has historically been published here:
<http://www.opendap.org/pub/olfs/> but maybe there's no point with that then?