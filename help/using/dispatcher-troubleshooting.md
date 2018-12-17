---
title: Troubleshooting Dispatcher Problems
seo-title: Troubleshooting AEM Dispatcher Problems
description: Learn to troubleshoot Dispatcher issues.
seo-description: Learn to troubleshoot AEM Dispatcher issues.
uuid: 9c109a48-d921-4b6e-9626-1158cebc41e7
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
discoiquuid: a612e745-f1e6-43de-b25a-9adcaadab5cf

---

# Troubleshooting Dispatcher Problems{#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Dispatcher versions are independent of AEM, however the Dispatcher documentation is embedded in the AEM documentation. Always use the Dispatcher documentation that is embedded in the documentation for the latest version of AEM.
>
>You may have been redirected to this page if you followed a link to the Dispatcher documentation that is embedded in the documentation for a previous version of AEM.

>[!NOTE]
>
>Please check the [Dispatcher Knowledge Base](http://helpx.adobe.com/cq/kb/index/dispatcher.html) or [Troubleshooting Dispatcher](http://helpx.adobe.com/adobe-cq/kb/troubleshooting-dispatcher-flushing-issues.html) for further information.

## Check the Basic Configuration {#check-the-basic-configuration}

As always the first steps are to check the basics:

* [Confirm Basic Operation](#ConfirmBasicOperation)
* Check all log files for your web server and dispatcher. If necessary increase the `loglevel` used for the dispatcher [logging](#Logging).

* [Check your configuration](#ConfiguringtheDispatcher):

    * Do you have multiple Dispatchers?

        * Have you determined which Dispatcher is handling the website / page you are investigating?

    * Have you implemented filters?

        * Are these impacting the matter you are investigating?

## IIS Diagnostic Tools {#iis-diagnostic-tools}

IIS provides various trace tools, dependent on the actual version:

* IIS 6 - IIS diagnostic tools can be downloaded and configured  
* IIS 7 - tracing is fully integrated

These can help you monitor activity.

## IIS and 404 Not Found {#iis-and-not-found}

When using IIS you might experience `404 Not Found` being returned in various scenarios. If so, see the following Knowledge Base articles.

* [IIS 6/7: POST method returns 404](http://helpx.adobe.com/dispatcher/kb/IIS6IsapiFilters.html)
* [IIS 6: Requests to URLs that contain the base path `/bin` return `404 Not Found`](http://helpx.adobe.com/dispatcher/kb/RequestsToBinDirectoryFailInIIS6.html)

You should also check that the dispatcher cache root and the IIS document root are set to the same directory.

## Problems Deleting Workflow Models {#problems-deleting-workflow-models}

**Symptoms**

Problems trying to delete workflow models when accessing an AEM author instance through the Dispatcher.

**Steps to reproduce:**

1. Log in to your author instance (confirm that requests are being routed through the dispatcher).
1. Create a new workflow; for example, with the Title set to workflowToDelete.
1. Confirm that the workflow was successfully created.
1. Select and right click on the workflow, then click **Delete**.  

1. Click **Yes** to confirm.
1. An error message box will appear showing:  
   " `ERROR 'Could not delete workflow model!!`".

**Resolution**

Add the following headers to the `/clientheaders` section of your `dispatcher.any` file:

* `x-http-method-override`
* `x-requested-with`

`{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}`

## Interference with mod_dir (Apache) {#interference-with-mod-dir-apache}

This describes how the dispatcher interacts with `mod_dir` inside the Apache webserver, as this can lead to various, potentially unexpected effects:

### Apache 1.3 {#apache}

In Apache 1.3 `mod_dir` handles every request where the URL maps to a directory in the file system.

It will either:

* redirect the request to an existing `index.html` file 
* generate a directory listing

When the dispatcher is enabled, it processes such requests by registering itself as a handler for the content type `httpd/unix-directory`.

### Apache 2.x {#apache-x}

In Apache 2.x things are different. A module can handle different stages of the request, such as URL fixup. `mod_dir` handles this stage by redirecting a request (when the URL maps to a directory) to the URL with a `/` appended.

Dispatcher does not intercept the `mod_dir` fixup, but completely handles the request to the redirected URL (i.e. with `/` appended). This might pose a problem if the remote server (e.g. AEM) handles requests to `/a_path` differently to requests to `/a_path/` (when `/a_path` maps to an existing directory).

If this happens you must either:

* disable `mod_dir` for the `Directory` or `Location` subtree handled by the dispatcher  

* use `DirectorySlash Off` to configure `mod_dir` not to append `/`
