---
title: The Dispatcher Security Checklist
description: Learn about the Dispatcher Security Checklist that should be completed before going on production.
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
jcr-lastmodifiedby: remove-legacypath-6-1
index: y
internal: n
snippet: y
exl-id: 49009810-b5bf-41fd-b544-19dd0c06b013
---
# The Dispatcher Security Checklist{#the-dispatcher-security-checklist}

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-05T05:14:35.365-0400

<p>Food for thought listed on <a href="https://jira.corp.adobe.com/browse/DOC-5649">DOC-5649</a>. To be considered while proof-reading.</p> 
<p> </p>

 -->

Adobe recommends that you complete the following checklist before going on production.

>[!CAUTION]
>
>Complete the Security Checklist of your version of AEM before going live. See the corresponding [Adobe Experience Manager documentation](https://experienceleague.adobe.com/en/docs/experience-manager-65/content/security/security-checklist).

## Use the Latest Version of Dispatcher {#use-the-latest-version-of-dispatcher}

Install the latest available version that is available for your platform. Upgrade your Dispatcher instance to use the latest version to take advantage of product and security enhancements. See [Installing Dispatcher](dispatcher-install.md).

>[!NOTE]
>
>You can check the current version of your Dispatcher installation by looking at the Dispatcher log file. 
>
>`[Thu Apr 30 17:30:49 2015] [I] [23171(140735307338496)] Dispatcher initialized (build 4.1.9)`
>
>To find the log file, inspect the Dispatcher configuration in your `httpd.conf`.

## Restrict Clients that Can Flush Your Cache {#restrict-clients-that-can-flush-your-cache}

Adobe recommends that you [limit the clients that can flush your cache.](dispatcher-configuration.md#limiting-the-clients-that-can-flush-the-cache)

## Enable the HTTPS for transport layer security {#enable-https-for-transport-layer-security}

Adobe recommends enabling the HTTPS transport layer on both author and publishing instances.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:41:28.841-0400

<p>Recommended to have SSL termination, front end SSL.</p> 
<p>Question is do we want to have SSL communication between dispatcher and AEM instances (publish and/or author).</p> 
<p>We might want to have two items:</p> 
<ul> 
 <li>MUST HTTPS clients -&gt; dispatcher / load balancer</li> 
 <li>NICE load balancer -&gt; dispatcher<br /> </li> 
 <li>NICE dispatcher -&gt; instances if sensitive information such as credit cards / or infrastructure requirements such as DMZ</li> 
</ul>

 -->

## Restrict Access {#restrict-access}

When configuring the Dispatcher, restrict external access as much as possible. See [Example /filter Section](dispatcher-configuration.md#main-pars_184_1_title) in the Dispatcher documentation.

## Make Sure Access to Administrative URLs is Denied {#make-sure-access-to-administrative-urls-is-denied}

Make sure you use filters to block external access to any administrative URLs, such as the Web Console.

See [Testing Dispatcher Security](dispatcher-configuration.md#testing-dispatcher-security) for a list of URLs that must be blocked.

## Use Allowlists Instead Of Blocklists {#use-allowlists-instead-of-blocklists}

Allowlists are a better way of providing access control since inherently, they assume that all access requests should be denied unless they are explicitly part of the allowlist. This model provides more restrictive control over new requests that might not have been reviewed yet or considered during a certain configuration stage.

## Run Dispatcher with a Dedicated System User {#run-dispatcher-with-a-dedicated-system-user}

When configuring the Dispatcher, ensure that the web server is ran by a dedicated user with least privileges. It is recommended that you only grant write access to the Dispatcher cache folder.

Also, IIS users must configure their website as follows:

1. In the physical path setting for your web site, select **Connect as a specific user**.
1. Set the user.

## Prevent Denial of Service (DoS) Attacks {#prevent-denial-of-service-dos-attacks}

A denial of service (DoS) attack is an attempt to make a computer resource unavailable to its intended users.

At the Dispatcher level, there are two methods of configuring to prevent DoS attacks: [Filters](https://experienceleague.adobe.com/en/docs#/filter)

* Use the mod_rewrite module (for example, [Apache 2.4](https://httpd.apache.org/docs/2.4/mod/mod_rewrite.html)) to perform URL validations (if the URL pattern rules are not too complex).

* Prevent the Dispatcher from caching URLs with spurious extensions by using [filters](dispatcher-configuration.md#configuring-access-to-content-filter).  
  For example, change the caching rules to limit caching to the expected mime types, such as:

    * `.html`
    * `.jpg`
    * `.gif`
    * `.swf`
    * `.js`
    * `.doc`
    * `.pdf`
    * `.ppt`

  An example configuration file can be seen for [restricting external access](#restrict-access). It includes restrictions for mime types.

To enable full functionality on the publish instances, configure filters to prevent access to the following nodes:

* `/etc/`
* `/libs/`

Then, configure filters to allow access to the following node paths:

* `/etc/designs/*`
* `/etc/clientlibs/*`
* `/etc/segmentation.segment.js`
* `/libs/cq/personalization/components/clickstreamcloud/content/config.json`
* `/libs/wcm/stats/tracker.js`
* `/libs/cq/personalization/*` (JS, CSS, and JSON)
* `/libs/cq/security/userinfo.json` (CQ user information)
* `/libs/granite/security/currentuser.json` (**data must not be cached**)  

* `/libs/cq/i18n/*` (Internalization)

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-00AF43764F54BE740A490D44@AdobeID)
Last Modified Date: 2015-06-26T04:38:17.016-0400

<p>We need to highlight whether a path applies to all versions or specific ones.<br /> </p>

 -->

## Configure Dispatcher to prevent CSRF Attacks {#configure-dispatcher-to-prevent-csrf-attacks}

AEM provides a [framework](https://experienceleague.adobe.com/en/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions#verification-steps) aimed at preventing Cross-Site Request Forgery attacks. To make proper use of this framework, allowlist CSRF token support in the Dispatcher by doing the following:

1. Creating a filter to allow the `/libs/granite/csrf/token.json` path;
1. Add the `CSRF-Token` header to the `clientheaders` section of the Dispatcher configuration.

## Prevent Clickjacking {#prevent-clickjacking}

To prevent clickjacking, Adobe recommends that you configure your webserver to provide the `X-FRAME-OPTIONS` HTTP header set to `SAMEORIGIN`.  
  
For more information on clickjacking, see the [OWASP site](https://owasp.org/www-community/attacks/Clickjacking).

## Perform a Penetration Test {#perform-a-penetration-test}

Adobe strongly recommends performing a penetration test of your AEM infrastructure before going on production.

