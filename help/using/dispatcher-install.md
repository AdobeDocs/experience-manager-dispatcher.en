---
title: Install Dispatcher
description: Learn how to install the Dispatcher module on Microsoft&reg; Internet Information Server, Apache Web Server, and Sun Java &trade; Web Server-iPlanet.
contentOwner: User
converted: true
topic-tags: dispatcher
content-type: reference
exl-id: 9375d1c0-8d9e-46cb-9810-fa4162a8c1ba
---
# Install Dispatcher {#installing-dispatcher}

<!-- 

Comment Type: draft

<h2>Introduction</h2>

 -->

Use the [Dispatcher Release Notes](release-notes.md) page to obtain the latest Dispatcher installation file for your operating system and web server. Dispatcher release numbers are independent of the Adobe Experience Manager release numbers and are compatible with Adobe Experience Manager 6.x, 5.x and Adobe CQ 5.x releases.

>[!NOTE]
>
>Adobe Experience Manager 6.5 requires Dispatcher version 4.3.2 or higher. That said, Dispatcher versions are independent of AEM, for example, Dispatcher version 4.3.2 is also compatible with Adobe Experience Manager 6.4.

The following file naming convention is used:

`dispatcher-<web-server>-<operating-system>-<dispatcher-version-number>.<file-format>`

For example, the `dispatcher-apache2.4-linux-x86_64-ssl-4.3.1.tar.gz` file contains Dispatcher version 4.3.1 for an Apache 2.4 web server that runs on Linux&reg; i686 and is packaged using the **tar** format.

The following table lists the web server identifier that is used in file names for each web server:

|Web Server|Installation Kit|
|--- |--- |
|Apache 2.4| `dispatcher-apache**2.4**-<other parameters>`|
|Microsoft&reg; Internet Information Server 7.5, 8, 8.5, 10 | `dispatcher-**iis**-<other parameters>`|
|Sun Java&trade; Web Server iPlanet | `dispatcher-**ns**-<other parameters>`|

>[!CAUTION]
>
>Be sure you install the latest version of Dispatcher that is available for your platform. On a yearly basis, upgrade your Dispatcher instance to use the latest version to take advantage of product improvements.

>[!NOTE]
>
>Customers upgrading specifically from version 4.3.3 to version 4.3.4 should notice a different behavior in how caching headers are set for uncacheable content. To read more about this change, see the [Release Notes](/help/using/release-notes.md#nov) page.

Each archive contains the following files:

* the Dispatcher modules  
* an example configuration file
* the README file that contains installation instructions and last-minute information
* the CHANGES file that lists issues fixed in current and past releases

>[!NOTE]
>
>Check the README file for any last-minute changes / platform-specific notes before starting the installation.

<!-- 

Comment Type: draft

<h3>Supported Web Servers</h3>

 -->

<!-- 

Comment Type: draft

<p>The following web servers are supported for use with Dispatcher version 4.1.12:</p>

 -->

<!-- 

Comment Type: draft

<p>The following sections detail the specific Web server installation procedures.</p>

 -->

## Microsoft&reg; Internet Information Server {#microsoft-internet-information-server}

For information on how to install this Web server, see the following resources:

* Microsoft&reg;'s own documentation on the Internet Information Server
* ["The Official Microsoft&reg; IIS site"](https://www.iis.net/)

### Required IIS components {#required-iis-components}

IIS versions 8.5 and 10 require that the following IIS components are installed:

* ISAPI Extensions

Also, you must add the Web server (IIS) role. Use Server Manager to add the role and the components.  

## Microsoft&reg; IIS - Installing the Dispatcher module {#microsoft-iis-installing-the-dispatcher-module}

The required archive for the Microsoft&reg; Internet Information System is:

* `dispatcher-iis-<operating-system>-<dispatcher-release-number>.zip`

The ZIP file contains the following files:

|File|Description|
|--- |--- |
|`disp_iis.dll`|The Dispatcher dynamic link library file.|
|`disp_iis.ini`|Configuration file for the IIS. This example can be updated with your requirements. **Note**: The ini file must have the same name-root as the dll.|
|`dispatcher.any`|An example configuration file for the Dispatcher.|
|`author_dispatcher.any`|An example configuration file for Dispatcher working with the author instance.|
|README|Readme file that contains installation instructions and last-minute information. **Note**: Check this file before starting the installation.|
|CHANGES|Changes a file that lists issues fixed in current and past releases.|

Use the following procedure to copy the Dispatcher files to the correct location.

1. Use Windows Explorer to create the `<IIS_INSTALLDIR>/Scripts` directory, for example, `C:\inetpub\Scripts`.

1. Extract the following files from the Dispatcher package into this Scripts directory:

   * `disp_iis.dll`
   * `disp_iis.ini`
   * One of the following files depending on if the Dispatcher is working with an AEM author instance or publish instance:
     * Author instance: `author_dispatcher.any`
     * Publish instance: `dispatcher.any`

## Microsoft&reg; IIS - Configure the Dispatcher INI file {#microsoft-iis-configure-the-dispatcher-ini-file}

To configure the Dispatcher installation, edit the `disp_iis.ini` file. The basic format of the `.ini` file is as follows:

```xml

[main]
configpath=<path to dispatcher.any>
loglevel=1|2|3
servervariables=0|1
replaceauthorization=0|1

```

The following table describes each property.

|Parameter|Description|
|--- |--- |
|`configpath`|The location of `dispatcher.any` within the local file system (absolute path).|
|`logfile`|The location of the `dispatcher.log` file. If this location is not set, then log messages go to the Windows event log.|
|`loglevel`|Defines the log level used to output messages to the event log. The following values may be specified at the log level for the log file: <br/>0 - error messages only. <br/>1 - errors and warnings. <br/>2 - errors, warnings, and informational messages <br/>3 - errors, warnings, informational messages, and debug messages. <br/>**Note**: Set the log level to 3 during installation and testing, then to 0 when running in a production environment.|
|`replaceauthorization`|Specifies how authorization headers in the HTTP request are handled. The following values are valid:<br/>0 - Authorization headers are not modified. <br/>1 - Replaces any header named "Authorization" other than "Basic" with its `Basic <IIS:LOGON\_USER>` equivalent.<br/>|
|`servervariables`|Defines how server variables are processed.<br/>0 - IIS server variables are not sent to the Dispatcher or AEM. <br/>1 - all IIS server variables (such as `LOGON\_USER, QUERY\_STRING, ...`) are sent to the Dispatcher, together with the requested headers (and also to the AEM instance if not cached).  <br/>Server variables include `AUTH\_USER, LOGON\_USER, HTTPS\_KEYSIZE` and many others. See the IIS documentation for the full list of variables, with details.|
|`enable_chunked_transfer`|Defines whether to enable (1) or disable (0) chunk transfer for the client response. The default value is 0.|

An example configuration:

```xml

[main]
configpath=C:\Inetpub\Scripts\dispatcher.any
loglevel=1
servervariables=1
replaceauthorization=0

```

### Configure Microsoft&reg; IIS {#configuring-microsoft-iis}

Configure IIS to integrate the Dispatcher ISAPI module. In IIS, you use wildcard application mapping.

### Configure anonymous access - IIS 8.5 and 10 {#configuring-anonymous-access-iis-and}

The default `Flush` replication agent on the Author instance is configured so that it does not send security credentials with flush requests. Therefore, the website on which you use the Dispatcher cache must allow anonymous access.

If your website uses an authentication method, the `Flush` replication agent must be configured accordingly.

1. Open IIS Manager and select the website that you are using as the Dispatcher cache.
1. Using Features View mode, in the IIS section double-click Authentication.
1. If Anonymous Authentication is not enabled, select Anonymous Authentication and in the Actions area click Enable.

### Integrate the Dispatcher ISAPI module - IIS 8.5 and 10 {#integrating-the-dispatcher-isapi-module-iis-and}

Use the following procedure to add the Dispatcher ISAPI module to IIS.

1. Open IIS Manager.
1. Select the website that you are using as the Dispatcher Cache.
1. Using Features View mode, in the IIS section double-click Handler Mappings.
1. In the Actions panel of the Handler Mappings page, click Add Wildcard Script Map, add the following property values, and then click OK:

   * Request Path: &#42;
   * Executable: The absolute path of the disp_iis.dll file, for example `C:\inetpub\Scripts\disp_iis.dll`.
   * Name: A descriptive name for the handler mapping, for example `Dispatcher`.

1. In the dialog box that appears, to add the disp_iis.dll library to the ISAPI and CGI Restrictions list, click **Yes**.

   For IIS 7.0 and 7.5, the configuration is complete. Continue with the remaining steps if you are configuring IIS 8.0.

1. (IIS 8.0) In the list of Handler Mappings, select the one that you created, and in the Actions area click Edit.
1. (IIS 8.0) In the Edit Script Map dialog box, click the Request Restrictions button.
1. (IIS 8.0) To ensure that the handler is used for files and folders that are not yet cached, deselect **Invoke Handler Only If Request Is Mapped To**. Click **OK**.
1. (IIS 8.0) On the Edit Script Map dialog box, click OK.

### Configure access to the cache - IIS 8.5 and 10 {#configuring-access-to-the-cache-iis-and}

Provide the default App Pool user with write-access to the folder that is being used as the Dispatcher cache.

1. Right-click the root folder of the website that you are using as the Dispatcher cache and click Properties, such as `C:\inetpub\wwwroot`.
1. On the Security tab, click Edit, and then in the Permissions dialog box, click Add. A dialog box opens for selecting user accounts. Click the Locations button, select your computer name, and then click OK.

   Keep this dialog box open while you complete the next step.

1. in IIS Manager, select the IIS site that you are using for the Dispatcher cache, and on the right side of the window, click Advanced Settings.
1. Select the value of the Application Pool property and copy it to the clipboard.
1. Return to the open dialog box. In the Enter The Object Names To Select box, type `IIS AppPool\` and then paste the contents of your clipboard. The value should look like the following example:

   `IIS AppPool\DefaultAppPool`

1. Click the Check Names button. When Windows resolves the user account, click OK.
1. In the Permissions dialog box for the Dispatcher folder, select the account that you just added, enable all permissions for the account **except for Full Control** and click OK. Click OK so you can close the folder Properties dialog box.

### Register the JSON mime type - IIS 8.5 and 10 {#registering-the-json-mime-type-iis-and}

Use the following procedure to register the JSON MIME type, when you want the Dispatcher to allow JSON calls.

1. In IIS Manager, select your website and using Features View, double-click Mime Types.
1. If the JSON extension is not in the list, in the Actions panel click Add, enter the following property values, and then click OK:

   * File Name Extension: `.json`
   * MIME Type: `application/json`

### Remove the bin hidden segment - IIS 8.5 and 10 {#removing-the-bin-hidden-segment-iis-and}

Use the following procedure to remove the `bin` hidden segment. Web sites that are not new can contain this hidden segment.

1. In IIS Manager, select your website and using Features View, double-click Request Filtering.
1. Select the `bin` segment, click Remove, and in the confirmation dialog box click Yes.

### Log IIS messages to a file - IIS 8.5 and 10 {#logging-iis-messages-to-a-file-iis-and}

Use the following procedure to write Dispatcher log messages to a log file instead of to the Windows Event log. Configure the Dispatcher to use the log file, and provide IIS with write-access to the file.

1. Use Windows Explorer to create a folder named `dispatcher` below the logs folder of the IIS installation. The path of this folder for a typical installation is `C:\inetpub\logs\dispatcher`.

1. Right-click the Dispatcher folder and click **Properties**.
1. On the Security tab, click **Edit**.
1. In the Permissions dialog box, click **Add**. A dialog box opens for selecting user accounts. Click the Locations button, select your computer name, and then click OK.

   Keep this dialog box open while you complete the next step.

1. in IIS Manager, select the IIS site that you are using for the Dispatcher cache, and on the right side of the window, click Advanced Settings.
1. Select the value of the Application Pool property and copy it to the clipboard.
1. Return to the open dialog box. In the Enter The Object Names To Select box, type `IIS AppPool\` and then paste the contents of your clipboard. The value should look like the following example:

   `IIS AppPool\DefaultAppPool`

1. Click the Check Names button. When Windows resolves the user account, click OK.
1. In the Permissions dialog box for the Dispatcher folder, select the account that you just added, enable all permissions for the account **except for Full Control,** and click OK. Click OK so you can close the folder Properties dialog box. 
1. Use a text editor to open the `disp_iis.ini` file.
1. To configure the location of the log file, add a line of text similar to the following example, then save the file:

   ```xml

   logfile=C:\inetpub\logs\dispatcher\dispatcher.log

   ```

### Next steps {#next-steps}

Before you can start using the Dispatcher, you must know the following:

* [Configure](dispatcher-configuration.md) Dispatcher
* [Configure AEM](page-invalidate.md) to work with Dispatcher.

## Apache Web Server {#apache-web-server}

>[!CAUTION]
>
>Instructions for installation under both **Windows** and **UNIX&reg;** are covered here. Be careful when performing the steps.

### Install Apache Web Server {#installing-apache-web-server}

For Information about how to install an Apache Web Server read the installation manual - either [online](https://httpd.apache.org/) or in the distribution.

>[!CAUTION]
>
>If you are creating an Apache binary by compiling the source files, make sure that you turn on **`dynamic modules support`**. Enabling this option can be done using any of the **--enable-shared** options. At a minimum, include the `mod_so` module.
>
>More information can be found in the Apache Web Server installation manual.

Also see the Apache HTTP Server [Security Tips](https://httpd.apache.org/docs/2.4/misc/security_tips.html) and [Security Reports](https://httpd.apache.org/security_report.html).

### Apache Web Server - Add the Dispatcher module {#apache-web-server-add-the-dispatcher-module}

The Dispatcher comes as either:

* **Windows**: a Dynamic Link Library (DLL)
* **UNIX&reg;**: a Dynamic Shared Object (DSO)

The installation archive files contain the following files - dependent on whether you have selected Windows or UNIX&reg;:

|File|Description|
|--- |--- |
|d`isp_apache<x.y>.dll`|Windows: The Dispatcher dynamic link library file.|
|`dispatcher-apacheM<x.y>-<rel-nr>.so`|UNIX&reg;: The Dispatcher shared object library file.|
|`mod_dispatcher.so`|UNIX&reg;: An example link.|
|`http.conf.disp<x>`|An example configuration file for the Apache server.|
|`dispatcher.any`|An example configuration file for the Dispatcher.|
|`README`|Readme file that contains installation instructions and last-minute information. **Note**: Check this file before starting the installation.|
|C`HANGES`|Changes a file that lists issues fixed in the current and past releases.|

Use the following steps to add the Dispatcher to your Apache Web Server:

1. Place the Dispatcher file in the appropriate Apache module directory:

   * **Windows**: Place `disp_apache<x.y>.dll` `<APACHE_ROOT>/modules`
   * **UNIX&reg;**: Locate either the `<APACHE_ROOT>/libexec` or `<APACHE_ROOT>/modules`directory according to your installation.  
     Copy `dispatcher-apache<options>.so` into this directory.  
     To simplify long-term maintenance, you can also create a symbolic link named `mod_dispatcher.so` to the Dispatcher:  
     `ln -s dispatcher-apache<x>-<os>-<rel-nr>.so mod_dispatcher.so`

1. Copy the dispatcher.any file to the `<APACHE_ROOT>/conf` directory.

   **Note:** You can place this file in a different location, as long as the DispatcherLog property of the Dispatcher module is configured accordingly. (See Dispatcher-Specific Configuration Entries below.)

### Apache Web Server - Configure SELinux properties {#apache-web-server-configure-selinux-properties}

If you are running Dispatcher on Red Hat&reg; Linux&reg; Kernel 2.6 with SELinux enabled, you might run into error messages like this in the Dispatcher logfile.

`Mon Jun 30 00:03:59 2013] [E] [16561(139642697451488)] Unable to connect to backend rend01 (10.122.213.248:4502): Permission denied`

This error is likely due to an enabled SELinux security. If so, perform the following tasks:

* Configure the SELinux context of the Dispatcher module file.
* Enable HTTPD scripts and modules to make network connections.
* Configure the SELinux context of the docroot, where the cached files are stored.

Enter the following commands in a terminal window, replacing `[path to the dispatcher.so file]` with the path to the Dispatcher module that you installed to Apache Web Server, and *`path to the docroot`* with the path where the docroot is located (for example, `/opt/cq/cache`):

```shell

semanage fcontext -a -t httpd_modules_t [path to the dispatcher.so file]
setsebool -P httpd_can_network_connect on
chcon -R --type httpd_sys_rw_content_t [path to the docroot]
semanage fcontext -a -t httpd_sys_rw_content_t "[path to the docroot](/.*)?"

```

### Apache Web Server - Configure Apache Web Server for Dispatcher {#apache-web-server-configure-apache-web-server-for-dispatcher}

The Apache Web Server must be configured, using `httpd.conf`. In the Dispatcher installation kit, find an example configuration file named `httpd.conf.disp<x>`.

These steps are compulsory:

1. Navigate to `<APACHE_ROOT>/conf`.
1. Open `httpd.conf`for editing.  
1. The following configuration entries must be added, in the order listed:

   * **LoadModule** to load the module on startup.
   * Dispatcher-specific configuration entries, including **DispatcherConfig, DispatcherLog**, and **DispatcherLogLevel**.
   * **SetHandler** to activate the Dispatcher. **LoadModule**.
   * **ModMimeUsePathInfo** to configure the behavior of **mod_mime**.

1. (Optional) It is recommended that you change the owner of the htdocs directory:

   * The Apache server starts as root, though the child processes start as daemon (for security purposes). The DocumentRoot (`<APACHE_ROOT>/htdocs`) must belong to the user daemon:  

     ```xml

     cd <APACHE_ROOT>  
     chown -R daemon:daemon htdocs
    
     ```

**LoadModule**

The following table lists examples that can be used; the exact entries are according to your specific Apache Web Server:

|||
|--- |--- |
|Windows|`... LoadModule dispatcher_module modules\disp_apache.dll ...`|
|UNIX&reg; (assuming symbolic link)|`... LoadModule dispatcher_module libexec/mod_dispatcher.so ...`|

>[!NOTE]
>
>The first parameter of each statement must be written exactly as in the above examples.
>
>See the example configuration files provided and the Apache Web Server documentation for full details about this command.

**Dispatcher specific configuration entries**

The Dispatcher-specific configuration entries are placed after the LoadModule entry. The following table lists an example configuration that is applicable for both UNIX&reg; and Windows:

**Windows & UNIX&reg;**

```
...
<IfModule disp_apache2.c>
DispatcherConfig conf/dispatcher.any
DispatcherLog logs/dispatcher.log DispatcherLogLevel 3
DispatcherNoServerHeader 0 DispatcherDeclineRoot 0
DispatcherUseProcessedURL 0
DispatcherPassError 0
DispatcherKeepAliveTimeout 60
</IfModule>
...

```

>[!NOTE]
>
>Customers upgrading specifically from version 4.3.3 to version 4.3.4 should notice a different behavior in how caching headers are set for uncacheable content. To read more about this change, see the [Release Notes](/help/using/release-notes.md#nov) page.

The individual configuration parameters:

|Parameter|Description|
|--- |--- |
|DispatcherConfig|Location and name of the Dispatcher configuration file. <br/>When this property is in the main server configuration, all virtual hosts inherit the property value. However, virtual hosts can include a DispatcherConfig property to override the main server configuration.|
|DispatcherLog|Location and name of the log file.|
|DispatcherLogLevel|Log level for the log file: <br/>0 - Errors <br/>1 - Warnings <br/>2 - Infos <br/>3 - Debug <br/>**Note**: Set the log level to 3 during installation and testing, then to 0 when running in a production environment.|
|DispatcherNoServerHeader|*This parameter is deprecated and ineffective.*<br/><br/> Defines the Server Header to be used: <br/><ul><li>undefined or 0 - The HTTP server header contains the AEM version. </li><li>1 - The Apache server header is used.</li></ul>|
|DispatcherDeclineRoot|Defines whether to decline requests to the root "/": <br/>**0** - accept requests to / <br/>**1** - The Dispatcher does not handle requests to /. Instead, use mod_alias for the correct mapping.|
|DispatcherUseProcessedURL|Defines whether to use pre-processed URLs for all further processing by Dispatcher: <br/>**0** - use the original URL passed to the web server. <br/>**1** - the Dispatcher uses the URL already processed by the handlers that precede the Dispatcher (that is, `mod_rewrite`) instead of the original URL passed to the web server. For example, either the original or the processed URL is matched with Dispatcher filters. The URL is also used as the basis for the cache file structure. See the Apache website documentation for information about mod_rewrite; for example, Apache 2.4. When using mod_rewrite, use the flag 'passthrough' (pass through to the next handler) to force the rewrite engine to set the URI field of the internal request_rec structure to the value of the filename field.|
|DispatcherPassError|Defines how to support error codes for ErrorDocument handling: <br/>**0** - Dispatcher spools all error responses to the client. <br/>**1** - Dispatcher does not spool an error response to the client (where the status code is greater or equal than 400). Instead, it passes the status code to Apache, which allows an ErrorDocument directive to process such a status code. <br/>**Code Range** - Specify a range of error codes for which the response is passed to Apache. Other error codes are passed to the client. For example, the following configuration passes responses for error 412 to the client, and all other errors are passed to Apache: DispatcherPassError 400-411,413-417|
|DispatcherKeepAliveTimeout|Specifies the keep-alive timeout, in seconds. Starting with Dispatcher version 4.2.0 the default keep-alive value is 60. A value of 0 disables keep-alive.|
|DispatcherNoCanonURL|Setting this parameter to On passes the raw URL to the backend instead of the canonicalized one and overrides the settings of DispatcherUseProcessedURL. The default value is Off. <br/>**Note**: The filter rules in the Dispatcher configuration are always evaluated against the sanitized URL not the raw URL.|

>[!NOTE]
>
>Path entries are relative to the root directory of the Apache Web Server.

>[!NOTE]
>
>The default settings for the Server Header are:  
>
>`ServerTokens Full`
>
>`DispatcherNoServerHeader 0`  
>
>Which shows the AEM version, for statistical purposes. If you want to disable such information being available in the header, you can set the following:
>
>`ServerTokens Prod`  
>
>See the [Apache Documentation about ServerTokens Directive (for example, for Apache 2.4)](https://httpd.apache.org/docs/2.4/mod/core.html) for more information.

**SetHandler**

After these entries you must add a **SetHandler** statement to the context of your configuration ( `<Directory>`, `<Location>`) for the Dispatcher to handle the incoming requests. The following example configures the Dispatcher to handle requests for the complete website:

**Windows and UNIX&reg;** 

```
...  
<Directory />  
<IfModule disp_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

The following example configures the Dispatcher to handle requests for a virtual domain:

**Windows**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot _\[cache-path\]_\\docs  
<Directory _\[cache-path\]_\\docs>  
<IfModule disp_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

**UNIX&reg;**

```
...  
<VirtualHost 123.45.67.89>  
ServerName www.mycompany.com  
DocumentRoot /usr/apachecache/docs  
<Directory /usr/apachecache/docs>  
<IfModule disp_apache2.c>  
SetHandler dispatcher-handler  
</IfModule>  
AllowOverride None  
</Directory>  
</VirtualHost>  
...
```

>[!NOTE]
>
>The parameter of the **SetHandler** statement must be written *exactly the same as the above examples* because it is the name of the handler defined in the module.
>
>See the example configuration files provided and the Apache Web Server documentation for full details about this command.

**ModMimeUsePathInfo**

After the **SetHandler** statement, you should also add the **ModMimeUsePathInfo** definition.

>[!NOTE]
>
>Only use and configure the `ModMimeUsePathInfo` parameter if you are using Dispatcher version 4.0.9, or higher.
>
>Dispatcher version 4.0.9 has been released in 2011. If you are using an older version, upgrading to a recent Dispatcher version is appropriate.

The **ModMimeUsePathInfo** parameter should be set `On` for all Apache configurations:

`ModMimeUsePathInfo On`

The mod_mime module (for example, [Apache Module mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html)) is used to assign content metadata to the content selected for an HTTP response. The default setup means that `mod_mime` determines the content type. As such, only the part of the URL that maps to a file or directory is considered.

When `On`, the `ModMimeUsePathInfo` parameter specifies that `mod_mime` is to determine the content type based on the *complete* URL; this means that virtual resources have metainformation applied based on their extension.

The following example activates **ModMimeUsePathInfo**:

**Windows and UNIX&reg;**

```
...  
<Directory />  
<IfModule disp_apache2.c>  
SetHandler dispatcher-handler  
ModMimeUsePathInfo On  
</IfModule>  
  
Options FollowSymLinks  
AllowOverride None  
</Directory>  
...
```

### Enable support for HTTPS (UNIX&reg; and Linux&reg;) {#enable-support-for-https-unix-and-linux}

Dispatcher uses OpenSSL to implement secure communication over HTTP. Starting from Dispatcher version **4.2.0**, OpenSSL 1.0.0 and OpenSSL 1.0.1 are supported. Dispatcher uses OpenSSL 1.0.0 by default. To use OpenSSL 1.0.1, use the following procedure to create symbolic links so that the Dispatcher uses the OpenSSL libraries that are installed.

1. Open a terminal and change the current directory to the directory where the OpenSSL libraries are installed, for example:

   ```shell
   cd /usr/lib64
   ```

1. To create the symbolic links, enter the following commands:

   ```shell
   ln -s libssl.so libssl.so.1.0.1
   ln -s libcrypto.so libcrypto.so.1.0.1
   ```

>[!NOTE]
>
>If you are using a customized version of Apache, make sure Apache and Dispatcher are compiled using the same version of OpenSSL. <!-- URL has connection error [OpenSSL] (https://www.openssl.org/source/). -->

### Next steps {#next-steps-1}

Before you can start using the Dispatcher, you must now do the following:

* [Configure](dispatcher-configuration.md) Dispatcher
* [Configure AEM](page-invalidate.md) to work with Dispatcher.

## Sun Java&trade; System Web Server / iPlanet {#sun-java-system-web-server-iplanet}

>[!NOTE]
>
>Instructions for both Windows and UNIX&reg; environments are covered here. 
>
>Be careful when selecting which to run.

### Sun Java&trade; System Web Server / iPlanet - Installing your Web Server {#sun-java-system-web-server-iplanet-installing-your-web-server}

For full information on how to install these web servers, see their respective documentation:

* Sun Java&trade; System Web Server
* iPlanet Web Server

### Sun Java&trade; System Web Server / iPlanet - Add the Dispatcher module {#sun-java-system-web-server-iplanet-add-the-dispatcher-module}

The Dispatcher comes as either:

* **Windows**: a Dynamic Link Library (DLL)
* **UNIX&reg;**: a Dynamic Shared Object (DSO)

The installation archive files contain the following files - dependent on whether you have selected Windows or UNIX&reg;:

|File|Description|
|---|---|
|`disp_ns.dll`|Windows: The Dispatcher dynamic link library file.|
|`dispatcher.so`|UNIX&reg;: The Dispatcher shared object library file.|
|`dispatcher.so`|UNIX&reg;: An example link.|
|`obj.conf.disp`|An example configuration file for the iPlanet / Sun Java&trade; System web server.|
|`dispatcher.any`|An example configuration file for the Dispatcher.|
|README|Readme file that contains installation instructions and last-minute information. **Note:** Check this file before starting the installation.|
|CHANGES|Changes a file that lists issues fixed in the current and past releases.|

Use the following steps to add the Dispatcher to your web server:

1. Place the Dispatcher file in the web server's `plugin` directory:

### Sun Java&trade; System Web Server / iPlanet - Configure for the Dispatcher {#sun-java-system-web-server-iplanet-configure-for-the-dispatcher}

The web server must be configured using `obj.conf`. In the Dispatcher installation kit, find an example configuration file named `obj.conf.disp`.

1. Navigate to `<WEBSERVER_ROOT>/config`.
1. Open `obj.conf`for editing.
1. Copy the line that starts:  
   `Service fn="dispService"`  
   from `obj.conf.disp` to the initialization section of `obj.conf`.  

1. Save the changes.
1. Open `magnus.conf` for editing.
1. Copy the two lines that start:  
   `Init funcs="dispService, dispInit"`  
   and  
   `Init fn="dispInit"`  
   from `obj.conf.disp` to the initialization section of `magnus.conf`.

1. Save the changes.

>[!NOTE]
>
>The following configurations should all be on one line. Also, the `$(SERVER_ROOT)` and `$(PRODUCT_SUBDIR)` must be replaced with their respective values.

**Init**

The following table lists examples that can be used; the exact entries are according to your specific web server:

**Windows and UNIX&reg;**

```
...  
Init funcs="dispService,dispInit" fn="load-modules" shlib="$(SERVER\_ROOT)/plugins/dispatcher.so"  
Init fn="dispInit" config="$(PRODUCT\_SUBDIR)/dispatcher.any" loglevel="1" logfile="$(PRODUCT\_SUBDIR)/logs/dispatcher.log"  
keepalivetimeout="60"  
...
```

Where: 

|Parameter|Description|
|--- |--- |
|`config`|Location and name of the configuration file `dispatcher.any.`|
|`logfile`|Location and name of the log file.|
|`loglevel`|Log level for when writing messages to the log file: <br/>**0** Errors <br/>**1** Warning <br/>**2** Infos <br/>**3** Debug <br/>**Note:** Set the log level to 3 during installation and testing and to 0 when running in a production environment.|
|`keepalivetimeout`|Specifies the keep-alive timeout, in seconds. Starting with Dispatcher version 4.2.0 the default keep-alive value is 60. A value of 0 disables keep-alive.|

Depending on your requirements, you can define the Dispatcher as a service for your objects. To configure the Dispatcher for your entire website, edit the default object:

**Windows**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)\\dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*\\\*"  
...
```

**UNIX&reg;**

```
...  
NameTrans fn="document-root" root="$(PRODUCT\_SUBDIR)/dispcache"  
...  
Service fn="dispService" method="(GET|HEAD|POST)" type="\*/\*"  
...
```

### Next steps {#next-steps-2}

Before you can start using the Dispatcher, you must now:

* [Configure](dispatcher-configuration.md) Dispatcher
* [Configure AEM](page-invalidate.md) to work with Dispatcher.
