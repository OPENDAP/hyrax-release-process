This task is to ensure that the *hyrax-dependencies* project is up to
date and tar balls on www.o.o. are current.

### Update the Version Numbers

If the review of the ChangeLog indicates that there have been changes
since the last release, increment the version number in the Makefile.

Make sure any change in version number is also reflected in the NEWS
file.

## [Common Release Tasks](common_release_tasks.md)
Perform the human driven [Common Release Tasks](common_release_tasks.md)
and then come right back here.

## Commit And Push
1.  Save, commit, and push the changes to master branch.
2.  Once the *hyrax-dependencies* CI build is finished
    1.  Trigger a CI build *libdap4* by pushing a small change to the
        *libdap4* master branch. When that CI build has completed
        successfully,
    2.  Trigger a CI build in the *bes* by pushing a small change to the
        *bes* master branch.
3.  Wait for the successful completion.

    If there's a problem with the CI builds at this point you may wish
    to follow the advice of ***Herman Wouk***: *"When in danger or in
    doubt, run in circles, scream and shout"*
<!--
## Publish and Sign
All you need do is build the tar file using `make dist`, sign it, and
push (or pull) these files onto www.opendap.org/pub/source.

1.  Go to the **hyrax-dependencies** project on your local machine and
    run `make dist` which will make a hyrax-dependencies-x.y.tar.gz file
    in the directory above the top level of the **hyrax-dependencies**
    project.
2.  Use **gpg** to sign the tar bundle:

    `gpg --detach-sign --local-user security@opendap.org ../hyrax-dependencies-x.y.tar`
3.  Use **sftp** to push the signature file and the tar bundle to the
    /httpdocs/pub/source directory on www.opendap.org

    *(Assuming your current working directory is the top of the
    **hyrax-dependencies** project)*
    ```
    sftp opendap@www.opendap.org
    cd httpdocs/pub/source
    put hyrax-dependencies-x.y.tar.sig
    put hyrax-dependencies-x.y.tar
    quit
    ```
4.  Check your work!
    1.  Download the source tar bundle and signature from
        www.opendap.org.
    2.  Verify the signature:
     ```
     gpg --verify hyrax-dependencies-x.y.tar.sig hyrax-dependencies-x.y.tar
     ```

-->

## Tag The Release
1.  Tag, and push the tag.
    - *git tag -m "version-<number>" -a <numbers>*
    - *git push origin <numbers>*

## Make The Release On GitHub
1.  Goto the [GitHub 'tags' page for
    *hyrax-dependencies*](https://github.com/OPENDAP/hyrax-dependencies/tags).
2.  Click the "Create release from tag" button
3.  Enter a title for the release (looks at previous releases for
    examples)
4.  Copy the most recent text from the NEWS file into the describe field
5.  Click Save/Update this release.

## hyrax-dependencies Release Assets 
Internal: 
* Our CICD produces a tar file of the installed includes, executables, and libraries of the hyrax-dependencies. The tar file is placed in S3 for use in building the `bes_core` docker images.

External:
* Source bundle tied to the GitHub release page.
