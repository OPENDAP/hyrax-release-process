# libdap release process
Here are the steps a developer should follow to release the libdap software library 
used by the Hyrax Data Server.
There is a separate page for making a release of the Hyrax server with 
an overview page that describes how the website is updated and lists are
notified.

We now depend on the CI/CD process to build binary packages and to test
the source builds. When the source code is tagged and marked as a
release in GitHub, our linked Zenodo account archives that software and
mints a DOI for it.

> **Tip**: If, while working on the release, you find you need to make
changes to the code, and you know the CI build will fail, do so on a
*release branch* that you can merge and discard later. Do not make a
release branch unless you need to since it complicates making tags.

### 1) Update your local libdap4 code base
Before making any changes, make your local branch is up to date.

* Change to the _master_ branch. 
* Do a `git pull`
* Create a branch: `git checkout -b new_release`.

### 2) Update the Version Numbers

There are really 2 version numbers for each of these project items. The
*human* version (like version-3.17.5) and the *library* API/ABI version
which is represented as `CURRENT:REVISION:AGE`. There are special rules
for when each of the numbers in the library API/ABI version get
incremented that are triggered by the kinds of changes that where made
to the code base. The human version number is more arbitrary. So for
example, we might make a major API/ABI change and have to change to a
new Libtool version like `25:0:0` but the human version might only
change from bes-3.17.3 to bes-3.18.0

#### 2.1) Version for Humans

1.  Determine the human version number. This appears to be a somewhat
    subjective process.
2.  Edit each of the *Affected Files* and update the human version
    number.

##### Affected Files:

* *configure.ac* - Look for:
```
AC_INIT(libdap, ###.###.###, opendap-tech@opendap.org)
```
* *README.md*
* *INSTALL*
* *ChangeLog*
* *NEWS*
* *CMakeLists.txt* - Look for:
```
set(LIBDAP_VERSION "#.##.#")
```

#### 2.2) API/ABI Version

The library API/ABI version is represented as CURRENT:REVISION:AGE.

The rules for shared image version numbers:

* No interfaces changed, only implementations (good): Increment
REVISION.

* Interfaces added, none removed (good): Increment CURRENT, set
REVISION to 0, increment AGE.

* Interfaces removed or changed (BAD, breaks upward compatibility):
Increment CURRENT, set REVISION to 0 , and set AGE to 0.

See the *Appendix: How to see the scope of API/ABI changes in C++
sources* below for gruesome details. Often basic knowledge of the edits
is good enough.

##### Affected Files
*configure.ac* - Look for
``` 
DAPLIB_CURRENT=###

DAPLIB_REVISION=###

DAPLIB_AGE=###

DAPLIB_REVISION=###

DAPLIB_AGE=###
```

### 3) [Common Release Tasks](common_release_tasks.md)
Perform the [Common Release Tasks](common_release_tasks.md).

### 4) Trigger CI/CD builds to complete. 
Commit and push. You must be working on the *PR* branch. Check the status
of the build for any red flag. If Travis CI/CD build successfully, then
the PR is ready to be merged.

### 5) Tag The Release

You may tag now (before merging the PR). It requires the right permissions
to do so.

1.  `git tag -m "hyrax-<number>" -a hyrax-<numbers>` 
2.  `git push origin hyrax-<numbers>`

### 6) Create the release on GitHub

Goto the 'tags' page ('code' then 'tags' at the top of the directory
window) and click the 'Tags' tab. There, click the ellipses (...) on the
right of the 'version-\*' tag and:

1.  Enter a *title* for the release. For example: `libdap-x.yy.x for Hyrax-1.xx.y`
2.  Copy the most recent text from the NEWS file into the *describe*
    field
3.  Click *Update this release* or *Save draft*

This will trigger an 'archive and DOI' process on the Zenodo system.

### 7) Publish and Sign

#### 7.1) Get The Files.
Go to our S3 bucket, opendap.travis.build, and retrieve the libdap assets
for the release. 

First, search the bucket by entering libdap-_version_ (say _libdap-3.22.0_)into the search bar. That search should locate the 2 regular RPM files (one for RHEL8 and another for RHEL9) and the source code tar ball. 

Next search for libdap-devel-_version_ (say _libdap-devel-3.22.0_)to get the RHEL8 and RHEL9 developers packages.

You should find 5 files! If you don't it's time to look at the CI/CD logs for this build.
#### 7.2) Sign The Files
Once you have these files you sign them by doing the following:
```bash
for i in *.rpm *.gz; do gpg --detach-sign --local-user security@opendap.org "$i"; done
```
#### 7.3) Upload
Beginning in your local directory that contains the various release assets and their signature files, login to the webhost. You will need to have credentials
to access FlyWheels site that hosts the OPeNDAP website.

```bash
sftp -P 22 your-user-id@sftp.flywheelsites.com
```
Copy the RHEL8 RPMs and signatures like this:
```bash
cd org-opendap/opendap/pub/binaries/hyrax-VERSION/rocky-8
put *el8*
```
Copy the RHEL9 RPMs and signatures like this:
```bash
cd org-opendap/opendap/pub/binaries/hyrax-VERSION/rocky-9
put *el9*
```
Check your work!
1.  Download the source tar bundle and signature from
        www.opendap.org/pub/source.
2.  Verify the signature:
```
gpg --verify libdap-x.y.z.tgz.sig libdap-x.y.z.tgz
```

### 8) Get the DOI from [Zenodo](https://zenodo.org)

The DOI from Zenodo is automatically updated since it uses `latest`.

<!-- 
1.  Goto [Zenodo](https://zenodo.org) and look at the 'upload' page.
    Since the libdap, BES and OLFS repositories are linked to Zenodo,
    the newly-tagged code is uploaded to Zenodo automatically and a DOI
    is minted for us.
2.  Click on the new version, then click on the DOI tag in the pane on
    the right of the page for the given release.
3.  Copy the DOI as markdown from the window that pops up and paste that
    into the info for the version back in GitHub land.
4.  Also paste that into the README file. Commit using *\[skip ci\]* so
    we don't do a huge build (or do the build, it really doesn't matter
    that much). -->

Images for the above steps to help with the web UI: coming soon

### 9) Update the online reference documentation

1.  *make gh-docs*

## Appendix: How to see the scope of API/ABI changes in C++ sources

Determine the new software version (assuming you don't already know the
extent of the changes that have been made)


For C++, build a file of the methods and their arguments using:

```
nm .libs/libdap.a \| c++filt \| grep ' T .\*::' \| sed 's@.\* T
$.*$@\1@' \> libdap_funcs
```
and compare that using `diff` on the previous release's library.

Assess the changes you find based on the following rules for the values
of `CURRENT`,`REVISION`, and `AGE`

- No interfaces changed, only implementations (good): ==\> Increment
  REVISION.
- Interfaces added, none removed (good): ==\> Increment CURRENT,
  increment AGE, set REVISION to 0.
- Interfaces removed or changed (BAD, breaks upward compatibility): ==\>
  Increment CURRENT, set AGE and REVISION to 0.

The current value of `CURRENT`,`REVISION`, and `AGE` can be found in
`configure.ac`:

``` bash
LIB_DIS_CURRENT=14
LIB_DIS_AGE=6
LIB_DIS_REVISION=1
```

Once you have determined the new values of the `CURRENT:REVISION:AGE`
strings then:

Edit the configure.ac and update the version values to the new ones.

## libdap Release Assets 
Internal: 
* RPM files. The CICD will automatically build the libdap RPM files. when the release file changes are pushed/merged to master/main. These assets will then be utilized by hyrax-docker to build the public released docker images.

External:
* Source bundle tied to the GitHub release page.
* The release RPM files built by CICD.

