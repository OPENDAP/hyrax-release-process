# Hyrax (aka hyrax-docker) release process

This document describes how to make a release of the official Hyrax Docker image

The Hyrax Docker images are the primary release asset for Hyrax. These images are automatically generated, versioned, and tagged by our TravisCI machine. 

The specific contents of the Hyrax docker image is dictated by the `build-recipe`. When we make a release we want to copy the build-recipe(s) used to build the release inmage into the releases directory, renaming them with the Hyrax version (ex: `hyrax-1.18.0`)

In order to make an offical numbered release of Hyrax we need to:
## Update the Hyrax Version numbers.
  * Edit the file `hyrax-docker/travis/version_and_build_offsets.sh` and update the `HYRAX_RELEASE_VERSION` and `TRAVIS_HYRAX_BUILD_OFFSET` values. 
  * Set the `HYRAX_RELEASE_VERSION` to the new version of Hyrax that is being released (ex: `1.18.0`)
  * Set the `TRAVIS_HYRAX_BUILD_OFFSET` value to the number of the last TravisCI build plus one. The previous commit and push will have triggered a TravisCI build. Find the build number for the previous commit in the TravisCI page for `hyrax-docker` and use that Travis build number plus 1.

## Perform the [Common Release Tasks](common_release_tasks.md)

## Copy the build recipe(s) to the releases' directory.
  * Name the files `hyrax-<numbers>-build-recipe`

## Update The DockerHub Landing Pages
In DockerHub each of our published Docker images has a landing page. The landing pages for the hyrax and the hyrax_ncwms images contain explanation and a list of links to official release version. These need to be updated. 

To update:
  1.  Log into DockerHub
  2.  Navigate to <https://hub.docker.com/repository/docker/opendap/hyrax>
      1.  Add the new release to the list of Release and Docker files.
      2.  Update the link to the ''Latest' Docker file.
  3.  Navigate to
    <https://hub.docker.com/repository/docker/opendap/hyrax_ncwms>
      1.  Add the new release to the list of Release and Docker files.
      2.  Update the link to the ''Latest' Docker file.

## Commit! 
* Commit the changes locally.
* Push the commited changes to DockerHub

## Tag the Repository.
  1.  Make sure all changes have been pushed and merged to the master
    branch.
  2.  Since the Docker project is essentially linked to a Hyrax Release,
    tag this point in the master branch with the Hyrax release number
```
    git tag -a hyrax-`<numbers>` -m "Hyrax `<number>`"`
    git push origin hyrax-`<numbers>
```

## Create a GitHub release
  * Goto the 'tags' page ('code' then 'tags' at the top of the directory window) and click the 'Tags' tab. There, click the ellipses (...) on the right of the 'version-\*' tag and:
    1.  Enter a *title* for the release
    2.  Copy the most recent text from the NEWS file into the *describe*
  field
    3.  Click *Update this release* or *Save draft*
  * This will trigger an 'archive and DOI' process on the Zenodo system.

### Get the DOI from [Zenodo](https://zenodo.org)

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
    that much).

Images for the above steps to help with the web UI: coming soon



