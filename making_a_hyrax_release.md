# Making A Hyrax Release
## Overview
Hyrax is made of several software components. Some of these are OPeNDAP software others, like the hyrax-dependencies are code that Hyrax depends on that must be compiled by our process, and not brought in through a package manager. This is, in part, because some of our dependency libraries are customized for our work.


### Release Products
Our release products are:
* **_libdap_** bundled as RPM files.
* **_hyrax_** The Hyrax data server bundled as a docker image.
* **_hyrax:ngap_** The Hyrax NGAP service bundled as docker image.


### Hyrax Components
Hyrax is built from several component projects:
* hyrax-dependencies 
* libdap4
* bes
* olfs
* hyrax-regression-tests
* hyrax-docker

When making a release every component should:
* Update the component's Travis Build Offset and base version numbers.
* A GitHub release should be made for the component, and the associated DOI badges for each component collected from Zenodo for use in the release documentation.
* 