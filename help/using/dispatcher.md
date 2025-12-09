---
title: Dispatcher Overview
description: Learn to use the Adobe Experience Manager Dispatcher for improved security, caching, and more on AEM Cloud Services.
pageversionid: 1193211344162
topic-tags: dispatcher
content-type: reference
exl-id: c9266683-6890-4359-96db-054b7e856dd0
---
# Dispatcher overview {#dispatcher-overview}

>[!NOTE]
>
>Dispatcher versions are independent of AEM (Adobe Experience Manager). You may have been redirected to this page if you followed a link to the Dispatcher documentation. That link was embedded in the documentation for a previous version of AEM.

Dispatcher is Adobe Experience Manager's caching and load-balancing tool that is used with an enterprise-class web server.

The process for deploying the Dispatcher is independent of the web server and the OS platform chosen:

1. Learn about Dispatcher (this page). Also, see [frequently asked questions about Dispatcher](/help/using/dispatcher-faq.md).
1. Install a [supported web server](https://experienceleague.adobe.com/en/docs/experience-manager-65/content/implementing/deploying/introduction/technical-requirements) according to the web server documentation.  
1. [Install the Dispatcher module](dispatcher-install.md) on your web server and configure the web server accordingly.
1. [Configure Dispatcher](dispatcher-configuration.md) (the dispatcher.any file).  
1. [Configure AEM](page-invalidate.md) so that content updates invalidate the cache.

>[!NOTE]
>
>To gain a better understanding of how the Dispatcher works with AEM:
>
>* See [Ask the AEM Community Experts for July 2017](https://communities.adobeconnect.com/pf0gem7igw1f/).
>* Access [this repository](https://github.com/adobe/aem-dispatcher-experiments). It contains a collection of experiments in a "take-home" laboratory format.


Use the following information as required:

* [The Dispatcher Security Checklist](security-checklist.md)
<!-- URL is 404! * [The Dispatcher Knowledge Base](https://helpx.adobe.com/experience-manager/kb/index/dispatcher.html) -->
* [Optimizing a Website for Cache Performance](https://experienceleague.adobe.com/en/docs/experience-manager-65/content/implementing/deploying/configuring/configuring-performance)
* [Using the Dispatcher with Multiple Domains](dispatcher-domains.md)
* [Using SSL with Dispatcher](dispatcher-ssl.md)
* [Implementing Permission-Sensitive Caching](permissions-cache.md)
* [Troubleshooting Dispatcher Problems](dispatcher-troubleshooting.md)
* [Dispatcher Top Issues FAQ](dispatcher-faq.md)

>[!NOTE]
>
>**The most common use of Dispatcher** is to cache responses from an AEM **publish instance**, to increase the responsiveness and security of your externally facing published website. Most of the discussion focuses on this case.
>
>But, the Dispatcher can also be used to increase the responsiveness of your **author instance**. This fact is true, particularly if you have a large number of users editing and updating your website. For details specific to this case see [Using a Dispatcher with an Author Server](#using-a-dispatcher-with-an-author-server), below.

## Why use Dispatcher to implement caching? {#why-use-dispatcher-to-implement-caching}

There are two basic approaches to web publishing:

* **Static Web Servers**: such as Apache or IIS, are simple, but fast.
* **Content Management Servers**: which provide dynamic, real-time, intelligent content, but require more computation time and other resources.

The Dispatcher helps realize an environment that is both fast and dynamic. It works as part of a static HTML server, such as Apache, with the aim of:

* storing (or "caching") as much of the site content as is possible, in the form of a static website
* accessing the layout engine as little as possible.

Which means that:

* **static content** is handled with the same speed and ease as on a static web server. Also, you can use the administration and security tools available for your static web servers.

* **dynamic content** is generated as needed, without slowing the system down any more than absolutely necessary.

The Dispatcher contains mechanisms to generate, and update, static HTML based on the content of the dynamic site. You can specify in detail which documents are stored as static files and which are always generated dynamically.

This section illustrates the principles behind this process.

### Static web server {#static-web-server}

![](assets/chlimage_1-3.png)

A static web server, such as Apache or IIS, serves static HTML files to visitors of your website. Static pages are created once, so the same content is delivered for each request.

This process is simple and efficient. If a visitor requests a file such as an HTML page, the file is taken directly from memory; at worst, it is read from the local drive. Static web servers have been available for quite some time. As such, there are a wide range of tools for administration and security management. These tools are well integrated with network infrastructures.

### Content management servers {#content-management-servers}

![](assets/chlimage_1-4.png)

If you use a CMS (Content Management Server), such as AEM, an advanced layout engine processes the request from a visitor. The engine reads content from a repository which, combined with styles, formats and access rights, transforms the content into a document that is tailored to a visitor's needs and rights.

This workflow lets you create richer, dynamic content, which increases the flexibility and functionality of your website. However, the layout engine requires more processing power than a static server, so this setup may be prone to slowdown if many visitors use the system.

## How Dispatcher performs caching {#how-dispatcher-performs-caching}

![](assets/chlimage_1-5.png) 

**The Cache Directory** For caching, the Dispatcher module uses the web server's ability to serve static content. The Dispatcher places the cached documents in the root of the Web server. 

>[!NOTE]
>
>When lacking the configuration for HTTP Header Caching, the Dispatcher stores only the HTML code of the page - it does not store the HTTP headers. This scenario can be an issue if you use different encodings within your website, as these pages may get lost. To enable HTTP Header Caching, see [Configuring the Dispatcher Cache.](https://experienceleague.adobe.com/en/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration)

>[!NOTE]
>
>Locating the document root of your web server on network-attached storage (NAS) causes performance degradation. Also, when a document root on NAS is shared between multiple web servers, intermittent locks can occur when replication actions are performed.

>[!NOTE]
>
>The Dispatcher stores the cached document in a structure equal to the requested URL. 
>
>There can be OS-level limitations for the length of the file name. That is, if you have a URL with numerous selectors.

### Methods for caching

The Dispatcher has two primary methods for updating the cache content when changes are made to the website.

* **Content Updates** remove the pages that have changed, and files that are directly associated with them.
* **Auto-Invalidation** automatically invalidates those parts of the cache that may be out of date after an update. That is, it effectively flags relevant pages as being out of date, without deleting anything.

### Content updates

In a content update, one or more AEM documents change. AEM sends a syndication request to the Dispatcher, which updates the cache accordingly:

1. It deletes the modified files from the cache.
1. It deletes all files that start with the same handle from the cache. For example, if the file `/en/index.html` is updated, all the files that start with `/en/index.` are deleted. This mechanism lets you design cache-efficient sites, especially for picture navigation.
1. It *touches* the so-called **statfile**, which updates the timestamp of the statfile to indicate the date of the last change.

The following points should be noted:

* Content Updates are typically used with an authoring system that "knows" what must be replaced.
* Files affected by a content update are removed, but not replaced immediately. The next time that file is requested, Dispatcher retrieves the updated version from the AEM instance and replaces the cached copy.
* Typically, automatically generated pictures that incorporate text from a page are stored in picture files starting with the same handle - thus ensuring that the association exists for deletion. For example, you may store the title text of the page mypage.html as the picture mypage.titlePicture.gif in the same folder. This way the picture is automatically deleted from the cache each time the page is updated, so you can be sure that the picture always reflects the current version of the page.
* You may have several statfiles, for example one per language folder. If a page is updated, AEM looks for the next parent folder containing a statfile, and *touches* that file.

### Auto-invalidation

Auto-invalidation automatically invalidates parts of the cache - without physically deleting any files. At every content update, the so-called statfile is touched, so its timestamp reflects the last content update.

The Dispatcher has a list of files that are subject to auto-invalidation. When a document from that list is requested, the Dispatcher compares the date of the cached document with the timestamp of the statfile:

* if the cached document is newer, the Dispatcher returns it.
* if it is older, the Dispatcher retrieves the current version from the AEM instance.

Again, certain points should be noted:

* Auto invalidation is typically used when the inter-relations are complex, such as HTML pages. These pages contain links and navigation entries, so they usually have to be updated after a content update. If you have automatically generated PDF or picture files, you may choose to auto-invalidate those files too.
* Auto-invalidation does not involve any action by the Dispatcher at update time, except for touching the statfile. However, touching the statfile automatically renders the cache content obsolete, without physically removing it from the cache.

## How Dispatcher returns documents {#how-dispatcher-returns-documents}

![](assets/chlimage_1-6.png)

### Determine whether a document is subject to caching

You can [define which documents the Dispatcher caches in the configuration file](https://experienceleague.adobe.com/en/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration). The Dispatcher checks the request against the list of cacheable documents. If the document is not in this list, the Dispatcher requests the document from the AEM instance.

The Dispatcher always requests the document directly from the AEM instance in the following cases:

* The request URI contains a question mark `?`. This scenario usually indicates a dynamic page, such as a search result, which does not need to be cached.
* The file extension is missing. The web server needs the extension to determine the document type (the MIME-type).
* The authentication header is set (configurable).

>[!NOTE]
>
>The GET or HEAD (for the HTTP header) methods are cacheable by the Dispatcher. For additional information on response header caching, see the [Caching HTTP Response Headers](https://experienceleague.adobe.com/en/docs/experience-manager-dispatcher/using/configuring/dispatcher-configuration) section.

### Determine if a document is cached

The Dispatcher stores the cached files on the web server as if they were part of a static website. If a user requests a cacheable document, the Dispatcher checks whether that document exists in the web server's file system:

* if the document is cached, Dispatcher returns the file.
* if it is not cached, the Dispatcher requests the document from the AEM instance.

### Determine if a document is up to date

To find out if a document is up to date, the Dispatcher performs two steps:

1. It checks whether the document is subject to auto-invalidation. If not, the document is considered up to date.
1. If the document is configured for auto-invalidation, the Dispatcher checks whether it is older or newer than the last change available. If it is older, the Dispatcher requests the current version from the AEM instance and replaces the version in the cache.

>[!NOTE]
>
>Documents without **auto-invalidation** remain in the cache until they are physically deleted. For example, by a content update on the web site.

## Benefits of load balancing {#the-benefits-of-load-balancing}

Load Balancing is the practice of distributing the computational load of the website across several instances of AEM.

![](assets/chlimage_1-7.png)

You gain:

* **increased processing power** 
  In practice, increased processing power means that the Dispatcher shares document requests between several instances of AEM. Because each instance now has fewer documents to process, you have faster response times. The Dispatcher keeps internal statistics for each document category, so it can estimate the load and distribute the queries efficiently.

* **increased fail-safe coverage** 
  If the Dispatcher does not receive responses from an instance, it automatically relays requests to one of the other instances. If an instance becomes unavailable, the only effect is a slowdown of the site, proportionate to the computational power lost. However, all services continue.

* You can also manage different websites on the same static web server.

>[!NOTE]
>
>While load balancing spreads the load efficiently, caching helps to reduce the load. Therefore, try to optimize caching and reduce the overall load before you set upload balancing. Good caching may increase the load balancer's performance, or render load balancing unnecessary.

>[!CAUTION]
>
>While a single Dispatcher is able to saturate the capacity of the available Publish instances, for some rare applications it can also make sense to balance the load between two Dispatcher instances. Configurations with multiple Dispatchers must be considered carefully. The reason is because an extra Dispatcher can increase the load on the available Publish instances and can easily decrease performance in most applications.

## How the Dispatcher performs load balancing {#how-the-dispatcher-performs-load-balancing}

### Performance statistics

The Dispatcher keeps internal statistics about how fast each instance of AEM processes documents. Based on this data, the Dispatcher estimates which instance can provide the quickest response time when answering a request, and so it reserves the necessary computation time on that instance.

Different types of requests may have differing average completion times, so the Dispatcher lets you specify document categories. These categories are then considered when computing the time estimates. For example, you can distinguish between HTML pages and images, as the typical response times may well differ.

If you use an elaborate search function, you can create a category for search queries. This method helps the Dispatcher send search queries to the instance that responds fastest. It also helps prevent a slower instance from stalling when it receives several "expensive" search queries, while the others get the "cheaper" requests.

### Personalized content (Sticky Connections)

Sticky connections ensure that documents for one user are all composed on the same instance of AEM. This point is important if you use personalized pages and session data. The data is stored on the instance, so subsequent requests from the same user must return to that instance or the data is lost.

Because sticky connections restrict the Dispatcher's ability to optimize the requests, you should use them only when needed. You can specify the folder that contains the "sticky" documents, thus ensuring all documents in that folder are composed in the same instance for each user.

>[!NOTE]
>
>For most pages that use sticky connections you have to switch off caching - otherwise the page looks the same to all users, regardless of the session content. 
>
>For a *few* applications, it can be possible to use both sticky connections and caching; for example, if you display a form that writes data to the session.

## Use multiple Dispatchers {#using-multiple-dispatchers}

In complex setups, you may use multiple Dispatchers. For example, you may use:

* one Dispatcher to publish a website on the Intranet
* a second Dispatcher, under a different address and with different security settings, to publish the same content on the Internet.

In such a case, make sure that each request goes through only one Dispatcher. A Dispatcher does not handle requests that come from another Dispatcher. Therefore, make sure that both Dispatchers access the AEM website directly.

## Use Dispatcher with a CDN {#using-dispatcher-with-a-cdn}

A content delivery network (CDN), such as Akamai Edge Delivery or Amazon Cloud Front, deliver content from a location close to the end user. By that it

* speeds up response times for end users
* takes load off your servers

As an HTTP infrastructure component, a CDN works much like a Dispatcher. When a CDN node receives a request, it serves the request from its cache, if possible (the resource is available in the cache and is valid). Otherwise, it reaches out to the next closest server to retrieve the resource and cache it for further requests if appropriate.

The "next closest server" depends on your specific setup. For example, in an Akamai setup the request can take the following path:

* The Akamai Edge Node  
* The Akamai Midgress Layer
* Your firewall
* Your load balancer
* Dispatcher
* AEM

Usually, Dispatcher is the next server that might serve the document from a cache and influence the response headers returned to the CDN server.

## Control a CDN cache {#controlling-a-cdn-cache}

There are several ways to control for how long a CDN caches a resource before it refetches it from Dispatcher.

1. Explicit configuration. 
   Configure how long particular resources are held in the CDN's cache, depending on mime type, extension, request type, and so on.  

1. Expiration and cache-control headers. 
   Most CDNs honor `Expires:` and `Cache-Control:` HTTP Headers if sent by the upstream server. This method can be achieved, for example, by using the [mod_expires](https://httpd.apache.org/docs/2.4/mod/mod_expires.html) Apache Module.

1. Manual invalidation. 
   CDNs allow resources to be removed from the cache through web interfaces.
1. API-based invalidation.  
   Most CDNs also offer a REST and/or SOAP API that allows resources to be removed from the cache.

In a typical AEM setup, configuration by extension, by path, or by both &ndash; which can be achieved through points 1 and 2 above &ndash; offers possibilities to set reasonable caching periods. These caching periods are for often-used resources that do not change often, such as design images and client libraries. When new releases are deployed, typically a manual invalidation is required.

If this approach is used to cache managed content, it implies that content changes are only visible to end users once the configured caching period is expired. And, when the document is fetched from Dispatcher again.

For finer-grained control, API-based invalidation lets you invalidate a CDN's cache as the Dispatcher cache is invalidated. Based on the CDNs API, you can implement your own [ContentBuilder](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/replication/ContentBuilder.html) and [TransportHandler](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/replication/TransportHandler.html) (if the API is not REST-based), and set up a Replication Agent that uses these pieces to invalidate the CDN's cache.

>[!NOTE]
>
>See also [AEM (CQ) Dispatcher Security and CDN+Browser Caching](https://www.slideshare.net/slideshow/dispatcher-caching-aemgemspart2jan2015/44053023) and recorded presentation on [Dispatcher Caching](https://experienceleague.adobe.com/en/docs/events/experience-manager-gems-recordings/overview#).

## Use a Dispatcher with an Author server {#using-a-dispatcher-with-an-author-server}

>[!CAUTION]
>
>If you use [AEM with Touch UI](https://experienceleague.adobe.com/en/docs/experience-manager-65/content/implementing/developing/introduction/touch-ui-concepts), do **not** cache author instance content. If caching is enabled for the author instance, you must disable it and delete the contents of the cache directory. To disable caching, edit the `author_dispatcher.any` file, and modify the `/rule` property of the `/cache` section as follows:

```xml
/rules
{
/0000
{ /type "deny" /glob "*"}
}
```

A Dispatcher can be used in front of an author instance to improve authoring performance. To configure an authoring Dispatcher, do the following:

1. Install a Dispatcher in a web server (an Apache or IIS web server, see [Installing Dispatcher](dispatcher-install.md)).
1. Test the newly installed Dispatcher against a working AEM publish instance. Doing so ensures that a baseline-correct install was achieved.
1. Ensure that the Dispatcher is able to connect by way of TCP/IP to your author instance.
1. Replace the sample `dispatcher.any` file with the `author_dispatcher.any` file provided with the [Dispatcher download](release-notes.md#downloads).
1. Open the `author_dispatcher.any` in a text editor and make the following changes:

    1. Change the `/hostname` and `/port` of the `/renders` section so they point to your author instance.
    1. Change the `/docroot` of the `/cache` section so they point to a cache directory. In case you are using [AEM with Touch UI](https://experienceleague.adobe.com/en/docs/experience-manager-65/content/implementing/developing/introduction/touch-ui-concepts), see the warning above.
    1. Save the changes.

1. Delete all existing files in the `/cache` > `/docroot` directory that you configured above.
1. Restart the web server.

>[!NOTE]
>
>With the provided `author_dispatcher.any` configuration, when you install a CQ5 feature pack, hotfix, or application code package that affects any content under `/libs` or `/apps`, you must delete the cached files. The files are under those directories in your Dispatcher cache. Doing so ensures that the next time they are requested the newly upgraded files are fetched, and not the old cached ones.

>[!CAUTION]
>
>If you have used the previously configured author Dispatcher and enabled a *Dispatcher flushing agent*, do the following:

1. Delete or disable the **author Dispatcher's** flushing agent on your AEM author instance.
1. Redo the author Dispatcher configuration by following the new instructions above.

<!--
[Author Dispatcher configuration file (Dispatcher 4.1.2 or later)](assets/author_dispatchernew.any)
-->
<!--[!NOTE]
>
>A related knowledge base article can be found here:  
>[How to configure the dispatcher in front of an authoring environment](https://helpx.adobe.com/cq/kb/HowToConfigureDispatcherForAuthoringEnvironment.html)
-->
