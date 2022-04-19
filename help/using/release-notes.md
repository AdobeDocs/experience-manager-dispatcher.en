---
title: AEM Dispatcher Release Notes
seo-title: AEM Dispatcher Release Notes
description: Release notes specific to Adobe Experience Manager Dispatcher
seo-description: Release notes specific to Adobe Experience Manager Dispatcher
uuid: ae3ccf62-0514-4c03-a3b9-71799a482cbd
topic-tags: release-notes
content-type: reference
products: SG_EXPERIENCEMANAGER/6.4
discoiquuid: ff3d38e0-71c9-4b41-85f9-fa896393aac5
exl-id: b55c7a34-d57b-4d45-bd83-29890f1524de
---
# AEM Dispatcher Release Notes{#aem-dispatcher-release-notes}

## Release Information {#release-information}

|||
|--- |--- |
|Products|Adobe Experience Manager (AEM) Dispatcher|
|Version|4.3.5|
|Type|Minor Release|
|Date|April 04, 2022|
|Download URL|<ul><li>[Apache 2.4](release-notes.md#apache)</li><li>[Microsoft Internet Information Services (IIS)](release-notes.md#iis)</li></ul>|
|Compatibility|AEM 6.1 or higher|

## System requirements and prerequisites {#system-requirements-and-prerequisites}

Please see the [Supported Platforms](https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/technical-requirements.html)page for more information regarding requirements and prerequisites.

Adobe strongly recommends using the latest version of AEM Dispatcher to benefit from the latest functionality, the most recent bug fixes, and the best possible performance.

## Installation instructions {#installation-instructions}

For detailed instructions, see [Installing Dispatcher](dispatcher-install.md).

## Release History {#release-history}

### Release 4.3.5 (2022-Apr-04) {#apr}

**Improvements**:

* DISP-954 - Support invalidation even if expiration has not passed
* DISP-949 - Dispatcher returns 200 instead of 404 even if POST request is blocked by filter

### Release 4.3.4 (2021-Nov-29) {#nov}

**Bug Fixes**:

* DISP-833 - X-Forwarded-Host headers may contain a list of comma separated hostnames
* DISP-835 - DispatcherUseForwardedHost swallows Host header if it comes last

**Improvements**:

* DISP-874 - Creates a dispatcher configuration to turn implementation of DISP-818 either On or Off through a flag `DispatcherRestrictUncacheableContent`. The default value is Off. When On, it removes any caching headers set by mod expires for uncacheable content. This is different from the behavior found in version 4.3.3 (where the default was On) but the same as versions earlier than 4.3.3 (where the default was Off). Keeping `DispatcherRestrictUncacheableContent`'s default Off is the recommended approach so the browser cache has more flexibility. If, when upgrading from version 4.3.3 to 4.3.4, you wish to keep the same behavior as in version 4.3.3, you must explicitly set `DispatcherRestrictUncacheableContent` to On.
* DISP-841 - Dispatcher doesn't respect /serverStaleOnError for 504 response code
* DISP-874 - Create a dispatcher configuration to turn implementation of DISP-818 on or off
* DISP-883 - Trace showing URL request Decomposition in Dispatcher
* DISP-944 - pre load vanity urls

### Release 4.3.3 (2019-Oct-18) {#october}

**Bug Fixes**:

* DISP-739 - LogLevel dispatcher: **level** doesn't work
* DISP-749 - Alpine Linux dispatcher crashes with trace log level

**Improvements**:

* DISP-813 - Support in Dispatcher for openssl 1.1.x
* DISP-814 - Apache 40x errors during cache flushes
* DISP-818 - mod_expires adds Cache-Control headers for uncacheable content
* DISP-821 - Do not store log context in socket
* DISP-822 - Dispatcher should use ppoll instead of pselect
* DISP-824 - Secure DispatcherUseForwardedHost
* DISP-825 - Log special message when there's no more space on disk
* DISP-826 - Support refetch URIs with a query string

**New Features**:

* DISP-703 - Farm Specific Cache Hit Ratio
* DISP-827 - Local server for testing
* DISP-828 - Create testing docker image for dispatcher

### Release 4.3.2 (2019-Jan-31) {#jan}

**Bug Fixes**:

* DISP-734 - Dispatcher causes crash in insert_output_filter if not set as handler
* DISP-735 - REs do not work on Alpine Linux
* DISP-740 - Loading dispatcher in macOS Mojave is disabled by default
* DISP-742 - Blocked requests may leak information to auth checker protected resources

**Improvements**:

* DISP-746 - Unlabelled strings in dispatcher.any should generate a warning

**New Feature**:

* DISP-747 - Provide request information in Apache environment

### Release 4.3.1 (2018-Oct-16) {#oct}

**Bug Fixes**:

* DISP-656 - Dispatcher serves wrong ETag Header
* DISP-694 - Suppress warnings when keep alive connections go stale
* DISP-714 - Cookie based session management doesn’t work in IIS
* DISP-715 - Secure flag for renderid cookie
* DISP-720 - Temporary files not closed, can lead to exhaustion (too many open files)
* DISP-721 - Dispatcher interrupts poll() when Apache gracefully restarts child
* DISP-722 - Cache files are created with octal mode 0600
* DISP-723 - Implicit 10 minute timeout (and retry) when Render timeouts set to 0
* DISP-725 - Trailing characters after strings are silently converted to unnamed value
* DISP-726 - Log a warning when no farm actually matches the incoming host
* DISP-727 - Dispatcher checks request content length for empty cache files
* DISP-730 - 404 when trying to access header file over dispatcher
* DISP-731 - Dispatcher is vulnerable to Log Injection
* DISP-732 - Dispatcher should remove consecutive ‘/’ in the URL
* DISP-733 - Dispatcher should set (calculate) an Age Header

**Improvements**:

* DISP-656 - Dispatcher serves wrong ETag Header
* DISP-694 - Suppress warnings when keep alive connections go stale
* DISP-715 - Secure flag for renderid cookie
* DISP-722 - Cache files are created with octal mode 0600
* DISP-726 - Log a warning when no farm actually matches the incoming host

### Release 4.3.0 (2018-Jun-13) {#jun}

**Bug Fixes**:

* DISP-682 - Numeric Log Level Incorrectly Applied
* DISP-685 - 32-bit Solaris SPARC binaries have an undefined reference to __divdi3
* DISP-688 - Dispatcher doesn’t return “X-Cache-Info” header on 404 response
* DISP-690 - Last-Modified header is not cacheable
* DISP-691 - Access Violations in w3wp.exe
* DISP-693 - Need to update Architectural details for solaris servers on dispatcher download page
* DISP-695 - Issue with DispatcherLog level in Dispatcher module 4.2.3
* DISP-698 - Dispatcher TTL needs to support s-maxage and private directives
* DISP-700 - Module does not work correctly on Alpine Linux
* DISP-704 - Browser requests containing %2b are sent to the Publisher unencoded
* DISP-705 - Dispatcher crash due to double free or corruption (fasttop)
* DISP-706 - During invalidation, dispatcher is following back reference symlinks which can cause an infinite loop
* DISP-709 - Block some vanity URL extensions
* DISP-710 - Builds for Linux not usable on Cent OS 6

**Improvements**:

* DISP-652 - Dispatcher serves wrong Date header

## Helpful resources {#helpful-resources}

* [AEM Dispatcher Overview](dispatcher.md)

## Downloads {#downloads}

### Apache 2.4 {#apache}

|Platform|Architecture|OpenSSL Support|Download|
|---|---|---|---|
|Linux|i686 (32-bit)|None| [dispatcher-apache2.4-linux-i686-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.5.tar.gz)|
|Linux|i686 (32-bit)|1.0| [dispatcher-apache2.4-linux-i686-ssl1.0-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.0-4.3.5.tar.gz)|
|Linux|i686 (32-bit)|1.1| [dispatcher-apache2.4-linux-i686-ssl1.1-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl1.1-4.3.5.tar.gz)|
|Linux|x86_64 (64-bit)|None| [dispatcher-apache2.4-linux-x86_64-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.5.tar.gz)|
|Linux|x86_64 (64-bit)|1.0| [dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.0-4.3.5.tar.gz)|
|Linux|x86_64 (64-bit)|1.1| [dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl1.1-4.3.5.tar.gz)|
|Linux|aarch64 (64-bit)|None| [dispatcher-apache2.4-linux-aarch64-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-4.3.5.tar.gz)|
|Linux|aarch64 (64-bit)|1.0| [dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.0-4.3.5.tar.gz)|
|Linux|aarch64 (64-bit)|1.1| [dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-aarch64-ssl1.1-4.3.5.tar.gz)|
|macOS|arm64 (64-bit)|None| [dispatcher-apache2.4-darwin-arm64-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-arm64-4.3.5.tar.gz)|
|macOS|x86_64 (64-bit)|None| [dispatcher-apache2.4-darwin-x86_64-4.3.5.tar.gz](https://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.5.tar.gz)|

### IIS {#iis}

|Platform|Architecture|OpenSSL Support|Download|
|---|---|---|---|
|Windows|x86 (32-bit)|None| [dispatcher-iis-windows-x86-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.5.zip)|
|Windows|x86 (32-bit)|1.0| [dispatcher-iis-windows-x86-ssl1.0-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.0-4.3.5.zip)|
|Windows|x86 (32-bit)|1.1| [dispatcher-iis-windows-x86-ssl1.1-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl1.1-4.3.5.zip)|
|Windows|x64 (64-bit)|None| [dispatcher-iis-windows-x64-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.5.zip)|
|Windows|x64 (64-bit)|1.0| [dispatcher-iis-windows-x64-ssl1.0-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.0-4.3.5.zip)|
|Windows|x64 (64-bit)|1.1| [dispatcher-iis-windows-x64-ssl1.1-4.3.5.zip](https://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl1.1-4.3.5.zip)|
