---
title: Invalidating Cached Pages From AEM
seo-title: Invalidating Cached Pages From AEM
description: null
seo-description: Learn how to configure the interaction between Dispatcher and AEM to ensure effective cache management.
uuid: 66533299-55c0-4864-9beb-77e281af9359
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: 79cd94be-a6bc-4d34-bfe9-393b4107925c
index: y
internal: n
snippet: y
---

# Invalidating Cached Pages From AEM{#invalidating-cached-pages-from-aem}

When using Dispatcher with AEM, the interaction must be configured to ensure effective cache management. Depending on your environment, the configuration can also increase performance.

## Setting up AEM User Accounts {#setting-up-aem-user-accounts}

The default `admin` user account is used to authenticate the replication agents that are installed by default. You should create a dedicated user account for use with replication agents. [](/content/help/en/experience-manager/6-3/sites/administering/using/security-checklist#VerificationSteps)

For more information see the [Configure Replication and Transport Users](/content/help/en/experience-manager/6-3/sites/administering/using/security-checklist#VerificationSteps) section of the Security Checklist.

## Invalidating Dispatcher Cache from the Authoring Environment {#invalidating-dispatcher-cache-from-the-authoring-environment}

A replication agent on the AEM author instance sends a cache invalidation request to Dispatcher when a page is published. The request causes Dispatcher to eventually refresh the file in the cache as new content is published.

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-0436B4A35714BFF67F000101@AdobeID)
Last Modified Date: 2017-05-25T10:37:23.679-0400

<p>Hiding this information due to <a href="https://jira.corp.adobe.com/browse/CQDOC-5805">CQDOC-5805</a>.</p>

 -->

Use the following procedure to configure a replication agent on the AEM author instance for invalidating the Dispatcher cache upon page activation:

1. Open the AEM Tools console. ( [http://localhost:4502/miscadmin#/etc](http://localhost:4502/miscadmin#/etc))
1. Open the required replication agent below Tools/replication/Agents on author. You can use the Dispatcher Flush agent that is installed by default.
1. Click Edit, and in the Settings tab ensure that **Enabled** is selected.  

1. (optional) To enable alias or vanity path invalidation requests select the **Alias update** option.
1. On the Transport tab, enter the URI needed to access Dispatcher.  
   If you are using the standard Dispatcher Flush agent you will probably need to update the hostname and port; for example, http://&lt;*dispatcherHost*&gt;:&lt;*portApache*&gt;/dispatcher/invalidate.cache  
  
   **Note:** For Dispatcher Flush agents, the URI property is used only if you use path-based virtualhost entries to differentiate between farms. You use this field to target the farm to invalidate. For example, farm #1 has a virtual host of www.mysite.com/path1/&#42; and farm #2 has a virtual host of www.mysite.com/path2/&#42;. You can use a URL of /path1/invalidate.cache to target the first farm and /path2/invalidate.cache to target the second farm. For more information, see [Using Dispatcher with Multiple Domains](dispatcher-domains.md).

1. Configure other parameters as required.  
1. Click OK to activate the agent.

Alternatively, you can also access and configure the Dispatcher Flush agent from the [AEM Touch UI](/content/help/en/experience-manager/6-2/sites/deploying/using/replication#ConfiguringaDispatcherFlushagent).

For additional details on how to enable access to vanity URLs, see [Enabling Access To Vanity URLs](dispatcher-configuration.md#EnablingAccesstoVanityURLsvanityurls).

>[!NOTE]
>
>The agent for flushing dispatcher cache does not have to have a user name and password, but if configured they will be sent with basic authentication.

There are two potential issues with this approach:

* The Dispatcher must be reachable from the authoring instance. If your network (e.g. the firewall) is configured such that access between the two is restricted, this may not be the case.

* Publication and cache invalidation take place at the same time. Depending on the timing, a user may request a page just after it was removed from the cache, and just before the new page is published. AEM now returns the old page, and the Dispatcher caches it again. This is more of an issue for large sites.

## Invalidating Dispatcher Cache from a Publishing Instance {#invalidating-dispatcher-cache-from-a-publishing-instance}

Under certain circumstances performance gains can be made by transferring cache management from the authoring environment to a publishing instance. It will then be the publishing environment (not the AEM authoring environment) that sends a cache invalidation request to Dispatcher when a published page is received.

Such circumstances include:

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

* Preventing possible timing conflicts between Dispatcher and the publish instance (see [Invalidating Dispatcher cache from the Authoring Environment](#InvalidatingDispatchercachefromtheAuthoringEnvironment)).
* The system includes several publishing instances that reside on high performance servers, and only one authoring instance.

>[!NOTE]
>
>The decision to use this method should be made by an experienced AEM administrator.

The dispatcher flush is controlled by a replication agent operating on the publish instance. However, the configuration is made on the authoring environment and then transferred by activating the agent:

1. Open the AEM Tools console.
1. Open the required replication agent below Tools/replication/Agents on publish. You can use the Dispatcher Flush agent that is installed by default.
1. Click Edit, and in the Settings tab ensure that **Enabled** is selected.
1. (optional) To enable alias or vanity path invalidation requests select the **Alias update** option.
1. On the Transport tab, enter the URI needed to access Dispatcher.  
   If you are using the standard Dispatcher Flush agent you will probably need to update the hostname and port; for example, http://&lt;*dispatcherHost*&gt;:&lt;*portApache*&gt;/dispatcher/invalidate.cache  
  
   **Note:** For Dispatcher Flush agents, the URI property is used only if you use path-based virtualhost entries to differentiate between farms. You use this field to target the farm to invalidate. For example, farm #1 has a virtual host of www.mysite.com/path1/&#42; and farm #2 has a virtual host of www.mysite.com/path2/&#42;. You can use a URL of /path1/invalidate.cache to target the first farm and /path2/invalidate.cache to target the second farm. For more information, see [Using Dispatcher with Multiple Domains](dispatcher-domains.md).

1. Configure other parameters as required.
1. Repeat for every publish instance affected.

After configuring, when you activate a page from author to publish, this agent initiates a standard replication. The log includes messages indicating requests coming from your publish server, similar to the following example:

1. `<publishserver> 13:29:47 127.0.0.1 POST /dispatcher/invalidate.cache 200`

## Manually Invalidating the Dispatcher Cache {#manually-invalidating-the-dispatcher-cache}

To invalidate (or flush) the Dispatcher cache without activating a page, you can issue an HTTP request to the dispatcher. For example, you can create an AEM application that enables administrators or other applications to flush the cache.

The HTTP request causes Dispatcher to delete specific files from the cache. Optionally, the Dispatcher then refreshes the cache with a new copy.

### Delete cached files {#delete-cached-files}

Issue an HTTP request that causes Dispatcher to delete files from the cache. Dispatcher caches the files again only when it recieves a client request for the page. Deleting cached files ins this manner is appropraite for web sites that are not likely to receive simultaneous requests for the same page.

The HTTP request has the following form:

`POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
CQ-Handle: *path-pattern* 
Content-Length: 0`

Dispatcher flushes (deletes) the cached files and folders that have names that match the value of the `CQ-Handler` header. For example, a `CQ-Handle` of `/content/geomtrixx-outdoors/en` matches the following items:

* All files (of any file extension) named `en` in the `geometrixx-outdoors` directory

* Any directory named " `_jcr_content`" below the en directory (which, if it exists, contains cached renderings of sub-nodes of the page)

All other files in the dispatcher cache (or up to a particular level, depending on the `/statfileslevel` setting) are invalidated by touching the `.stat` file. This file's last modification date is compared to the last modification date of a cached document and the document is re-fetched if the `.stat` file is newer. See [Invalidating Files by Folder Level](dispatcher-configuration.md#main-pars_title_26) for details.

Invalidation (i.e. touching of .stat files) can be prevented by sending an additional Header `CQ-Action-Scope: ResourceOnly`. This can be used to flush particular resources without invalidating other parts of the cache, like JSON data that is dynamically created and requires regular flushing independent of the cache (e.g. representing data that is obtained from a third-party system to display news, stock tickers, etc.).

### Delete and recache files {#delete-and-recache-files}

Issue an HTTP request that causes Dispatcher to delete cached files, and immediately retrieve and recache the file. Delete and immediately re-cache files when web sites are likely to receive simultaneous client requests for the same page. Immediate recaching ensures that Dispatcher retrieves and caches the page only once, instead of once for each of the simultaneous client requests.

**Note:** Deleting and recaching fies should be performed on the publish instance only. When performed from the author instance, race conditions occur when attempts to recache resources occur before they haved been published.

The HTTP request has the following form:

`POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate   
`Content-Type: text/plain  
CQ-Handle: *path-pattern  
*Content-Length: *numchars in bodypage_path0*

`*page_path1* 
...  
*page_pathn*`

The page paths to immediately recache are listed on separate lines in the message body. The value of `CQ-Handle` is the path of a page that invalidates the pages to recache. (See the `/statfileslevel` parameter of the [Cache](dispatcher-configuration.md#main-pars_146_44_0010) configuration item.) The following example HTTP request message deletes and recaches the /content/geometroxx-outdoors/en.html page:

`POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
Content-Type: text/plain   
`CQ-Handle: /content/geometrixx-outdoors/en/men.html  
Content-Length: 36

/content/geometrixx-outdoors/en.html

### Example flush servlet {#example-flush-servlet}

The following code implements a servlet that sends an invalidate request to Dispatcher. The servlet receives a request message that contains `handle` and `page` parameters. These parameters provide the value of the `CQ-Handle` header and the path of the page to recache, respectively. The servlet uses the values to construct the HTTP request for Dispatcher.

When the servlet is deployed to the publish instance, the following URL causes Dispatcher to delete the /content/geometrixx-outdoors/en.html page and then cache a new copy.

`10.36.79.223:4503/bin/flushcache/html?page=/content/geometrixx-outdoors/en.html&handle=/content/geometrixx-outdoors/en/men.html`

>[!NOTE]
>
>This example servlet is not secure and only demonstrates the use of the HTTP Post request message. Your solution should secure access to the servlet.
>

```java
package com.adobe.example;

import org.apache.felix.scr.annotations.Component;
import org.apache.felix.scr.annotations.Service;
import org.apache.felix.scr.annotations.Property;

import org.apache.sling.api.SlingHttpServletRequest;
import org.apache.sling.api.SlingHttpServletResponse;
import org.apache.sling.api.servlets.SlingSafeMethodsServlet;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.apache.commons.httpclient.*;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.httpclient.methods.StringRequestEntity;

@Component(metatype=true)
@Service
public class Flushcache extends SlingSafeMethodsServlet {

 @Property(value="/bin/flushcache")
 static final String SERVLET_PATH="sling.servlet.paths";

 private Logger logger = LoggerFactory.getLogger(this.getClass());

 public void doGet(SlingHttpServletRequest request, SlingHttpServletResponse response) {
  try{ 
      //retrieve the request parameters
      String handle = request.getParameter("handle");
      String page = request.getParameter("page");

      //hard-coding connection properties is a bad practice, but is done here to simplify the example
      String server = "localhost"; 
      String uri = "/dispatcher/invalidate.cache";

      HttpClient client = new HttpClient();

      PostMethod post = new PostMethod("http://"+host+uri);
      post.setRequestHeader("CQ-Action", "Activate");
      post.setRequestHeader("CQ-Handle",handle);
   
      StringRequestEntity body = new StringRequestEntity(page,null,null);
      post.setRequestEntity(body);
      post.setRequestHeader("Content-length", String.valueOf(body.getContentLength()));
      client.executeMethod(post);
      post.releaseConnection();
      //log the results
      logger.info("result: " + post.getResponseBodyAsString());
      }
  }catch(Exception e){
      logger.error("Flushcache servlet exception: " + e.getMessage());
  }
 }
}
```

