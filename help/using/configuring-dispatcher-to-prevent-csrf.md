---
title: Configuring Dispatcher to Prevent CSRF Attacks
seo-title: Configuring Dispatcher to Prevent CSRF Attacks
description: null
seo-description: Learn how to configure Dispatcher to prevent Cross-Site Request Forgery attacks.
uuid: 79421758-b36a-453e-94b7-dfd890136da5
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: 8fa0a282-25b7-4b5e-8e8f-b2969f62d1f4
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
index: y
internal: n
snippet: y
---

# Configuring Dispatcher to Prevent CSRF Attacks{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM provides a framework aimed at preventing Cross-Site Request Forgery attacks. In order to properly make use of this framework, you need to make the following changes to your dispatcher configuration:

>[!NOTE]
>
>Be sure to update the rule numbers in the examples below based on your existing configuration. Remember that dispatchers will use the last matching rule to grant an allow or deny, so place the rules near the bottom of your existing list.

1. In the /clientheaders section of your author-farm.any and publish-farm.any, add the following entry to the bottom of the list:  
   "CSRF-Token"
1. In the /filters section of your author-farm.any and publish-farm.any or publish-filters.any file, add the following line to allow requests for /libs/granite/csrf/token.json through the dispatcher.  
   /0999 { /type "allow" /glob "&#42; /libs/granite/csrf/token.json&#42;" }
1. Under the /cache /rules section of your publish-farm.any, add a rule to block the dispatcher from caching the token.json file. Typically authors bypass caching, so you should not need to add the rule into your author-farm.any.  
   /0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }

To validate that the configuration is working, watch the dispatcher.log in DEBUG mode to validate that the token.json file is not being cached and is not being blocked by filters. You should see messages similar to:  
... checking [/libs/granite/csrf/token.json]  
... request URL not in cache rules: /libs/granite/csrf/token.json  
... cache-action for [/libs/granite/csrf/token.json]: NONE

You can also validate that requests are succeeding in your apache access_log. Requests for /libs/granite/csrf/token.json should return an HTTP 200 status code.
