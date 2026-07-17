# hyrax_regression_tests Release Process


## Determine the version number

## [Common Release Tasks](common_release_tasks.md)
Perform the human driven [Common Release Tasks](common_release_tasks.md)
and then come right back here.

## Tag the code

The build process automatically tags builds of the master branch. The
Hyrax-version tag is a placeholder for us so we can sort out what code
goes with various Hyrax source releases.

As prt f a Hyrax Release, tag this point in the
    master/main branch with the Hyrax release number
    - `git tag -m "hyrax-`<number>`" -a hyrax-`<numbers>
    - `git push origin hyrax-`<numbers>

## Create the hyrax_regression_tests release on Github

1.  [Goto the hyrax_regression_tests project page in
    GitHub](https://github.com/OPENDAP/hyrax_regression_tests)
2.  Choose the **releases** tab.
3.  On the [Releases page](https://github.com/OPENDAP/bes/releases)
    click the 'Tags' tab.
4.  On the [Tags page](https://github.com/OPENDAP/bes/tags), locate the
    tag (created above) associated with this new release.
5.  Click the ellipses (...) located on the far right side of the
    *version-x.y.z* tag 'frame' for this release and choose *Create
    release*.
    - Enter a *title* for the release
    - Copy the most recent text from the NEWS file into the *describe*
      field
    - Click **Publish release** or **Save draft**.
      - If you have previously edited the release page you can click
        **Update this release**

