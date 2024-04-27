---
title: Configuring Dispatcher to Prevent CSRF Attacks
description: Learn how to configure AEM Dispatcher to prevent Cross-Site Request Forgery attacks.
topic-tags: dispatcher
content-type: reference
exl-id: bcd38878-f977-46a6-b01a-03e4d90aef01
---
# Configuring Dispatcher to Prevent CSRF Attacks{#configuring-dispatcher-to-prevent-csrf-attacks}

AEM provides a framework aimed at preventing Cross-Site Request Forgery attacks. To properly use this framework, make the following changes to your Dispatcher configuration:

>[!NOTE]
>
>Be sure to update the rule numbers in the examples below based on your existing configuration. Remember that dispatchers use the last matching rule to grant an allow or deny, so place the rules near the bottom of your existing list.

1. In the `/clientheaders` section of your `author-farm.any` and `publish-farm.any`, add the following entry to the bottom of the list:  
   `CSRF-Token`
1. In the /filters section of your `author-farm.any` and `publish-farm.any` or `publish-filters.any` file, add the following line to allow requests for `/libs/granite/csrf/token.json` through the Dispatcher.  
   `/0999 { /type "allow" /glob " * /libs/granite/csrf/token.json*" }`
1. Under the `/cache /rules` section of your `publish-farm.any`, add a rule to block the Dispatcher from caching the `token.json` file. Typically authors bypass caching, so there is no need to add the rule into your `author-farm.any`.  
   `/0999 { /glob "/libs/granite/csrf/token.json" /type "deny" }`

To validate that the configuration is working, watch the dispatcher.log in DEBUG mode to validate that the token.json file is not being cached and is not being blocked by filters. You should see messages similar to:  
`... checking [/libs/granite/csrf/token.json]  `
`... request URL not in cache rules: /libs/granite/csrf/token.json`  
`... cache-action for [/libs/granite/csrf/token.json]: NONE`

You can also validate that requests are succeeding in your Apache `access_log`. Requests for ``/libs/granite/csrf/token.json should return an HTTP 200 status code.
