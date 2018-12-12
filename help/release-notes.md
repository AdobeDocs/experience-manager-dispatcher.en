---
title: AEM Dispatcher Release Notes
seo-title: AEM Dispatcher Release Notes
description: null
seo-description: Release notes specific to Adobe Experience Manager Dispatcher
uuid: fe7ea016-c8f3-4713-b4f2-a078b7acf713
acrolinxstatus: not_checked
cq-lastmodifiedby: dpfister
topic-tags: release-notes
content-type: reference
products: SG_EXPERIENCEMANAGER/6.4
discoiquuid: 360f2ae5-c6f5-48ea-8527-085f543e7993
disttype: dist5
donotlocalize: false
gnavtheme: light
locpublishoption: now
moreHelpPaths: /content/help/en/experience-manager/6-4/morehelp/release-notes;/content/help/en/experience-manager/6-4/morehelp/release-notes
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
pagelayout: video
sidecolumn: left
index: y
internal: n
snippet: y
---

# AEM Dispatcher Release Notes{#aem-dispatcher-release-notes}

## Release Information {#release-information}

<table border="1" cellpadding="1" cellspacing="0" width="100%"> 
 <tbody>
  <tr>
   <td>Products</td> 
   <td><strong>Adobe Experience Manager (AEM) Dispatcher</strong></td> 
  </tr>
  <tr>
   <td>Version</td> 
   <td>4.3.1</td> 
  </tr>
  <tr>
   <td>Type</td> 
   <td>Minor Release</td> 
  </tr>
  <tr>
   <td>Date</td> 
   <td>October 16, 2018</td> 
  </tr>
  <tr>
   <td>Download URL</td> 
   <td>
    <ul> 
     <li><a href="release-notes.md#apache24">Apache 2.4</a></li> 
     <li><a href="release-notes.md#iis">Microsoft Internet Information Services (IIS)</a></li> 
    </ul> </td> 
  </tr>
  <tr>
   <td>Compatibility</td> 
   <td>AEM 6.1 or higher</td> 
  </tr>
 </tbody>
</table>

## System requirements and prerequisites {#system-requirements-and-prerequisites}

Please see the [Supported Platforms](/content/help/en/experience-manager/6-4/sites/deploying/using/technical-requirements) page for more information regarding requirements and prerequisites.

Adobe strongly recommends using the latest version of AEM Dispatcher to avail the latest functionality, the most recent bug fixes, and the best possible performance.

## Installation instructions {#installation-instructions}

For detailed instructions, see [Installing Dispatcher](using/dispatcher-install.md).

## Release History {#release-history}

### 4.3.1 (2018-Oct-16) {#oct}

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

### 4.3.0 (2018-Jun-13) {#jun}

**Bug Fixes:**

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

* [AEM Dispatcher Overview](using/dispatcher.md)

## Downloads {#downloads}

### Apache 2.4 {#apache}

| Platform |Architecture |SSL Support |Download |
|---|---|---|---|
| AIX |PowerPC (32-bit) |No | [dispatcher-apache2.4-aix-powerpc-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc-4.3.1.tar.gz) |
| AIX |PowerPC (32-bit) |Yes | [dispatcher-apache2.4-aix-powerpc-ssl-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc-ssl-4.3.1.tar.gz) |
| AIX |PowerPC (64-bit) |No | [dispatcher-apache2.4-aix-powerpc64-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc64-4.3.1.tar.gz) |
| AIX |PowerPC (64-bit) |Yes | [dispatcher-apache2.4-aix-powerpc64-ssl-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-aix-powerpc64-ssl-4.3.1.tar.gz) |
| Linux |i686 (32-bit) |No | [dispatcher-apache2.4-linux-i686-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-4.3.1.tar.gz) |
| Linux |i686 (32-bit) |Yes | [dispatcher-apache2.4-linux-i686-ssl-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-i686-ssl-4.3.1.tar.gz) |
| Linux |x86_64 (64-bit) |No | [dispatcher-apache2.4-linux-x86_64-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-4.3.1.tar.gz) |
| Linux |x86_64 (64-bit) |Yes | [dispatcher-apache2.4-linux-x86_64-ssl-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-linux-x86_64-ssl-4.3.1.tar.gz) |
| macOS |x86_64 (64-bit) |No | [dispatcher-apache2.4-darwin-x86_64-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-darwin-x86_64-4.3.1.tar.gz) |
| Solaris |AMD (32-bit) |No | [dispatcher-apache2.4-solaris-i386-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-i386-4.3.1.tar.gz) |
| Solaris |AMD (32-bit) |Yes | [dispatcher-apache2.4-solaris-i386-ssl-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-i386-ssl-4.3.1.tar.gz) |
| Solaris |AMD (64-bit) |No | [dispatcher-apache2.4-solaris-amd64-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-amd64-4.3.1.tar.gz) |
| Solaris |AMD (64-bit) |Yes | [dispatcher-apache2.4-solaris-amd64-ssl-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-amd64-ssl-4.3.1.tar.gz) |
| Solaris |SPARC (32-bit) |No | [dispatcher-apache2.4-solaris-sparc-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparc-4.3.1.tar.gz) |
| Solaris |SPARC (32-bit) |Yes | [dispatcher-apache2.4-solaris-sparc-ssl-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparc-ssl-4.3.1.tar.gz) |
| Solaris |SPARC (64-bit) |No | [dispatcher-apache2.4-solaris-sparcv9-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparcv9-4.3.1.tar.gz) |
| Solaris |SPARC (64-bit) |Yes | [dispatcher-apache2.4-solaris-sparcv9-ssl-4.3.1.tar.gz](http://download.macromedia.com/dispatcher/download/dispatcher-apache2.4-solaris-sparcv9-ssl-4.3.1.tar.gz) |

### IIS {#iis}

| Platform |Architecture |SSL support |Download |
|---|---|---|---|
| Windows |x86 (32-bit) |No | [dispatcher-iis-windows-x86-4.3.1.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-4.3.1.zip) |
| Windows |x86 (32-bit) |Yes | [dispatcher-iis-windows-x86-ssl-4.3.1.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x86-ssl-4.3.1.zip) |
| Windows |x64 (64-bit) |No | [dispatcher-iis-windows-x64-4.3.1.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-4.3.1.zip) |
| Windows |x64 (64-bit) |Yes | [dispatcher-iis-windows-x64-ssl-4.3.1.zip](http://download.macromedia.com/dispatcher/download/dispatcher-iis-windows-x64-ssl-4.3.1.zip) |

