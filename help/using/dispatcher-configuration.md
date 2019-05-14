---
title: Configuring Dispatcher
seo-title: Configuring Dispatcher
description: Learn how to configure Dispatcher.
seo-description: Learn how to configure Dispatcher.
uuid: 253ef0f7-2491-4cec-ab22-97439df29fd6
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
topic-tags: dispatcher
content-type: reference
discoiquuid: aeffee8e-bb34-42a7-9a5e-b7d0e848391a
---

# Configuring Dispatcher{#configuring-dispatcher}

>[!NOTE]
>
>Dispatcher versions are independent of AEM. You may have been redirected to this page if you followed a link to the Dispatcher documentation that is embedded in the documentation for a previous version of AEM.

The following sections describe how to configure various aspects of the Dispatcher.

## Support for IPv4 and IPv6 {#support-for-ipv-and-ipv}

All elements of AEM and Dispatcher can be installed in both IPv4 and IPv6 networks. See [IPV4 and IPV6](https://helpx.adobe.com/experience-manager/6-3/sites/deploying/using/technical-requirements.html#AdditionalPlatformNotes).

## Dispatcher Configuration Files {#dispatcher-configuration-files}

By default the Dispatcher configuration is stored in the `dispatcher.any` text file, though you can change the name and location of this file during installation.

The configuration file contains a series of single-valued or multi-valued properties that control the behavior of Dispatcher:

* Property names are prefixed with a forward slash `/`.
* Multi-valued properties enclose child items using braces `{ }`.

An example configuration is structured as follows:

```xml
# name of the dispatcher
/name "internet-server"

# each farm configures a set off (loadbalanced) renders
/farms
 {
  # first farm entry (label is not important, just for your convenience)
   /website 
     {  
     /clientheaders
       {
       # List of headers that are passed on
       }
     /virtualhosts
       {
       # List of URLs for this Web site
       }
     /sessionmanagement 
       {
       # settings for user authentification
       }
     /renders
       {
       # List of AEM instances that render the documents
       }
     /filter
       {
       # List of filters
       }
     /vanity_urls
       {
       # List of vanity URLs
       }
     /cache
       {
       # Cache configuration
       /rules
         {
         # List of cachable documents
         }
       /invalidate
         {
         # List of auto-invalidated documents
         }
       }
     /statistics
       {
       /categories
         {
         # The document categories that are used for load balancing estimates
         }
       }
     /stickyConnectionsFor "/myFolder"
     /health_check
       {
       # Page gets contacted when an instance returns a 500
       }
     /retryDelay "1"
     /numberOfRetries "5"
     /unavailablePenalty "1"
     /failover "1"
     }
 }
```

You can include other files that contribute to the configuration:

* If your configuration file is large you can split it into several smaller files (that are easier to manage) then include these.
* To include files that are generated automatically.

For example, to include the file myFarm.any in the /farms configuration use the following code:

```xml
/farms
  {
  $include "myFarm.any"
  }
```

Use the asterisk ("*") as a wildcard to specify a range of files to include.

For example, if the files `farm_1.any` through to `farm_5.any` contain the configuration of farms one to five, you can include them as follows:

```xml
/farms
  {
  $include "farm_*.any"
  }
```

## Using Environment Variables {#using-environment-variables}

You can use environment variables in string-valued properties in the dispatcher.any file instead of hard-coding the values. To include the value of an environment variable, use the format `${variable_name}`.

For example, if the dispatcher.any file is located in the same directory as the cache directory, the following value for the [docroot](dispatcher-configuration.md#main-pars-title-30) property can be used:

```xml
/docroot "${PWD}/cache"
```

As another example, if you create an environment variable named `PUBLISH_IP` that stores the hostname of the AEM publish instance, the following configuration of the [/renders](dispatcher-configuration.md#main-pars-127-25-0008) property can be used:

```xml
/renders {
  /0001 {
    /hostname "${PUBLISH_IP}"
    /port "8443"
  }
}
```

## Naming the Dispatcher Instance {#naming-the-dispatcher-instance-name}

Use the `/name` property to specify a unique name to identify your Dispatcher instance. The `/name` property is a top-level property in the configuration structure.

## Defining Farms {#defining-farms-farms}

The `/farms` property defines one or more sets of Dispatcher behaviors, where each set is associated with different web sites or URLs. The `/farms` property can include a single farm or multiple farms:

* Use a single farm when you want Dispatcher to handle all of your web pages or web sites in the same way.
* Create multiple farms when different areas of your web site or different web sites require different Dispatcher behavior.

The `/farms` property is a top-level property in the configuration structure. To define a farm, add a child property to the `/farms` property. Use a property name that uniquely identifies the farm within the Dispatcher instance.

The `/*farmname*` property is multi-valued, and contains other properties that define Dispatcher behavior:

* The URLs of the pages that the farm applies to.
* One or more service URLs (typically of AEM publish instances) to use for rendering documents.
* The statistics to use for load-balancing multiple document renderers.
* Several other behaviors, such as which files to cache and where.

The value can have include any alphanumeric (a-z, 0-9) character. The following example shows the skeleton definition for two farms named `/daycom` and `/docsdaycom`:

```xml
#name of dispatcher
/name "day sites"

#farms section defines a list of farms or sites
/farms
{
   /daycom
   {
       ...
   }
   /docdaycom
   {
      ...
   }
}
```

>[!NOTE]
>
>If you use more than one render farm, the list is evaluated bottom-up. This is particularly relevant when defining [Virtual Hosts](dispatcher-configuration.md#main-pars-117-15-0006) for your websites.

Each farm property can contain the following child properties:

|Property name|Description|
|--- |--- |
|[/homepage](#specify-a-default-page-iis-only-homepage)|Default homepage (optional)(IIS only)|
|[/clientheaders](#specifying-the-http-headers-to-pass-through-clientheaders)|The headers from the client HTTP request to pass through.|
|[/virtualhosts](#identifying-virtual-hosts-virtual-hosts)|The virtual hosts for this farm.|
|[/sessionmanagement](#enabling-secure-sessions-session-management)|Support for session management and authentication.|
|[/renders](#defining-page-renderers-renders)|The servers that provide rendered pages (typically AEM publish instances).|
|[/filter](#configuring-access-to-content-filter)|Defines the URLs to which Dispatcher enables access.|
|[/vanity_urls](#enabling-access-to-vanity-urls-vanity-urls)|Configures access to vanity URLs.|
|[/propagateSyndPost](#forwarding-syndication-requests-propagate-syndpost)|Support for the forwarding of syndication requests.|
|[/cache](#configuring-the-dispatcher-cache-cache)|Configures caching behavior.|
|[/statistics](#configuring-load-balancing-statistics)|Defining statistic categories for load-balancing calculations.|
|[/stickyConnectionsFor](#identifying-a-sticky-connection-folder-sticky-connections-for)|The folder that contains sticky documents.|
|[/health_check](#specifying-a-health-check-page)|The URL to use to determine server availability.|
|[/retryDelay](#specifying-the-page-retry-delay)|The delay before retrying a failed connection.|
|[/unavailablePenalty](#reflecting-server-unavailability-in-dispatcher-statistics)|Penalties that affect statistics for load-balancing calculations.|
|[/failover](#using-the-fail-over-mechanism)|Resend requests to different renders when the original request fails.|

## Specify a Default Page (IIS Only) - /homepage {#specify-a-default-page-iis-only-homepage}

>[!CAUTION]
>
>The `/homepage`parameter (IIS only) no longer works. Instead, you should use the [IIS URL Rewrite Module](https://docs.microsoft.com/en-us/iis/extensions/url-rewrite-module/using-the-url-rewrite-module).
>
>If you are using Apache, you should use the `mod_rewrite` module. See the Apache web site documentation for information about `mod_rewrite` (for example, [Apache 2.4](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)). When using `mod_rewrite`, it is advisable to use the flag ** ['passthrough|PT' (pass through to next handler)](https://helpx.adobe.com/dispatcher/kb/DispatcherModReWrite.html)** to force the rewrite engine to set the `uri` field of the internal `request_rec` structure to the value of the `filename` field.

<!-- 

Comment Type: draft

<p>The optional /homepage parameter specifies the page that Dispatcher returns when a client requests an undeterminable page or file.</p> 
<p>Typically this situation occurs when a user specifies an URL for which neither IIS or AEM provides an automatic redirection target. For example, if the AEM render instance is shut down after the content is cached, the content redirect URL is unavailable.</p> 
<p>The following example configuration displays the <span class="code">index.html</span> page in such circumstances:</p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  /homepage&nbsp;"/index.html" 
</codeblock>

 -->

<!-- 

Comment Type: draft

<p>The <span class="code">/homepage</span> section is located inside the <span class="code">/farms</span> section, for example:<br /> </p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  #name&nbsp;of&nbsp;dispatcher!!discoiqbr!!/name&nbsp;"day&nbsp;sites"!!discoiqbr!!!!discoiqbr!!#farms&nbsp;section&nbsp;defines&nbsp;a&nbsp;list&nbsp;of&nbsp;farms&nbsp;or&nbsp;sites!!discoiqbr!!/farms!!discoiqbr!!{!!discoiqbr!!&nbsp;&nbsp;&nbsp;/daycom!!discoiqbr!!&nbsp;&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;/homepage&nbsp;"/index.html"!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...!!discoiqbr!!&nbsp;&nbsp;&nbsp;}!!discoiqbr!!&nbsp;&nbsp;&nbsp;/docdaycom!!discoiqbr!!&nbsp;&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;...!!discoiqbr!!&nbsp;&nbsp;&nbsp;}!!discoiqbr!!} 
</codeblock>

 -->

## Specifying the HTTP Headers to Pass Through {#specifying-the-http-headers-to-pass-through-clientheaders}

The `/clientheaders` property defines a list of HTTP headers that Dispatcher passes from the client HTTP request to the renderer (AEM instance).

By default Dispatcher forwards the standard HTTP headers to the AEM instance. In some instances, you might want forward additional headers, or remove specific headers:

* Add headers, such as custom headers, that your AEM instance expects in the HTTP request.
* Remove headers, such as authentication headers, that are only relevant to the web server.

If you customize the set of headers to pass through, you must specify an exhaustive list of headers, including those that are normally included by default.

For example, a Dispatcher instance that handles page activation requests for publish instances requires the `PATH` header in the `/clientheaders` section. The `PATH` header enables communication between the replication agent and the dispatcher.

The following code is an example configuration for `/clientheaders`:

```shell
/clientheaders
  {
  "CSRF-Token"
  "X-Forwarded-Proto"
  "referer"
  "user-agent"
  "authorization"
  "from"
  "content-type"
  "content-length"
  "accept-charset"
  "accept-encoding"
  "accept-language"
  "accept"
  "host"
  "if-match"
  "if-none-match"
  "if-range"
  "if-unmodified-since"
  "max-forwards"
  "proxy-authorization"
  "proxy-connection"
  "range"
  "cookie"
  "cq-action"
  "cq-handle"
  "handle"
  "action"
  "cqstats"
  "depth"
  "translate"
  "expires"
  "date"
  "dav"
  "ms-author-via"
  "if"
  "lock-token"
  "x-expected-entity-length"
  "destination"
  "PATH"
  }

```

## Identifying Virtual Hosts {#identifying-virtual-hosts-virtualhosts}

The `/virtualhosts` property defines a list of all hostname/URI combinations that Dispatcher accepts for this farm. You can use the asterisk ("*") character as a wildcard. Values for the / `virtualhosts` property use the following format:

```xml
[scheme]host[uri][*]
```

* `scheme`: (Optional) Either `https://` or `https://.`
* `host`: The name or IP address of the host computer and the port number if necessary. (See [https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.23))
* `uri`: (Optional) The path to the resources.

The following example configuration handles requests for the .com and .ch domains of myCompany, and all domains of mySubDivision:

```xml
   /virtualhosts
    {
    "www.myCompany.com"
    "www.myCompany.ch"
    "www.mySubDivison.*"
    }
```

The following configuration handles *all* requests:

```xml
   /virtualhosts
    {
    "*"
    }
```

### Resolving the Virtual Host {#resolving-the-virtual-host}

When Dispatcher receives an HTTP or HTTPS request, it finds the virtual host value that best-matches the `host,` `uri`, and `scheme` headers of the request. Dispatcher evaluates the values in the `virtualhosts` properties in the following order:

* Dispatcher begins at the lowest farm and progresses upward in the dispatcher.any file.
* For each farm, Dispatcher begins with the topmost value in the `virtualhosts` property and progresses down the list of values.

Dispatcher finds the best-matching virtual host value in the following manner:

* The first-encountered virtual host that matches all three of the `host`, the `scheme`, and the `uri` of the request is used.
* If no `virtualhosts` values has `scheme` and `uri` parts that both match the `scheme` and `uri` of the request, the first-encountered virtual host that matches the `host` of the request is used.
* If no `virtualhosts` values have a host part that matches the host of the request, the topmost virtual host of the topmost farm is used.

Therefore, you should place your default virtual host at the top of the `virtualhosts` property in the topmost farm of your dispatcher.any file.

### Example Virtual Host Resolution {#example-virtual-host-resolution}

The following example represents a snippet from a dispatcher.any file that defines two Dispatcher farms, and each farm defines a `virtualhosts` property.

```xml
/farms
  {
  /myProducts 
    { 
    /virtualhosts
      {
      "www.mycompany.com"
      }
    /renders
      {
      /hostname "server1.myCompany.com"
      /port "80"
      }
    }
  /myCompany 
    { 
    /virtualhosts
      {
      "www.mycompany.com/products/*"
      }
    /renders
      {
      /hostname "server2.myCompany.com"
      /port "80"
      }
    }
  }
```

Using this example, the following table shows the virtual hosts that are resolved for the given HTTP requests:

| Request URL |Resolved virtual host |
|---|---|
| `https://www.mycompany.com/products/gloves.html`|`www.mycompany.com/products/*;`|
| `https://www.mycompany.com/about.html` |`www.mycompany.com` |

## Enabling Secure Sessions - /sessionmanagement {#enabling-secure-sessions-sessionmanagement}

>[!CAUTION]
>
>`/allowAuthorized` **must** be set to `"0"` in the `/cache` section in order to enable this feature.

Create a secure session for access to the render farm so that users need to log in to access any page in the farm. After logging in, users can access all pages in the farm. See [Creating a Closed User Group](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/cug.html#CreatingTheUserGroupToBeUsed) for information about using this feature with CUGs.

The `/sessionmanagement` property is a subproperty of `/farms`.

>[!CAUTION]
>
>If sections of your website use different access requirements, you need to define multiple farms.

**/sessionmanagement** has several sub-parameters:

**/directory** (mandatory)

The directory that stores the session information. If the directory does not exist, it is created.

**/encode** (optional)

How the session information is encoded. Use "md5" for encryption using the md5 algorithm, or "hex" for hexadecimal encoding. If you encrypt the session data, a user with access to the file system cannot read the session contents. The default is "md5".

**/header** (optional)

The name of the HTTP header or cookie that stores the authorization information. If you store the information in the http header, use `HTTP:<*header-name*>`. To store the information in a cookie, use `Cookie:<header-name>`. If you do not specify a value `HTTP:authorization` is used.

**/timeout** (optional)

The number of seconds until the session times out after it has been used last. If not specified "800" is used, so the session times out a little over 13 minutes after the last request of the user.

An example configuration looks as follows:

```xml
/sessionmanagement 
  { 
  /directory "/usr/local/apache/.sessions" 
  /encode "md5" 
  /header "HTTP:authorization" 
  /timeout "800" 
  }

```

## Defining Page Renderers {#defining-page-renderers-renders}

The /renders property defines the URL to which Dispatcher sends requests to render a document. The following example `/renders` section identifies a single AEM instance for rendering:

```xml
/renders
  {
    /myRenderer
      {
      # hostname or IP of the renderer
      /hostname "aem.myCompany.com"
      # port of the renderer
      /port "4503"
      # connection timeout in milliseconds, "0" (default) waits indefinitely
      /timeout "0"
      }
  }
```

The following example /renders section identifies an AEM instance that runs on the same computer as Dispatcher:

```xml
/renders
  {
    /myRenderer
     {
     /hostname "127.0.0.1"
     /port "4503"
     }
  }

```

The following example /renders section distributes render requests equally among two AEM instances:

```xml
/renders
  {
    /myFirstRenderer
      {
      /hostname "aem.myCompany.com"
      /port "4503"
      }
    /mySecondRenderer
      {
      /hostname "127.0.0.1"
      /port "4503"
      }
  }

```

### Renders options {#renders-options}

**/timeout**

Specifies the connection timeout accessing the AEM instance in milliseconds. The default is "0", causing the Dispatcher to wait indefinitely.

**/receiveTimeout**

Specifies the time in milliseconds that a response is allowed to take. The default is "600000", causing Dispatcher to wait for 10 Minutes. A setting of "0" eliminates the timeout completely.  
If the timeout is reached while parsing response headers, an HTTP Status of 504 (Bad Gateway) is returned. If the timeout is reached while the response body is read, the Dispatcher will return the incomplete response to the client but delete any cache file that might have been written.

**/ipv4**

Specifies whether Dispatcher uses the `getaddrinfo` function (for IPv6) or the `gethostbyname` function (for IPv4) for obtaining the IP address of the render. A value of 0 causes `getaddrinfo` to be used. A value of 1 causes `gethostbyname` to be used. The default value is 0.

The getaddrinfo function returns a list of IP addresses. Dispatcher iterates the list of addresses until it establishes a TCP/IP connection. Therefore, the ipv4 property is important when the render hostname is associated with mutliple IP addresses and the host, in response to the getaddrinfo function, returns a list of IP addresses that are always in the same order. In this situation, you should use the gethostbyname function so that the IP address that Dispatcher connects with is randomized.

Amazon Elastic Load Balancing (ELB) is such a service that responds to getaddrinfo with a potentially same-ordered list of IP addresses.

**/secure**

If the `/secure` property has a value of "1" Dispatcher uses HTTPS to communicate with the AEM instance. For additional details, also see [Configuring Dispatcher to Use SSL](dispatcher-ssl.md#configuring-dispatcher-to-use-ssl).

**/always-resolve**

With Dispatcher version **4.1.6**, you can configure the `/always-resolve` property as follows:

* When set to "1" it will resolve the host-name on every request (the Dispatcher will never cache any IP address). There may be a slight performance impact due to the additional call required to get the host information for each request.
* If the property is not set, the IP address will be cached by default.

Also, this property can be used in case you run into dynamic IP resolution issues, as shown in the following sample:

```xml
/rend {
  /0001 {
     /hostname "host-name-here"
     /port "4502"
     /ipv4 "1"
     /always-resolve "1"
     }
  }
```

## Configuring Access to Content {#configuring-access-to-content-filter}

Use the `/filter` section to specify the HTTP requests that Dispatcher accepts. All other requests are sent back to the web server with a 404 error code (page not found). If no `/filter` section exists, all requests are accepted.

**Note:** Requests for the [statfile](dispatcher-configuration.md#main-pars-title-28) are always rejected.

>[!CAUTION]
>
>See the [Dispatcher Security Checklist](security-checklist.md) for further considerations when restricting access using Dispatcher. Also, read the [AEM Security Cheklist](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html) for additional security details regarding your AEM installation.

The /filter section consist of a series of rules that either deny or allow access to content according to patterns in the request-line part of the HTTP request. You should use a whiltelist strategy for your /filter section:

* First, deny access to everything.
* Allow access to content as needed.

### Defining a Filter {#defining-a-filter}

Each item in the `/filter` section includes a type and a pattern that is matched with a specific element of the request line or the entire request line. Each filter can contain the following items:

* **Type**: The `/type` indicates whether to allow or deny access for the requests that match the pattern. The value can be either `allow` or `deny`. 

* **Element of the Request Line:** Include `/method`, `/url`, `/query`, or `/protocol` and a pattern for filtering requests according to these specific parts of the request-line part of the HTTP request. Filtering on elements of the request line (rather than on the entire request line) is the preferred filter method.

* **Advanced Elements of the Request Line:** Starting with Dispatcher 4.2.0, four new filter elements are available for use. These new elements are `/path`, `/selectors`, `/extension` and `/suffix` respectively. Include one or more of these items to further control URL patterns.

>[!NOTE]
>
>For more information about what part of the request line each of these elements references, see the [Sling URL Decomposition](https://sling.apache.org/documentation/the-sling-engine/url-decomposition.html) wiki page.

* **glob Property**: The `/glob` property is used to match with the entire request-line of the HTTP request.  

>[!CAUTION]
>
>Filtering with globs is deprecated in Dispatcher. As such, you should avoid using globs in the `/filter` sections since it may lead to security issues. So, instead of:

`/glob "* *.css *"`

you should use

`/url "*.css"`

#### The request-line Part of HTTP Requests {#the-request-line-part-of-http-requests}

HTTP/1.1 defines the [request-line](https://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html) as follows:

*Method Request-URI HTTP-Version*&lt;CRLF&gt;

The &lt;CRLF&gt; characters repesent a carriage return followed by a line feed. The following example is the request-line that is recieved when a client requests the en page of the Geometrixx-Outoors site:

GET /content/geometrixx-outdoors/en.html HTTP.1.1&lt;CRLF&gt;

Your patterns must take into account the space characters in the request-line and the &lt;CRLF&gt; characters.

#### Double-quotes vs Single-quotes {#double-quotes-vs-single-quotes}

When creating your filter rules, use double quotation marks `"pattern"` for simple patterns. If you are using Dispatcher 4.2.0 or later and your pattern includes a regular expression, you must enclose the regex pattern `'(pattern1|pattern2)'` within single quotation marks.

#### Regular Expressions {#regular-expressions}

After Dispatcher 4.2.0, you can include POSIX Extended Regular Expressions in your filter patterns. 

#### Troubleshooting Filters {#troubleshooting-filters}

If your filters are not triggering in the way you would expect, enable [Trace Logging](#trace-logging) on dispatcher so you can see which filter is intercepting the request.

#### Example Filter: Deny All {#example-filter-deny-all}

The following example filter section causes Dispatcher to deny requests for all files. You should deny access to all files and then allow access to specific areas.

```xml
  /0001  { /glob "*" /type "deny" }
```

Requests to an explicitly denied area result in a 404 error code (page not found) being returned.

#### Example Filter: Deny Acess to Specific Areas {#example-filter-deny-acess-to-specific-areas}

Filters also allow you to deny access to various elements for example ASP pages and sensitive areas within a publish instance. The following filter denies access to ASP pages:

```xml
/0002  { /type "deny" /url "*.asp"  }
```

#### Example Filter: Enable POST Requests {#example-filter-enable-post-requests}

The following example filter allows submitting form data by the POST method:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002 { /type "allow" /method "POST" /url "/content/[.]*.form.html" }
}
```

#### Example Filter: Allow Access to the Workflow Console {#example-filter-allow-access-to-the-workflow-console}

The following example shows a filter used to deny external access to the Workflow console:

```xml
/filter {
    /0001  { /glob "*" /type "deny" }
    /0002  {  /type "allow"  /url "/libs/cq/workflow/content/console*"  }
}
```

If your publish instance uses a web application context (for example publish) this can also be added to your filter definition.

```xml
/0003   { /type "deny"  /url "/publish/libs/cq/workflow/content/console/archive*"  }
```

If you still need to access single pages within the restricted area, you can allow access to them. For example, to allow access to the Archive tab within the Workflow console add the following section:

```xml
/0004  { /type "allow"  /url "/libs/cq/workflow/content/console/archive*"   }
```

>[!NOTE]
>
>When multiple filters patterns apply to a request, the last filter pattern that applies is effective.

#### Example filter: Using Regular Expressions {#example-filter-using-regular-expressions}

This filter enables extensions in non-public content directories using a regular expression, defined here between single quotes:

```xml
/005  {  /type "allow" /extension '(css|gif|ico|js|png|swf|jpe?g)' }
```

#### Example filter: Filter Additional Elements of a Request URL {#example-filter-filter-additional-elements-of-a-request-url}

Below is a rule example that blocks content grabbing from the `/content` path and its subtree, using filters for path, selectors and extensions:

```xml
/006 {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }
```

### Example /filter section {#example-filter-section}

When configuring Dispatcher you should restrict external access as much as possible. The following example provides minimal access for external visitors:

* `/content`
* miscellaneous content such as designs and client libraries; for example:

  * `/etc/designs/default*`
  * `/etc/designs/mydesign*`

After you create filters, [test page access](dispatcher-configuration.md#main-pars-title-19) to ensure your AEM instance is secure.

The following /filter section of the dispatcher.any file can be used as a basis in your [Dispatcher configuration](dispatcher-configuration.md) file.

This example is based on the default configuration file that is provided with Dispatcher and is intended as an example for use in a production environment. Items prefixed with # are deactivated (commented out), care should be taken if you decide to activate any of these (by removing the # on that line) as this can have a security impact.

You should deny access to everything, then allow access to specific (limited) elements:

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:32:37.986-0400

<p>We should mention the config files that are shipped with the dispatcher distribution and only give a few examples here. This aims to avoid confusion and reduce content maintenance.<br /> </p>

 -->

```xml
  /filter
      {
      # Deny everything first and then allow specific entries
      /0001 { /type "deny" /glob "*" }
      
      # Open consoles
#     /0011 { /type "allow" /url "/admin/*"  }  # allow servlet engine admin
#     /0012 { /type "allow" /url "/crx/*"    }  # allow content repository
#     /0013 { /type "allow" /url "/system/*" }  # allow OSGi console
        
      # Allow non-public content directories
#     /0021 { /type "allow" /url "/apps/*"   }  # allow apps access
#     /0022 { /type "allow" /url "/bin/*"    }
      /0023 { /type "allow" /url "/content*" }  # disable this rule to allow mapped content only
      
#     /0024 { /type "allow" /url "/libs/*"   }
#     /0025 { /type "deny"  /url "/libs/shindig/proxy*" } # if you enable /libs close access to proxy

#     /0026 { /type "allow" /url "/home/*"   }
#     /0027 { /type "allow" /url "/tmp/*"    }
#     /0028 { /type "allow" /url "/var/*"    }

      # Enable extensions in non-public content directories, using a regular expression
      /0041
        {
        /type "allow"
        /extension '(css|gif|ico|js|png|swf|jpe?g)'
        }

      # Enable features 
      /0062 { /type "allow" /url "/libs/cq/personalization/*"  }  # enable personalization

      # Deny content grabbing, on all accessible pages, using regular expressions
      /0081
        {
        /type "deny"
        /selectors '((sys|doc)view|query|[0-9-]+)'
        /extension '(json|xml)'
        }
      # Deny content grabbing for /content and its subtree
      /0082
        {
        /type "deny"
        /path "/content/*"
        /selectors '(feed|rss|pages|languages|blueprint|infinity|tidy)'
        /extension '(json|xml|html)'
        }

#     /0087 { /type "allow" /method "GET" /extension 'json' "*.1.json" }  # allow one-level json requests
}
```

>[!NOTE]
>
>When used with Apache, design your filter URL patterns according to the DispatcherUseProcessedURL property of the Dispatcher module. (See [Apache Web Server - Configure your Apache Web Server for Dispatcher](dispatcher-install.md#main-pars-55-35-1022).)

>[!NOTE]
>
>Filters 0030 and 0031 regarding Dynamic Media are applicable to AEM 6.0 and higher.

Consider the following recommendations if you do choose to extend access:

* External access to `/admin` should always be *completely* disabled if you are using CQ version 5.4 or an earlier version.  

* Care must be taken when allowing access to files in `/libs`. Access should be allowed on an individual basis.
* Deny access to the replication configuration so it cannot be seen:

    * `/etc/replication.xml*`
    * `/etc/replication.infinity.json*`

* Deny access to the Google Gadgets reverse proxy:

    * `/libs/opensocial/proxy*`

Depending on your installation, there might be additional resources under `/libs`, `/apps` or elsewhere, that must be made available. You can use the `access.log` file as one method of determining resources that are being accessed externally.

>[!CAUTION]
>
>Access to consoles and directories can present a security risk for production environments. Unless you have explicit justifications they should remain deactivated (commented out).

>[!CAUTION]
>
>If you are [using reports in a publish environment](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/reporting.html#UsingReportsinaPublishEnvironment) you should configure Dispatcher to deny access to `/etc/reports` for external visitors.

### Restricting Query Strings {#restricting-query-strings}

Since Dispatcher version 4.1.5, use the `/filter` section to restrict query strings. It is highly recommended to explicitly allow query strings and exclude generic allowance through `allow` filter elements.

A single entry can have either *glob* or some combination of *method*,*url*,*query* and *version* but not both. The following example allows the `a=*` query string and denies all other query strings for URLs that resolve to the `/etc` node:

```xml
/filter {
 /0001 { /type "deny" /method "POST" /url "/etc/*" }
 /0002 { /type "allow" /method "GET" /url "/etc/*" /query "a=*" }
}
```

>[!NOTE]
>
>If a rule contains a `/query`, it will only match requests that contain a query string and match the provided query pattern.
>
>In above example, if requests to `/etc` that have no query string should be allowed as well, the following rules would be required:
>

```xml
/filter {  
>/0001 { /type "deny" /method “*" /url "/path/*" }  
>/0002 { /type "allow" /method "GET" /url "/path/*" }  
>/0003 { /type “deny" /method "GET" /url "/path/*" /query "*" }  
>/0004 { /type "allow" /method "GET" /url "/path/*" /query "a=*" }  
}  
```

### Testing Dispatcher Security {#testing-dispatcher-security}

Dispatcher filters should block access to the following pages and scripts on AEM publish instances. Use a web browser to attempt to open the following pages as a site visitor would and verify that a code 404 is returned. If any other result is obtained, adjust your filters.

Note that you should see normal page rendering for /content/add_valid_page.html?debug=layout.


* /admin
* /system/console
* /dav/crx.default
* /crx
* /bin/crxde/logs
* /jcr:system/jcr:versionStorage.json
* /_jcr_system/_jcr_versionStorage.json
* /libs/wcm/core/content/siteadmin.html
* /libs/collab/core/content/admin.html
* /libs/cq/ui/content/dumplibs.html
* /var/linkchecker.html
* /etc/linkchecker.html
* /home/users/a/admin/profile.json
* /home/users/a/admin/profile.xml
* /libs/cq/core/content/login.json
* /content/../libs/foundation/components/text/text.jsp
* /content/.{.}/libs/foundation/components/text/text.jsp
* /apps/sling/config/org.apache.felix.webconsole.internal.servlet.OsgiManager.config/jcr%3acontent/jcr%3adata
* /libs/foundation/components/primary/cq/workflow/components/participants/json.GET.servlet
* /content.pages.json
* /content.languages.json
* /content.blueprint.json
* /content.-1.json
* /content.10.json
* /content.infinity.json
* /content.tidy.json
* /content.tidy.-1.blubber.json
* /content/dam.tidy.-100.json
* /content/content/geometrixx.sitemap.txt 
* /content/add_valid_page.query.json?statement=//*
* /content/add_valid_page.qu%65ry.js%6Fn?statement=//*
* /content/add_valid_page.query.json?statement=//*[@transportPassword]/(@transportPassword%20|%20@transportUri%20|%20@transportUser)
* /content/add_valid_path_to_a_page/_jcr_content.json
* /content/add_valid_path_to_a_page/jcr:content.json
* /content/add_valid_path_to_a_page/_jcr_content.feed
* /content/add_valid_path_to_a_page/jcr:content.feed
* /content/add_valid_path_to_a_page/pagename._jcr_content.feed
* /content/add_valid_path_to_a_page/pagename.jcr:content.feed
* /content/add_valid_path_to_a_page/pagename.docview.xml
* /content/add_valid_path_to_a_page/pagename.docview.json
* /content/add_valid_path_to_a_page/pagename.sysview.xml
* /etc.xml
* /content.feed.xml
* /content.rss.xml
* /content.feed.html
* /content/add_valid_page.html?debug=layout
* /projects
* /tagging
* /etc/replication.html
* /etc/cloudservices.html
* /welcome

Issue the following command in a terminal or command prompt to determine whether anonymous write access is enabled. You should not be able to write data to the node.

`curl -X POST "https://anonymous:anonymous@hostname:port/content/usergenerated/mytestnode"`

Issue the following command in a terminal or command prompt to attempt to invalidate the Dispatcher cache, and ensure that you recieve a code 404 response:

`curl -H "CQ-Handle: /content" -H "CQ-Path: /content" https://yourhostname/dispatcher/invalidate.cache`

## Enabling Access to Vanity URLs {#enabling-access-to-vanity-urls-vanity-urls}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2015-03-25T14:23:05.185-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For https://jira.corp.adobe.com/browse/DOC-4812</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The com.adobe.granite.dispatcher.vanityurl.content package needs to be made public before publishing this contnet.</p>

 -->

Configure Dispatcher to enable access to vanity URLs that are configured for your CQ or AEM pages.

When access to vanity URLs is enabled, Dispatcher periodically calls a service that runs on the render instance to obtain a list of vanity URLs. Dispatcher stores this list in a local file. When a request for a page is denied due to a filter in the `/filter` section, Dispatcher consults the list of vanity URLs. If the denied URL is on the list, Dispatcher allows access to the vanity URL.

To enable access to vanity URLs, add a `/vanity_urls` section to the `/farms` section, similar to the following example:

```xml
 /vanity_urls {
      /url "/libs/granite/dispatcher/content/vanityUrls.html"
      /file "/tmp/vanity_urls"
      /delay 300
 }
```

The `/vanity_urls` section contains the following properties:

* `/url`: The path to the vanity URL service that runs on the render instance. The value of this property must be `"/libs/granite/dispatcher/content/vanityUrls.html"`.

* `/file`: The path to the local file where Dispatcher stores the list of vanity URLs. Make sure that Dispatcher has write-access to this file.
* `/delay`: (Seconds) The time between calls to the vanity URL service.

>[!NOTE]
>
>If your render is an instance of AEM you must install the [VanityURLS-Components](https://www.adobeaemcloud.com/content/marketplace/marketplaceProxy.html?packagePath=/content/companies/public/adobe/packages/cq600/component/vanityurls-components) package to install the vanity URL service. (See [Signing In to Package Share](https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/package-manager.html#SigningIntoPackageShare).)

Use the following procedure to enable access to vanity URLs.

1. If your render service is an AEM instance, install the com.adobe.granite.dispatcher.vanityurl.content package on the publish instance (see the note above).
1. For each vanity URL that you have configured for an AEM or CQ page, ensure that the ` [/filter](dispatcher-configuration.md#main-pars_134_32_0009)` configuration denies the URL. If necessary, add a filter that denies the URL. 
1. Add the `/vanity_urls` section below `/farms`.
1. Restart Apache web server.

## Forwarding Syndication Requests - /propagateSyndPost {#forwarding-syndication-requests-propagatesyndpost}

Syndication requests are usually intended for Dispatcher only, so by default they are not sent to the renderer (for example, an AEM instance).

If necessary, set the /propagateSyndPost property to "1" to forward syndication requests to Dispatcher. If set, you must make sure that POST requests are not denied in the filter section.

## Configuring the Dispatcher Cache - /cache {#configuring-the-dispatcher-cache-cache}

The `/cache` section controls how Dispatcher caches documents. Configure several sub-properties to implement your caching strategies:


* /docroot 
* /statfile
* /serveStaleOnError
* /allowAuthorized
* /rules
* /statfileslevel 
* /invalidate
* /invalidateHandler
* /allowedClients
* /ignoreUrlParams
* /headers
* /mode
* /gracePeriod


An example cache section might look as follows:

```xml
/cache
  {
  /docroot "/opt/dispatcher/cache"
  /statfile  "/tmp/dispatcher-website.stat"          
  /allowAuthorized "0"
      
  /rules
    {
    # List of files that are cached
    }

  /invalidate
    {
    # List of files that are auto-invalidated
    }
  }
  
```

>[!NOTE]
>
>For permission-sensitive caching, read [Caching Secured Content](permissions-cache.md).

### Specifying the Cache Directory {#specifying-the-cache-directory}

The `/docroot` property identifies the directory where cached files are stored.

>[!NOTE]
>
>The value must be the exact same path as the document root of the web server so that Dispatcher and the web server handle the same files.  
>The web server is responsible for delivering the correct status code when the dispatcher cache file is used, that's why it is important that it can find it as well.

If you use multiple farms, each farm must use a different document root.

### Naming the Statfile {#naming-the-statfile}

The `/statfile` property identifies the file to use as the statfile. Dispatcher uses this file to register the time of the most recent content update. The statfile can be any file on the web server.

The statfile has no content. When content is updated, Dispatcher updates the timestamp. The default statfile is named .stat and is stored in the docroot. Dispatcher blocks access to the statfile.

>[!NOTE]
>
>If `/statfileslevel` is configured, Dispatcher ignores the `/statfile` property and uses .stat as the name.

### Serving Stale Documents When Errors Occur {#serving-stale-documents-when-errors-occur}

The `/serveStaleOnError` property controls whether Dispatcher returns invalidated documents when the render server returns an error. By default, when a statfile is touched and invalidates cached content, Dispatcher deletes the cached content the next time it is requested.

If `/serveStaleOnError` is set to "1", Dispatcher does not delete invalidated content from the cache unless the render server returns a successful response. A 5xx response from AEM or a connection timeout causes Dispatcher to serve the outdated content and respond with and HTTP Status of 111 (Revalidation Failed).

### Caching When Authentication is Used {#caching-when-authentication-is-used}

The `/allowAuthorized` property controls whether requests that contain any of the following authentication information are cached:

* The `authorization` header.
* A cookie named `authorization`.
* A cookie named `login-token`.

By default, requests that include this authentication information are not cached because authentication is not performed when a cached document is returned to the client. This configuration prevents Dispatcher from serving cached documents to users who do not have the necessary rights.

However, if your requirements permit the caching of authenticated documents, set /allowAuthorized to one:

`/allowAuthorized "1"`

>[!NOTE]
>
>To enable session management (using the `/sessionmanagement` property), the `/allowAuthorized` property must be set to `"0"`.

### Specifying the Documents to Cache {#specifying-the-documents-to-cache}

The `/rules` property controls which documents are cached according to the document path. Regardless of the /rules property, Dispatcher never caches a document in the following circumstances:

* If the request URI contains a question mark ("?").  
  This usually indicates a dynamic page, such as a search result that does not need to be cached.
* The file extension is missing.  
  The web server needs the extension to determine the document type (the MIME-type).
* The authentication header is set (this can be configured)
* If the AEM instance responds with the following headers:

  * `no-cache`
  * `no-store`
  * `must-revalidate`

>[!NOTE]
>
>The GET or HEAD (for the HTTP header) methods are cacheable by the Dispatcher. For additional information on response header caching, see the [Caching HTTP Response Headers](dispatcher-configuration.md#caching-http-response-headers) section.

Each item in the /rules property includes a [glob](#designing-patterns-for-glob-properties) pattern and a type:

* The glob pattern is used to match the path of the document.
* The type indicates whether to cache the documents that match the glob pattern. The value can be either allow (to cache the document) or deny (to always render the document).

If you do not have dynamic pages (beyond those already excluded by the above rules), you can configure Dispatcher to cache everything. The rules section for this looks as follows:

```xml
/rules
  { 
    /0000  {  /glob "*"   /type "allow" }
  }
```

For information about glob properties, see [Designing Patterns for glob Properties](#designing-patterns-for-glob-properties).

If there are some sections of your page that are dynamic (for example a news application) or within a closed user group, you can define exceptions:

>[!NOTE]
>
>Closed user groups must not be cached as user rights are not checked for cached pages.

```xml
/rules
  {
   /0000  { /glob "*" /type "allow" }
   /0001  { /glob "/en/news/*" /type "deny" }
   /0002  { /glob "*/private/*" /type "deny"  }   
  }
```

**Compression**

On Apache web servers you can compress the cached documents. Compression allows Apache to return the document in a compressed form if so requested by the client. Compression is done automatically by enabling the Apache module `mod_deflate`, for example:

```xml
AddOutputFilterByType DEFLATE text/plain
```

The module is installed by default with Apache 2.x.

<!-- 

Comment Type: draft

<note type="note"> 
 <p>Depending on the Apache web server version you can enable <span class="code">gzip</span> compression as follows:</p> 
 <ul> 
  <li>For Apache 1.3 you can enable the <span class="code">mod_gzip </span>module. The module must be downloaded and installed.</li> 
  <li>For Apache 2.x you can enable the <span class="code">mod_deflate</span> module. The module is installed by default with Apache 2.x.<br /> </li> 
 </ul> 
 <p> </p> 
</note>

 -->

<!-- 

Comment Type: draft

<p>The following rule caches all documents in compressed form; Apache can return either the uncompressed or the compressed form to the client:</p>

 -->

<!-- 

Comment Type: draft

<codeblock gutter="true" class="syntax xml">
  /rules!!discoiqbr!!&nbsp;&nbsp;{!!discoiqbr!!&nbsp;&nbsp;&nbsp;/rulelabel&nbsp;&nbsp;{&nbsp;&nbsp;/glob&nbsp;"*"&nbsp;/type&nbsp;"allow"&nbsp;&nbsp;/compress&nbsp;"gzip"&nbsp;}!!discoiqbr!!&nbsp;&nbsp;} 
</codeblock>

 -->

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-11-13T09:23:24.326-0500

<p>Hidden the <span class="code">mod_gzip</span> content as requested in CQDOC-11124.</p>

 -->

### Invalidating Files by Folder Level {#invalidating-files-by-folder-level}

Use the `/statfileslevel` property to invalidate cached files according to their path:

* Dispatcher creates `.stat`files in each folder from the docroot folder to the level that you specify. The docroot folder is level 0.
* Files are invalidated by touching the `.stat` file. The `.stat` file's last modification date is compared to the last modification date of a cached document. The document is re-fetched if the `.stat` file is newer. 

* When a file located at a certain level is invalidated then **all** `.stat` files from the docroot **to** the level of the invalidated file or the configured `statsfilevel` (whichever is smaller) will be touched.

    * For example: if you set the `statfileslevel` property to 6 and a file is invalidated at level 5 then every `.stat` file from docroot to 5 will be touched. Continuing with this example, if a file is invalidated at level 7 then every . `stat` file from docroot to 6 will be touched (since `/statfileslevel = "6"`).

Only resources** along the path** to the invalidated file are affected. Consider the following example: a website uses the structure `/content/myWebsite/xx/.` If you set `statfileslevel` as 3, a `.stat`file is created as follows:

* `docroot`
* `/content`
* `/content/myWebsite`
* `/content/myWebsite/*xx*`

When a file in `/content/myWebsite/xx` is invalidated then every `.stat` file from docroot down to `/content/myWebsite/xx`is touched. This would be the case only for `/content/myWebsite/xx` and not for example `/content/myWebsite/yy` or `/content/anotherWebSite`.

>[!NOTE]
>
>Invalidation can be prevented by sending an additional Header `CQ-Action-Scope:ResourceOnly`. This can be used to flush particular resources without invalidating other parts of the cache. See [this page](https://adobe-consulting-services.github.io/acs-aem-commons/features/dispatcher-flush-rules/index.html) and [Manually Invalidating the Dispatcher Cache](https://helpx.adobe.com/experience-manager/dispatcher/using/page-invalidate.html) for additional details.

>[!NOTE]
>
>If you specify a value for the `/statfileslevel` property, the `/statfile` property is ignored.

### Automatically Invalidating Cached Files {#automatically-invalidating-cached-files}

The `/invalidate` property defines the documents that are automatically invalidated when content is updated.

With automatic invalidation, Dispatcher does not delete cached files after a content update, but checks their validity when they are next requested. Documents in the cache that are not auto-invalidated will remain in the cache until a content update explicitly deletes them.

Automatic invalidation is typically used for HTML pages. HTML pages often contain links to other pages, making it difficult to determine whether a content update affects a page. To make sure that all relevant pages are invalidated when content is updated, automatically invalidate all HTML pages. The following configuration invalidates all HTML pages:

```xml
  /invalidate
  {
   /0000  { /glob "*" /type "deny" }
   /0001  { /glob "*.html" /type "allow" }
  }

```

For information about glob properties, see [Designing Patterns for glob Properties](#designing-patterns-for-glob-properties).

This configuration causes the following activity when /content/geometrixx/en is activated:

* All the files with pattern en.* are removed from the /content/geometrixx/ folder.
* The /content/geometrixx/en/_jcr_content folder is removed.
* All the other files that match the /invalidate configuration are not immediately deleted. These files are deleted when the next request occurs. In our example /content/geometrixx.html is not deleted, it will be deleted when /content/geometrixx.html is requested.

If you offer automatically generated PDF and ZIP files for download, you might have to automatically invalidate these as well. A configuration example this looks as follows:

```xml
/invalidate
  {
   /0000 { /glob "*" /type "deny" }
   /0001 { /glob "*.html" /type "allow" }
   /0002 { /glob "*.zip" /type "allow" }
   /0003 { /glob "*.pdf" /type "allow" }
  }

```

The AEM integration with Adobe Analytics delivers configuration data in an analytics.sitecatalyst.js file in your website. The example dispatcher.any file that is provided with Dispatcher includes the following invalidation rule for this file:

```xml
{
   /glob "*/analytics.sitecatalyst.js"  /type "allow"
}
```

### Using custom invalidation scripts {#using-custom-invalidation-scripts}

The /invalidateHandler property allows you to define a script which is called for each invalidation request received by Dispatcher.

It is called with the following arguments:

* Handle  
  The content path that is invalidated
* Action  
  The replication Action (e.g. Activate, Deactivate)
* Action Scope  
  The replication Action's Scope (empty, unless a header of `CQ-Action-Scope: ResourceOnly` is sent, see [Invalidating Cached Pages from AEM](page-invalidate.md) for details)

This can be used to cover a number of different use cases, such as invalidating other application specific caches, or to handle cases where the externalized URL of a page and its place in the docroot does not match the content path.

Below example script logs each invalidate request to a file.

```xml
/invalidateHandler "/opt/dispatcher/scripts/invalidate.sh"
```

#### sample invalidation handler script {#sample-invalidation-handler-script}

```shell
#!/bin/bash

printf "%-15s: %s %s" $1 $2 $3>> /opt/dispatcher/logs/invalidate.log
```

### Limiting the Clients That Can Flush the Cache {#limiting-the-clients-that-can-flush-the-cache}

The /allowedClients property defines specific clients that are allowed to flush the cache. The globbing patterns are matched against the IP.

The following example:

1. denies access to any client
1. explicitly allows access to the localhost

```xml
/allowedClients
  {
   /0001 { /glob "*.*.*.*"  /type "deny" }
   /0002 { /glob "127.0.0.1" /type "allow" }
  }
```

For information about glob properties, see [Designing Patterns for glob Properties](#designing-patterns-for-glob-properties).

>[!CAUTION]
>
>It is recommended that you define the /allowedClients.
>
>If this is not done, any client can issue a call to clear the cache; if this is done repeatedly it can severely impact the site performance.

### Ignoring URL Parameters {#ignoring-url-parameters}

The `ignoreUrlParams` section defines which URL parameters are ignored when determining whether a page is cached or delivered from cache:

* When a request URL contains parameters that are all ignored, the page is cached.
* When a request URL contains one or more parameters that are not ignored, the page is not cached.

When a parameter is ignored for a page, the page is cached the first time that the page is requested. Subsequent requests for the page are served the cached page, regardless of the value of the parameter in the request.

To specify which parameters are ignored, add glob rules to the `ignoreUrlParams` property:

* To ignore a parameter, create a glob property that allows the parameter. 
* To prevent the page to be cached, create a glob property that denies the parameter.

The following example causes Dispatcher to ignores the "q" parameter, so that request URLs that include the q parameter are cached:

```xml
/ignoreUrlParams
{
    /0001 { /glob "*" /type "deny" }
    /0002 { /glob "q" /type "allow" }
}
```

Using the example `ignoreUrlParams` value, the following HTTP request causes the page to be cached because the `q` parameter is ignored:

```xml
GET /mypage.html?q=5
```

Using the example `ignoreUrlParams` value, the following HTTP request causes the page to **not** be cached because the `p` parameter is not ignored:

```xml
GET /mypage.html?q=5&p=4

```

For information about glob properties, see [Designing Patterns for glob Properties](#designing-patterns-for-glob-properties).

### Caching HTTP Response Headers {#caching-http-response-headers}

>[!NOTE]
>
>This feature is avaiable with version **4.1.11** of the Dispatcher.

The `/headers` property allows you to define the HTTP header types that are going to be cached by the Dispatcher. On the first request to an uncached resource, all headers matching one of the configured values (see the configuration sample below) are stored in a separate file, next to the cache file. On subsequent requests to the cached resource, the stored headers are added to the response.

Presented below is a sample from the default configuration:

```xml
/cache {
  ...
  /headers {
    "Cache-Control"
    "Content-Disposition"
    "Content-Type"
    "Expires"
    "Last-Modified"
    "X-Content-Type-Options"
    "Last-Modified"
  }
}

```

>[!NOTE]
>
>Also, be aware that file globbing characters are not allowed. For more details, see [Designing Patterns for glob Properties](#designing-patterns-for-glob-properties).

>[!NOTE]
>
>If you need Dispatcher to store and deliver ETag response headers from AEM, do the following:
>
>* Add the header name in the `/cache/headers`section.
>* Add the following [Apache directive](https://httpd.apache.org/docs/2.4/mod/core.html#fileetag) in the Dispatcher related section:
>

```xml
FileETag none
```

### Dispatcher Cache File Permissions {#dispatcher-cache-file-permissions}

The `mode` property specifies what file permissions are applied to new directories and files in the cache. This setting is restricted by the `umask` of the calling process. It is an octal number constructed from the sum of one or more of the following values:

* 0400 Allow read by owner.
* 0200 Allow write by owner.
* 0100 Allow the owner to search in directories.
* 0040 Allow read by group members.
* 0020 Allow write by group members.
* 0010 Allow group members to search in the directory.
* 0004 Allow read by others.
* 0002 Allow write by others.
* 0001 Allow others to search in the directory.

The default value is 0755 which allows the owner to read, write or search and the group and others to read or search.

### Throttling .stat file touching {#throttling-stat-file-touching}

With the default `/invalidate` property, every activation effectively invalidates all `.html` files (when their path matches the `/invalidate` section). On a website with considerable traffic, multiple, subsequent activations will increase the cpu load on the backend. In such a scenario, it would be desirable to "throttle" `.stat` file touching to keep the website responsive. You can do this by using the `/gracePeriod` property.

The `/gracePeriod` property defines the number of seconds a stale, auto-invalidated resource may still be served from the cache after the last occuring activation. The property can be used in a setup where a batch of activations would otherwise repeatedly invalidate the entire cache. The recommended value is 2 seconds.

For additional details, also read the `/invalidate` and `/statfileslevel`sections above.

## Configuring Time Based Cache Invalidation - /enableTTL {#configuring-time-based-cache-invalidation-enablettl}

If set, the `enableTTL` property will evaluate the response headers from the backend, and if they contain a `Cache-Control` max-age or `Expires` date, an auxiliary, empty file next to the cache file is created, with modification time equal to the expiry date. When the cached file is requested past the modification time it is automatically re-requested from the backend.

You can enable the feature by adding this line to the `dispatcher.any` file:

```xml
/enableTTL "1"
```

>[!NOTE]
>
>This feature is avaiable with version **4.1.11** of the Dispatcher.

## Configuring Load Balancing - /statistics {#configuring-load-balancing-statistics}

The `/statistics` section defines categories of files for which Dispatcher scores the responsiveness of each render. Dispatcher uses the scores to determine which render to send a request.

Each category that you create defines a glob pattern. Dispatcher compares the URI of the requested content to these patterns to determine the category of the requested content:

* The order of the categories determines the order in which they are compared to the URI.
* The first category pattern that matches the URI is the category of the file. No more category patterns are evaluated.

Dispatcher supports a maximum of 8 statistics categories. If you define more than 8 categories, only the first 8 are used.

**Render Selection**

Each time Dispatcher requires a rendered page, it uses the following algorithm to select the render:

1. If the request contains the render name in a `renderid` cookie, Dispatcher uses that render.
1. If the request includes no `renderid` cookie, Dispatcher compares the render statistics:

    1. Dispatcher determines the cateogry of the request URI.
    1. Dispatcher determines which render has the lowest response score for that category, and selects that render.

1. If no render is selected yet, use the first render in the list.

The score for a render's category is based on previous response times, as well as previous failed and successful connections that Dispatcher attempts. For each attempt, the score for the category of the requested URI is updated.

>[!NOTE]
>
>If you do not use load balancing, you can omit this section.

### Defining Statistics Categories {#defining-statistics-categories}

Define a category for each type of document for which you want to keep statistics for render selection. The /statistics section contains a /categories section. To define a category, add a line below the /categories section that has the following format:

`/name { /glob "pattern"}`

The category `name` must be unique to the farm. The `pattern` is described in the [Designing Patterns for glob Properties](#designing-patterns-for-glob-properties) section.

To determine the category of a URI, Dispatcher compares the URI with each category pattern until a match is found. Dispatcher begins with the first category in the list and cointinues in order. Therefore, place categories with more specific patterns first.

For example, Dispatcher the default dispatcher.any file defines an HTML category and an others category. The HTML category is more specific and so it appears first:

```xml
/statistics
  {
  /categories
    {
      /html { /glob "*.html" }
      /others  { /glob "*" }
    }
  }
```

The following example also includes a category for search pages:

```xml
/statistics
  {
  /categories
    {
      /search { /glob "*search.html" }
      /html { /glob "*.html" }
      /others  { /glob "*" }
    }
  }
```

### Reflecting Server Unavailability in Dispatcher Statistics {#reflecting-server-unavailability-in-dispatcher-statistics}

The `/unavailablePenalty` property sets the time (in tenths of a second) that is applied to the render statistics when a connection to the render fails. Dispatcher adds the time to the statistics category that matches the requested URI.

For example, the penalty is applied when the TCP/IP connection to the designated hostname/port cannot be established, either because AEM is not running (and not listening) or because of a network-related problem.

The `/unavailablePenalty` property is a direct child of the `/farm` section (a sibling of the `/statistics` section).

If no `/unavailablePenalty` property exists, a value of "1" is used.

```xml
/unavailablePenalty "1"
```

## Identifying a Sticky Connection Folder - /stickyConnectionsFor {#identifying-a-sticky-connection-folder-stickyconnectionsfor}

The `/stickyConnectionsFor` property defines one folder that contains sticky documents; this will be accessed using the URL. Dispatcher sends all requests, from a single user, that are in this folder to the same render instance. Sticky connections ensure that session data is present and consistent for all documents. This mechanism uses the `renderid` cookie.

The following example defines a sticky connection to the /products folder:

```xml
/stickyConnectionsFor "/products"
```

When a page is composed of content from several content nodes, include the `/paths` property that lists the paths to the content. For example, a page contains content from `/content/image`, `/content/video`, and `/var/files/pdfs`. The following configuration enables sticky connections for all content on the page:

```xml
/stickyConnections {
  /paths {
    "/content/image"
    "/content/video"
    "/var/files/pdfs"
  }
}
```

### httpOnly {#httponly}

When sticky connections are enabled, the dispatcher module sets the `renderid` cookie. This cookie doesn't have the `httponly` flag, which should be added in order to enhance security. You can do this by setting the `httpOnly` property in the `/stickyConnections` node of a `dispatcher.any` configuration file. The property's value (either 0 or 1) defines whether the `renderid` cookie has the `HttpOnly` attribute appended. The default value is 0, which means the attribute will not be added.

For additional information about the `httponly` flag, read [this page](https://www.owasp.org/index.php/HttpOnly).

### secure {#secure}

When sticky connections are enabled, the dispatcher module sets the `renderid` cookie. This cookie doesn't have the **secure** flag, which should be added in order to enhance security. You can do this by setting the `secure` property in the `/stickyConnections` node of a `dispatcher.any` configuration file. The property's value (either 0 or 1) defines whether the `renderid` cookie has the `secure` attribute appended. The default value is 0, which means the attribute will be added if* *the incoming request is secure. If the value is set to 1 then the secure flag will be added regardless of whether the incoming request is secure or not.

## Handling Render Connection Errors {#handling-render-connection-errors}

Configure Dispatcher behavior when the render server returns a 500 error, or is unavailable.

### Specifying a Health Check Page {#specifying-a-health-check-page}

Use the `/health_check` property to specify a URL that is checked when a 500 status code occurs. If this page also returns a 500 status code the instance is considered to be unavailable and a configurable time penalty ( `/unavailablePenalty`) is applied to the render before retrying.

```xml
/health_check
  {
  # Page gets contacted when an instance returns a 500
  /url "/health_check.html"
  }

```

### Specifying the Page Retry Delay {#specifying-the-page-retry-delay}

The / `retryDelay` property sets the time (in seconds) that Dispatcher waits between rounds of connection attempts with the farm renders. For each round, the maximum number of times Dispatcher attempts a connection to a render is the number of renders in the farm.

Dispatcher uses a value of `"1"` if `/retryDelay` is not explicitly defined. The default value is appropriate in most cases.

```xml
/retryDelay "1"
```

### Configuring the Number of Retries {#configuring-the-number-of-retries}

The `/numberOfRetries` property sets the maximum number of rounds of connection attempts that Dispatcher performs with the renders. If Dispatcher cannot successfully connect to a render after this number of retries, Dispatcher returns a failed response.

For each round, the maximum number of times Dispatcher attempts a connection to a render is the number of renders in the farm. Therefore, the maximum number of times that Dispatcher attempts a connection is ( `/numberOfRetries`) x (the number of renders).

If the value is not explicitly defined, the default value is **5**.

```xml
/numberOfRetries "5"
```

### Using the Failover Mechanism {#using-the-failover-mechanism}

Enable the failover mechanism on your Dispatcher farm to resend requests to different renders when the original request fails. When failover is enabled, Dispatcher has the following behavior:

* When a request to a render returns HTTP status 503 (UNAVAILABLE), Dispatcher sends the request to a different render.
* When a request to a render returns HTTP status 50x (other than 503), Dispatcher sends a request for the page that is configured for the `health_check` property.

    * If the health check returns 500 (INTERNAL_SERVER_ERROR), Dispatcher sends the original request to a different render.
    * If the healtch check returns HTTP status 200, Dispatcher returns the initial HTTP 500 error to the client.

To enable failover, add the following line to the farm (or website):  

```xml
/failover "1" 
```

>[!NOTE]
>
>To retry HTTP requests that contain a body, Dispatcher sends a `Expect: 100-continue` request header to the render before spooling the actual contents. CQ 5.5 with CQSE then immediately answers with either 100 (CONTINUE) or an error code. Other servlet containers should support this as well.

## Ignoring Interruption Errors - /ignoreEINTR {#ignoring-interruption-errors-ignoreeintr}

>[!CAUTION]
>
>This option is not usually needed. You only need to use this when you see the following log messages:
>
>`Error while reading response: Interrupted system call`

Any file system oriented system call can be interrupted `EINTR` if the object of the system call is located on a remote system accessed via NFS. Whether these system calls can time out or be interrupted is based on how the underlying file system was mounted on the local machine.

Use the /ignoreEINTR parameter if your instance has such a configuration and the log contains the following message:

`Error while reading response: Interrupted system call`

Internally, Dispatcher reads the response from the remote server (i.e. AEM) using a loop that can be represented as:

`while (response not finished) {  
read more data  
}`

Such messages can be generated when the `EINTR` occurs in the " `read more data`" section and are caused by the reception of a signal before any data was received.

To ignore such interrupts you can add the following parameter to `dispatcher.any` (before `/farms`):

`/ignoreEINTR "1"`

Setting `/ignoreEINTR` to `"1"` causes Dispatcher to continue to attempt to read data until the complete response is read. The default value is 0 and deactivates the option.

## Designing Patterns for glob Properties {#designing-patterns-for-glob-properties}

Several sections in the Dispatcher configuration file use `glob` properties as selection criteria for client requests. The values of glob properties are patterns that Dispatcher compares to an aspect of the request, such as the path of the requested resource, or the IP address of the client. For example, the items in the `/filter` section use glob patterns to identify the paths of the pages that Dispatcher acts on or rejects.

The glob values can include wildcard characters and alphanumeric characters to define the pattern.

|Wildcard character|Description|Examples|
|--- |--- |--- |
|`*`|Matches zero or more contiguous instances of any character in the string. The final character of the match is determined by either of the following situations: <br/>A character in the string matches the next character in the pattern, and the pattern character has the following characteristics:<br/><ul><li>Not a *</li><li>Not a ?</li><li>A literal character (including a space) or a character class.</li><li>The end of the pattern is reached.</li></ul>Inside a character class, the character is interpreted literally.|`*/geo*` Matches any page below the `/content/geometrixx` node and the `/content/geometrixx-outdoors` node. The following HTTP requests match the glob pattern: <br/><ul><li>`"GET /content/geometrixx/en.html"`</li><li>`"GET /content/geometrixx-outdoors/en.html"` </li></ul><br/> `*outdoors/*` <br/>Matches any page below the `/content/geometrixx-outdoors` node. For example, the following HTTP request matches the glob pattern: <br/><ul><li>`"GET /content/geometrixx-outdoors/en.html"`</li></ul>|
|`?`|Matches any single character. Use outside character classes. Inside a character class, this character is interpreted literally.|`*outdoors/??/*`<br/> Matches the pages for any language in the geometrixx-outdoors site. For example, the following HTTP request matches the glob pattern: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>The following request does not match the glob pattern: <br/><ul><li>"GET /content/geometrixx-outdoors/en.html"</li></ul>|
|`[ and ]`|Demarks the beginning and end of a character class. Character classes can include one or more character ranges and single characters.<br/>A match occurs if the target character matches any of the characters in the character class, or within a defined range.<br/>If the closing bracket is not included, the pattern produces no matches.|`*[o]men.html*`<br/> Matches the following HTTP request:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>Does not match the following HTTP request:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/> `*[o/]men.html*` <br/>Matches the following HTTP requests: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul>|
|`-`|Denotes a range of characters. For use in character classes.  Outside of a character class, this character is interpreted literally.|`*[m-p]men.html*` Matches the following HTTP request: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul>Does not match the following HTTP request:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul>|
|`!`|Negates the character or character class that follows. Use only for negating characters and character ranges inside character classes. Equivalent to the `^ wildcard`. <br/>Outside of a character class, this character is interpreted literally.|`*[!o]men.html*`<br/> Matches the following HTTP request: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/men.html"`</li></ul><br/>Does not match the following HTTP request: <br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"`</li></ul><br/>`*[!o!/]men.html*`<br/> Does not match the following HTTP request:<br/><ul><li>`"GET /content/geometrixx-outdoors/en/women.html"` or `"GET /content/geometrixx-outdoors/en/men. html"`</li></ul>|
|`^`|Negates the character or character range that follows. Use for negating only characters and character ranges inside character classes. Equivalent to the `!` wildcard character. <br/>Outside of a character class, this character is interpreted literally.|The examples for the `!` wildcard character apply, substituting the `!` characters in the example patterns with `^` characters.|


<!--- need to troubleshoot table

The following table describes the wildcard characters.

<table border="1" cellpadding="1" cellspacing="0" width="100%"> 
 <tbody> 
  <tr> 
   <th>Wildcard character</th> 
   <th>Description</th> 
   <th>Examples</th> 
  </tr> 
  <tr> 
   <td>*</td> 
   <td><p>Matches zero or more contiguous instances of any character in the string. The final character of the match is determined by either of the following situations:</p> 
    <ul> 
     <li>A character in the string matches the next character in the pattern, and the pattern character has the following characteristics: 
      <ul> 
       <li>Not a *</li> 
       <li>Not a ?</li> 
       <li>A literal character (including a space) or a character class.</li> 
      </ul> </li> 
     <li>The end of the pattern is reached.</li> 
    </ul> <p>Inside a character class, the character is interpreted literally.</p> </td> 
   <td><p>*/geo*</p> <p>Matches any page below the /content/geometrixx node and the /content/geometrixx-outdoors node. The following HTTP requests match the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx/en.html"</li> 
     <li>"GET /content/geometrixx-outdoors/en.html" </li> 
    </ul> <p>*outdoors/*</p> <p>Matches any page below the /content/geometrixx-outdoors node. For example, the following HTTP request matches the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en.html"</li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>?</td> 
   <td><p>Matches any single character. Use outside character classes.</p> <p>Inside a character class, this character is interpreted literally.</p> </td> 
   <td><p>*outdoors/??/*</p> <p>Matches the pages for any language in the geometrixx-outdoors site. For example, the following HTTP request matches the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p>The following request does not match the glob pattern:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en.html" </li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>[ and ]</td> 
   <td><p>Demarks the beginning and end of a character class.</p> <p>Character classes can include one or more character ranges and single characters.</p> <p>A match occurs if the target character matches any of the characters in the character class, or within a defined range.</p> <p>If the closing bracket is not included, the pattern produces no matches.</p> <p></p> <p></p> <p></p> </td> 
   <td><p>*[o]men.html*</p> <p>Matches the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html"</li> 
    </ul> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html" </li> 
    </ul> <p>*[o/]men.html*</p> <p>Matches the following HTTP requests: </p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" </li> 
     <li> "GET /content/geometrixx-outdoors/en/men.html" </li> 
    </ul> <p></p> </td> 
  </tr> 
  <tr> 
   <td>-</td> 
   <td><p>Denotes a range of characters. For use in character classes.<br /> </p> <p>Outside of a character class, this character is interpreted literally.</p> <p></p> </td> 
   <td><p>*[m-p]men.html*</p> <p>Matches the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html"</li> 
    </ul> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p></p> </td> 
  </tr> 
  <tr> 
   <td>!</td> 
   <td><p>Negates the character or character class that follows. Use only for negating characters and character ranges inside character classes. Equivalent to the ^ wildcard.</p> <p>Outside of a character class, this character is interpreted literally.</p> <p></p> </td> 
   <td><p>*[!o]men.html*</p> <p>Matches the following HTTP request: </p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/men.html"</li> 
    </ul> <p>Does not match the following HTTP request</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" </li> 
    </ul> <p>*[!o!/]men.html*</p> <p>Does not match the following HTTP request:</p> 
    <ul> 
     <li>"GET /content/geometrixx-outdoors/en/women.html" or "GET /content/geometrixx-outdoors/en/men. html" </li> 
    </ul> </td> 
  </tr> 
  <tr> 
   <td>^</td> 
   <td><p>Negates the character or character range that follows. Use for negating only characters and character ranges inside character classes. Equivalent to the ! wildcard character.</p> <p>Outside of a character class, this charcter is interpreted literally.</p> </td> 
   <td>The examples for the ! wildcard character apply, substituting the ! characters in the example patterns with ^ characters.</td> 
  </tr> 
 </tbody> 
</table>
-->

## Logging {#logging}

In the web server configuration, you can set:

* The location of the Dispatcher log file.  
* The log level.

Refer to the web server documentation and the readme file of your Dispatcher instance for more information.

**Apache Rotated / Piped Logs**

If using an **Apache** web server you can use the standard functionality for rotated and/or piped logs. For example, using piped logs:

`DispatcherLog "| /usr/apache/bin/rotatelogs logs/dispatcher.log%Y%m%d 604800"`

This will automatically rotate:

* the dispatcher log file; with a timestamp in the extension (logs/dispatcher.log%Y%m%d).  
* on a weekly basis (60 x 60 x 24 x 7 = 604800 seconds).

Please see the Apache web server documentation on Log Rotation and Piped Logs; for example [Apache 2.2](https://httpd.apache.org/docs/2.2/logs.html).

>[!NOTE]
>
>Upon installation the default log level is high (i.e. level 3 = Debug), so that the Dispatcher logs all errors and warnings. This is very useful in the initial stages.
>
>However, this requires additional resources, so when the Dispatcher is working smoothly *according to your requirements*, you can(should) lower the log level.

### Trace Logging {#trace-logging}

Amongst other enhancements for the Dispatcher, version 4.2.0 also introduces Trace Logging.

This is a higher level than Debug logging, showing additional information in the logs. It adds logging for:

* The values of the forwarded headers;
* The rule that is being applied for a certain action.

You can enable Trace Logging by setting the log level to `4` in your web server.

Below is an example of logs with tracing enabled:

```xml
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Host] = "localhost:8443"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[User-Agent] = "curl/7.43.0"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Accept] = "*/*"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Client-Cert] = "(null)"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Via] = "1.1 localhost:8443 (dispatcher)"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-For] = "::1"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL] = "on"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Cipher] = "DHE-RSA-AES256-SHA"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-SSL-Session-ID] = "ba931f5e4925c2dde572d766fdd436375e15a0fd24577b91f4a4d51232a934ae"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[X-Forwarded-Port] = "8443"
[Thu Mar 03 16:05:38 2016] [T] [17183] request.headers[Server-Agent] = "Communique-Dispatcher"
```

And an event logged when a file that matches a blocking rule is requested:

```xml
[Thu Mar 03 14:42:45 2016] [T] [11831] 'GET /content.infinity.json HTTP/1.1' was blocked because of /0082
```

## Confirming Basic Operation {#confirming-basic-operation}

To confirm basic operation and interaction of the web server, Dispatcher and AEM instance you can use the following steps:

1. Set the `loglevel` to `3`.

1. Start the web server; this also starts the Dispatcher.
1. Start the AEM instance.
1. Check the log and error files for your web server and the Dispatcher.  
   Depending on your web server you should see messages such as:  
   `[Thu May 30 05:16:36 2002] [notice] Apache/2.0.50 (Unix) configured`  
   and:  
   `[Fri Jan 19 17:22:16 2001] [I] [19096] Dispatcher initialized (build XXXX)`

1. Surf the website via the web server. Confirm that content is being shown as required.  
   For example, on a local installation where AEM runs on port `4502` and the web server on `80` access the Websites console using both:  
   ` https://localhost:4502/libs/wcm/core/content/siteadmin.html  
   https://localhost:80/libs/wcm/core/content/siteadmin.html  
   `The results should be identical. Confirm access to other pages with the same mechanism.

1. Check that the cache directory is being filled.
1. Activate a page to check that the cache is being flushed correctly.
1. If everything is operating correctly you can reduce the `loglevel` to `0`.

## Using Multiple Dispatchers {#using-multiple-dispatchers}

In complex setups, you may use multiple Dispatchers. For example, you may use:

* one Dispatcher to publish a website on the Intranet
* a second Dispatcher, under a different address and with different security settings, to publish the same content on the Internet.

In such a case, make sure that each request goes through only one Dispatcher. A Dispatcher does not handle requests that come from another Dispatcher. Therefore, make sure that both Dispatchers access the AEM website directly.

## Debugging {#debugging}

When adding the header `X-Dispatcher-Info` to a request, Dispatcher answers whether the target was cached, returned from cached or not cacheable at all. The response header `X-Cache-Info` contains this information in a readable form. You can use these response headers to debug issues involving responses cached by the Dispatcher.

This functionality is not enabled by default, so in order for the response header `X-Cache-Info` to be included, the farm must contain the following entry:

```xml
/info "1"
```

For example,

```xml
/farm
{
    /mywebsite
    {
        # Include X-Cache-Info response header if X-Dispatcher-Info is in request header
        /info "1"
    }
}
```

Also, the `X-Dispatcher-Info` header does not need a value, but if you use `curl` for testing you must supply a value in order to send the header, such as:

```xml
curl -v -H "X-Dispatcher-Info: true" https://localhost/content/we-retail/us/en.html

```

Below is a list containing the response headers that `X-Dispatcher-Info` will return:

* **cached**  
  The target file is contained in the cache and the dispatcher has determined that it is valid to deliver it.
* **caching**  
  The target file isn't contained in the cache and the dispatcher has determined that it is valid to cache the output and deliver it.
* **caching: stat file is more recent**
  The target file is contained in the cache, however, it is invalidated by a more recent stat file. The dispatcher will delete the target file, recreate it from the output and deliver it.
* **not cacheable: no document root** 
  The farm's configuration doesn't contain a document root (configuration element `cache.docroot`).
* **not cacheable: cache file path too long**  
  The target file - the concatenation of document root and URL file - exceeds the longest possible file name on the system.
* **not cacheable: temporary file path too long**  
  The temporary file name template exceeds the longest possible file name on the system. The dispatcher creates a temporary file first, before actually creating or overwriting the cached file. The temporary file name is the target file name with the characters `_YYYYXXXXXX` appended to it, where the `Y` and `X` will be replaced to create a unique name.
* **not cacheable: request URL has no extension**  
  The request URL has no extension, or there is a path following the file extension, for example: `/test.html/a/path`.
* **not cacheable: request wasn't a GET or HEAD** 
  The HTTP method is neither a GET nor a HEAD. The dispatcher assumes that the output will contain dynamic data that shouldn't be cached.
* **not cacheable: request contained a query string**  
  The request contained a query string. The dispatcher assumes that the output depends on the query string given and therefore doesn't cache.
* **not cacheable: session manager didn't authenticate**  
  The farm's cache is governed by a session manager (the configuration contains a `sessionmanagement` node) and the request didn't contain the appropriate authentication information.
* **not cacheable: request contains authorization**  
  The farm is not allowed to cache output ( `allowAuthorized 0`) and the request contains authentication information.
* **not cacheable: target is a directory**  
  The target file is a directory. This might point to some conceptual mistake, where a URL and some sub-URL both contain cacheable output, for example if a request to `/test.html/a/file.ext` comes first and contains cacheable output, the dispatcher will not be able to cache the output of a subsequent request to `/test.html`.
* **not cacheable: request URL has a trailing slash**  
  The request URL has a trailing slash.
* **not cacheable: request URL not in cache rules**  
  The farm's cache rules explicitly deny caching the output of some request URL.
* **not cacheable: authorization checker denied access**  
  The farm's authorization checker denied access to the cached file.
* **not cacheable: session not valid** 
  The farm's cache is governed by a session manager (configuration contains a `sessionmanagement` node) and the user's session is not or no longer valid.
* **not cacheable: response contains `no_cache `**
  The remote server returned a `Dispatcher: no_cache` header, forbidding the dispatcher to cache the output.
* **not cacheable: response content length is zero** 
  The content length of the response is zero; the dispatcher will not create a zero-length file.
