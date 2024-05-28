---
title: Troubleshooting Dispatcher Problems
description: Learn to troubleshoot Dispatcher issues.
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
exl-id: 29f338ab-5d25-48a4-9309-058e0cc94cff
---
# Troubleshooting Dispatcher Problems {#troubleshooting-dispatcher-problems}

>[!NOTE]
>
>Dispatcher versions are independent of AEM. However, the Dispatcher documentation is embedded in the AEM documentation. Always use the Dispatcher documentation that is embedded in the documentation for the latest version of AEM.
>
>You may have been redirected to this page if you followed a link to the Dispatcher documentation. That link is embedded in the documentation for a previous version of AEM.

>[!NOTE]
>
>Check the [Dispatcher Knowledge Base](https://helpx.adobe.com/experience-manager/kb/index/dispatcher.html), [Troubleshooting Dispatcher Flushing Issues](https://experienceleague.adobe.com/search.html?lang=en#q=troubleshooting%20dispatcher%20flushing%20issues&sort=relevancy&f:el_product=[Experience%20Manager]), and the [Dispatcher Top Issues FAQ](dispatcher-faq.md) for further information.

## Check the Basic Configuration {#check-the-basic-configuration}

As always, the first steps are to check the basics:

* [Confirm Basic Operation](/help/using/dispatcher-configuration.md#confirming-basic-operation)
* Check all log files for your web server and Dispatcher. If necessary, increase the `loglevel` used for the Dispatcher [logging](/help/using/dispatcher-configuration.md#logging).

* [Check your configuration](/help/using/dispatcher-configuration.md):

    * Do you have multiple Dispatchers?

        * Have you determined which Dispatcher is handling the website / page you are investigating?

    * Have you implemented filters?

        * Are these filters impacting the matter that you are investigating?

## IIS Diagnostic Tools {#iis-diagnostic-tools}

IIS provides various trace tools, dependent on the actual version:

* IIS 6 - IIS diagnostic tools can be downloaded and configured  
* IIS 7 - tracing is fully integrated

These tools can help you monitor activity.

## IIS and 404 Not Found {#iis-and-not-found}

When using IIS, you might experience `404 Not Found` being returned in various scenarios. If so, see the following Knowledge Base articles.

* [IIS 6/7: POST method returns 404](https://helpx.adobe.com/experience-manager/kb/IIS6IsapiFilters.html)
* [IIS 6: Requests to URLs that contain the base path `/bin` return a `404 Not Found`](https://helpx.adobe.com/experience-manager/kb/RequestsToBinDirectoryFailInIIS6.html)

Also check that the Dispatcher cache root and the IIS document root are set to the same directory.

## Problems Deleting Workflow Models {#problems-deleting-workflow-models}

**Symptoms**

Problems trying to delete workflow models when accessing an AEM author instance through the Dispatcher.

**Steps to reproduce:**

1. Log in to your author instance (confirm that requests are being routed through the Dispatcher).
1. Create a workflow; for example, with the Title set to workflowToDelete.
1. Confirm that the workflow was successfully created.
1. Select and right-click the workflow, then click **Delete**.  

1. Click **Yes** to confirm.
1. An error message box appears that shows the following:  
   `ERROR 'Could not delete workflow model!!`.

**Resolution**

Add the following headers to the `/clientheaders` section of your `dispatcher.any` file:

* `x-http-method-override`
* `x-requested-with`

```
{  
{  
/clientheaders  
{  
...  
"x-http-method-override"  
"x-requested-with"  
}
```

## Interference with mod_dir (Apache) {#interference-with-mod-dir-apache}

This process describes how the Dispatcher interacts with `mod_dir` inside the Apache webserver, as it can lead to various, potentially unexpected effects:

### Apache 1.3 {#apache}

In Apache 1.3, `mod_dir` handles every request where the URL maps to a directory in the file system.

It will either:

* redirect the request to an existing `index.html` file 
* generate a directory listing

When the Dispatcher is enabled, it processes such requests by registering itself as a handler for the content type `httpd/unix-directory`.

### Apache 2.x {#apache-x}

In Apache 2.x, things are different. A module can handle different stages of the request, such as URL fixup. The `mod_dir` handles this stage by redirecting a request (when the URL maps to a directory) to the URL with a `/` appended.

Dispatcher does not intercept the `mod_dir` fixup, but completely handles the request to the redirected URL (that is, with `/` appended). This process might pose a problem if the remote server (for example, AEM) handles requests to `/a_path` differently to requests to `/a_path/` (when `/a_path` maps to an existing directory).

If this situation happens, you must either:

* disable `mod_dir` for the `Directory` or `Location` subtree handled by the Dispatcher  

* use `DirectorySlash Off` to configure `mod_dir` not to append `/`
