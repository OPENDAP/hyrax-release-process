# Hyrax 1.18


## What's New For *hyrax-1.18.0*
### 1. Performance & Cloud Optimization

* **Direct S3 Access:** Replaced TEA redirects with presigned S3 URLs using TEA STS credentials. This was first applied to data requests and later expanded to initial DMR++ requests to dramatically lower response times.
* **Direct Chunk IO:** Introduced direct chunk IO capabilities for both whole variables and subset cases to optimize S3 data retrieval.
* **Caching Security:** Included the EDL user ID in the keys for data URL caches to prevent potential misattribution of data requests.
* **Authentication Speed:** Implemented local EDL (Earthdata Login) token validation and direct user ID extraction from tokens to reduce curl request and session connection times.
* **AWS Warm Pool:** Altered deployments to leverage an AWS Warm Pool for faster autoscaler response.

### 2. File Format & Large Dataset Support

* **Large Variable Support (>2GB):** Enabled the DAP service to support DAP4 and NetCDF4 binary responses with individual variables larger than 2GB.
* **Unlimited Dimensions:** Added support for netCDF files containing variables with unlimited dimensions.
* **HDF4 & HDF5 Optimizations:**
* Improved DMR++ build times for HDF4-EOS2 data by one to two orders of magnitude (reducing build times from minutes to seconds).
* Resolved serialization bugs for datasets with groups and added support for HDF5 Enumeration data types.
* Updated the HDF5 library version to **2.1.1** to address a critical security vulnerability (CVE) found in 1.14.6.



### 3. DMR++ Builder & Sidecar Improvements

* **`gen_dmrpp_side_car` Script:** Introduced a consolidated python script to handle HDF4, HDF5/NetCDF4, and missing georeferencing data.
* **Optimized Storage:**
* Added the `-L` flag option to write variable-length data into a sidecar file if the main DMR++ file becomes too large.
* Stored CF grid-mapping 1-byte variables directly in the DMR++ instead of a sidecar file to improve response times.


* **Robustness:** Added support for HDF4 DMR++ generation even if HDF4 filename extensions are missing, and fixed building errors for complex datasets (e.g., ECCOSTRESS L2, AIRS, OMI).

### 4. Deprecations & Tech Stack Upgrades

* **Java 17 Upgrade:** Upgraded the service to Java 17, resolving intermittent TLS handshake failures during EDL validation.
* **Retired Services:** Completely retired the **WCS (Web Coverage Service)** and **w10n** services along with their associated libraries.
* **Removed Feature:** Removed the unused *Build DMR++ Service*.

---

## 📦 Docker Images Timeline

| Docker Image Tag | Release Date | Primary Focus | Cumulus Task Build Required? |
| --- | --- | --- | --- |
| **`1.17.1-52`** | Feb 5, 2025 | Bug fixes, HDF4-EOS2 georeferencing improvements, and introduction of AWS Warm Pool. | Yes (if 100% functionality with complex collections is needed) |
| **`1.17.1-118`** | Apr 4, 2025 | Up to $100\times$ faster DMR++ builds for HDF4-EOS2 files. Varying length float array support. | *Not specified* |
| **`1.17.1-166`** | Jun 5, 2025 | Support for variables >2GB, unlimited dimensions, HDF5 Enumeration types. Retired Build DMR++ Service. | Yes (for "two unlimited dimensions" use cases) |
| **`1.17.1-173`** | Jun 9, 2025 | Fixed NetCDF4 response failures for certain GESDISC collections. | No |
| **`1.17.1-219`** | Jul 1, 2025 | Local EDL token validation for faster authorization. DAP4/NetCDF4 >2GB variable support. | No |
| **`1.17.1-388`** | Sep 18, 2025 | **Critical Security Patch**. Added CloudWatch logs for service profiling and fixed trailing null byte bugs. | No |
| **`1.17.1-406-b2`** | Oct 1, 2025 | Token rotation update. Implemented local EDL token validation to decrease response times. | No |
| **`1.17.1-486`** | Nov 14, 2025 | Added Client-Id to HTTP request headers. Faster DMR++ generation for AIRS, OMI, and MCD43D. | **Suggested** |
| **`1.17.1-560`** | Jan 8, 2026 | Fixed DAP4 serialization for datasets with Groups. Faster DMR++ building; ECCOSTRESS L2 support. | No (unless ECCOSTRESS support is immediately required) |
| **`1.17.1-679`** | Feb 9, 2026 | Response buffer chunking, optional request compression, and support for "two unlimited dimensions". | **Required** |
| **`1.17.1-712-el8`** | Mar 3, 2026 | **Upgraded to Java 17**. TLS handshake fix. Supports missing HDF4 filename extensions during generation. | **Required** |
| **`1.17.1-1200-el8`** | Apr 20, 2026 | Direct chunk IO for array subsetting. **Retired WCS service**. Store CF grid-mapping in DMR++ instead of sidecars. | **Required** |
| **`1.17.1-1315-el8`** | May 7, 2026 | CloudWatch logging improvements. Handles legacy OPeNDAP URLs and unnamed dimensions. **Retired w10n service**. | **Required** (for faster HDF5 generation) |
| **`1.17.1-1358-el8`** | May 28, 2026 | **Direct S3 data access** (bypassing TEA redirects). Upgraded to HDF5 library 2.1.1 (CVE fix). Sidecar options for massive DMR++ files. | **Required** |
| **`1.17.1-173+` (Latest)** | Jun 11, 2026 | **Direct S3 DMR++ access**. Secured S3 cache keys with EDL user IDs. Timeout prevention by skipping NetCDF4 compression on floats. | *Truncated text* |


# BES

Here is a thematic summary of the provided Hyrax Backend Server (BES) change log covering the period from late March 2026 through mid-July 2026.

---

## 🛠️ Key Feature Enhancements & Refactoring

### 1. String Array Handling & Optimization

A large portion of the updates (primarily by developer `kyang2014`) focused on stabilizing and optimizing string arrays:

* **Fixed-Size String Arrays:** Completed code cleanup to manage fixed-size string arrays without creating memory leaks (**HYRAX-1225**). Switched memory allocation from `reserve` to `resize` to resolve specific Linux memory errors.
* **Buffer Overflow Prevention:** Fixed a potential buffer overflow vulnerability triggered when ingesting fixed-size string arrays (**HYRAX-2198**).
* **Array Subsetting & Dimensions:**
* Added subsetting support for both variable-length (**HYRAX-2204**) and fixed-size string arrays when chunk size exceeds array size (**HYRAX-2200**).
* Added mapping to treat 1-character string arrays as equivalent `char` arrays in netCDF (**HYRAX-2191**).

* **Size Calculation:** Revised the calculations determining the maximum response size for string arrays (**HYRAX-2193**).

### 2. Cloud & AWS Integration

* **Presigned DMR++ URLs:** Implemented functionality to presign the DMR++ URL to avoid embedding request headers inside the presigned URL resource (**HYRAX-1733**).
* **EDL User ID Caching:** Multi-tenant cache safety was enhanced by appending the Earthdata Login (EDL) user ID suffix to cache keys within both `EffectiveURLCache` (**HYRAX-2177**) and `SignedURLCache` (**HYRAX-2178**).
* **Error Messaging:** Improved user-facing errors for situations where parsing S3/STS credentials fails (**HYRAX-2169**).

### 3. Data Optimization & Formats

* **Floating-Point Compression:** Implemented a new configuration option/BES key allowing users to disable compression of large floating-point arrays in `fileout netCDF`, significantly lowering the Time to First Byte (TTFB) for data requests (**HYRAX-1956**, **HYRAX-2182**).
* **HDF5 Modernization:** Cleaned up legacy HDF5 API support in favor of the latest HDF5 APIs (**HYRAX-2118**, **HYRAX-2143**). Sped up dimension name retrieval across almost all scenarios (**HYRAX-2120**).
* **Vlen Data Storage:** Added an option for users to opt to store variable-length (`vlen`) data inside a separate sidecar file (**HYRAX-2050**).
* **WCS Retirement:** Permanently removed the `w10n_handler` module along with its associated production rules (**HYRAX-1316**).

---

## 🖥️ DevOps, CI/CD, and Build Architecture

### 1. Docker & Logging Improvements

* **Log Shipping:** Configured Docker builds to bundle 'make check' test logs into a tarball and push them directly to S3 storage targets (Travis/S3 buckets) (**#1407**, **HYRAX-2190**).
* **Security & Footprint:** Removed the `--enable-developer` flag from the `bes_core` Dockerfile build configuration (**HYRAX-2077**). Unnecessary `sed` operations on stale `.pem` files were also cleaned out of the Dockerfiles (**#1400**).

### 2. Dependency Upgrades & Cleanup

* **HDF5 2.1 Migration:** Updated baseline test suites to adjust for switching to **HDF5 version 2.1** (**HYRAX-2121**).
* **JSON Library Update:** Upgraded the embedded `nlohmann` JSON library from version 3.11.2 to 3.12.0 and established versioned symlinks (**HYRAX-2155**).
* **Token Cleanup:** Completely eliminated deprecated `echo-token` support from the codebase (**#1382**).

### 3. Test Suite & Code Quality Tweaks

* **Memory & Performance Profiling:** Added valgrind tracking parameters to isolate memory leaks and eliminated various code smells flagged by SonarCloud across multiple PRs.
* **Mac OS Builds:** Updated GitHub Actions to drop older `macos-15-intel` runners while adding `macos-14` platform test paradigms. Fixed an arm64 compilation error that grabbed stale AWS SDK headers (**#1319**).
* **GDAL Re-activation:** Re-enabled GDAL back into Travis CI/CD build environments and expanded multi-architecture testing configuration flags (**HYRAX-2012**, **HYRAX-1968**).

---
# OLFS
Here is a summary of the provided Hyrax / OLFS (OPeNDAP Line Formatted Server) change log covering developments from July 2025 through June 2026.


## 🚀 Deprecations & Feature Retirements

The development team focused heavily on cleaning up legacy components and unneeded features:

* **Hyrax Administration Interface (HAI):** The team retired the HAI entirely, including associated `web.xml` references and admin logs. They briefly rolled back this removal in December 2025, but finalized the retirement shortly after.


* **Web Services/UI Drop:** Retired the **Hyrax Gateway WebUI and API** (#308), the **WCS (Web Coverage Service)** code and resources (#281), and the **w10n Service** (#296).


* **Logging & Testing Assets:** Retired the `cloudwatch-logback-appender` jar file (#304), the `HAI_DEBUG_LOGGER`, and older **OSX build assets**. Cruft from older WCS work was also purged from the Dap4 package.



## 🛠️ Logging, Authentication & Session Enhancements

Significant improvements were made to cloud-native metrics (specifically for NASA's NGAP environment) and user security:

* **JSON & CloudWatch Logging:** Added a logback converter to generate JSON-encoded logs for the OLFS (#297). For troubleshooting, request log details were mirrored into the CloudWatch response logs (#298).


* **EDL (Earthdata Login) Instrumentation:** Implemented profiling checkpoints and leaner CloudWatch metrics tracking for EDL token validation. It was optimized to skip optimization/logging overhead when no token is present and to use the token to fetch user IDs directly.


* **Session Management:** Improved session security by renewing sessions following authentication, supporting token revocation on logout, and parsing the `X-Forwarded-For` header to correctly track client IPs.


* **Caching & Redirects:** Resolved initialization bugs within the `RequestCache` across several JSP files and fixed redirect failures targeting the ROOT context on logout (#245).



## ⚙️ CI/CD & Build Infrastructure

The vast majority of the change log tracks automated system changes:

* **Automated Builds:** Ongoing, frequent triggers executed by `The-Robot-Travis` to spin up OLFS builds for snapshot production against changing version matrices of `libdap4` and `bes`.


* **CI/CD Refactoring:** Streamlined automated branch tracking with auto-merging scripts (#275, #276), enabled multi-OS Travis builds for `hyrax-docker`, turned on `el9` builds, and downgraded the Gradle build environment back to version 8.14.3 for compatibility (#238).


* **Version Determination:** Refactored how Hyrax and OLFS determine version strings so that version numbers no longer need to be manually hardcoded into OLFS code or docker recipes (#284, #286).



## 🌐 UI & General Improvements

* **Server Header:** Added the standard HTTP response header `Server` matching Apache’s formatting (e.g., `Hyrax/1.17.1-1356`) (#295).


* **UI Tweaks:** Swapped out local interactive tutorials links for official NASA tutorial links (#255), fixed DAP4 `help.jsp` banners, and temporarily added (then subsequently removed) a government shutdown banner (#253).


* **Checksums:** Forced data checksum transmissions to be active at all times for the NGAP service to ensure consistency across separate requests (#242, #244).