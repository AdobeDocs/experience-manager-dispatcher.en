---
title: Using SSL with Dispatcher
description: Learn how to configure the Dispatcher to communicate with AEM using SSL connections.
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
index: y
internal: n
snippet: y
exl-id: ec378409-ddb7-4917-981d-dbf2198aca98
---
# Using SSL with Dispatcher {#using-ssl-with-dispatcher}

Use SSL connections between the Dispatcher and the rendering computer:

* [One-way SSL](#use-ssl-when-dispatcher-connects-to-aem)
* [Mutual SSL](#configuring-mutual-ssl-between-dispatcher-and-aem)

>[!NOTE]
>
>Operations related to the SSL certificates are bound to third-party products. They are not covered by the Adobe Platinum Maintenance and Support contract.

## Use SSL When Dispatcher Connects to AEM {#use-ssl-when-dispatcher-connects-to-aem}

Configure the Dispatcher to communicate with the AEM or CQ render instance using SSL connections.

Before you configure Dispatcher, configure AEM or CQ to use SSL. For further information see:

* [SSL/TLS By Default ](https://experienceleague.adobe.com/en/docs/experience-manager-65/content/security/ssl-by-default) 
* [Use the SSL Wizard in AEM](https://experienceleague.adobe.com/en/docs/experience-manager-learn/foundation/security/use-the-ssl-wizard)

### SSL-Related Request Headers {#ssl-related-request-headers}

When Dispatcher receives an HTTPS request, Dispatcher includes the following headers in the subsequent request that it sends to AEM or CQ:

* `X-Forwarded-SSL`
* `X-Forwarded-SSL-Cipher`
* `X-Forwarded-SSL-Keysize`
* `X-Forwarded-SSL-Session-ID`

A request through Apache-2.4 with `mod_ssl` includes headers that are similar to the following example:

```shell
X-Forwarded-SSL: on
X-Forwarded-SSL-Cipher: DHE-RSA-AES256-SHA
X-Forwarded-SSL-Session-ID: 814825E8CD055B4C166C2EF6D75E1D0FE786FFB29DEB6DE1E239D5C771CB5B4D
```

### Configuring Dispatcher to Use SSL {#configuring-dispatcher-to-use-ssl}

To configure Dispatcher to connect with AEM or CQ over SSL, your [dispatcher.any](dispatcher-configuration.md) file requires the following properties:

* A virtual host that handles HTTPS requests.
* The `renders` section of the virtual host includes an item that identifies the host name and port of the CQ or AEM instance that uses HTTPS.
* The `renders` item includes a property named `secure` of value `1`.

Note: Create another virtual host for handling HTTP requests, if necessary.  

The following example `dispatcher.any` file shows the property values for connecting using HTTPS to a CQ instance that is running on host `localhost` and port `8443`:  

```
/farms
{
   /secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTPS requests
         "https://*"
      }
      /renders
      {
      /0001
         {
            # hostname or IP of the render
            /hostname "localhost"
            # port of the render
            /port "8443"
            # connect via HTTPS
            /secure "1"
         }
      }
     # the rest of the properties are omitted
   }

   /non-secure
   { 
      /virtualhosts
      {
         # select this farm for all incoming HTTP requests
         "http://*"
      }
      /renders
      {
         /0001
      {
         # hostname or IP of the render
         /hostname "localhost"
         # port of the render
         /port "4503"
      }
   }
    # the rest of the properties are omitted
}
```

## Configuring Mutual SSL Between Dispatcher and AEM {#configuring-mutual-ssl-between-dispatcher-and-aem}

To use Mutual SSL, configure the connections between Dispatcher and the render computer (typically an AEM or CQ publish instance):

* Dispatcher connects to the render instance over SSL.
* The render instance verifies the validity of the Dispatcher's certificate.
* Dispatcher verifies that the CA of the render instance's certificate is trusted.
* (Optional) Dispatcher verifies that the certificate of the render instance matches the render instance's server address.

To configure mutual SSL, you require certificates that are signed with a trusted Certificate Authority (CA). Self-signed certificates are not adequate. You can either act as the CA or use the services of a third-party CA to sign your certificates. To configure mutual SSL, you require the following items:

* Signed certificates for the render instance and Dispatcher
* The CA certificate (if you are acting as the CA)
* OpenSSL libraries for generating the CA, certificates, and certificate requests.

To configure mutual SSL, perform the following steps:

1. [Install](dispatcher-install.md) the latest version of Dispatcher for your platform. Use a Dispatcher binary that supports SSL (SSL is in the file name, such as `dispatcher-apache2.4-linux-x86-64-ssl10-4.1.7.tar`).
1. [Create or obtain a CA-signed certificate](dispatcher-ssl.md#main-pars-title-3) for the Dispatcher and the render instance. 
1. [Create a keystore containing the render certificate](dispatcher-ssl.md#main-pars-title-6) and configure the render's HTTP service.
1. [Configure the Dispatcher web server module](dispatcher-ssl.md#main-pars-title-4) for mutual SSL.

### Creating or Obtaining CA-Signed Certificates {#creating-or-obtaining-ca-signed-certificates}

Create or obtain the CA-signed certificates that authenticate the publishing instance and Dispatcher.

#### Creating Your CA {#creating-your-ca}

If you are acting as the CA, use [OpenSSL](https://www.openssl.org/) to create the Certificate Authority that signs the server and client certificates. (You must have the OpenSSL libraries installed.) If you are using a third-party CA, do not perform this procedure.

1. Open a terminal and change the current directory to the directory that contains the `CA.sh` file, such as `/usr/local/ssl/misc`.
1. To create the CA, enter the following command and then provide values when prompted:

   ```shell
   ./CA.sh -newca
   ```

   >[!NOTE]
   >
   >Several properties in the `openssl.cnf` file control the behavior of the CA.sh script. Edit this file as required before you create your CA.

#### Creating the Certificates {#creating-the-certificates}

Use OpenSSL to create the certificate requests to send to the third-party CA or to sign with your CA.

When you create a certificate, OpenSSL uses the Common Name property to identify the certificate holder. For the certificate of the render instance, use the instance computer's host name as the Common Name if you configure Dispatcher to accept the certificate. Do this procedure only if it matches the hostname of the Publishing instance. See the [DispatcherCheckPeerCN](dispatcher-ssl.md#main-pars-title-11) property.

1. Open a terminal and change the current directory to the directory that contains the CH.sh file of your OpenSSL libraries.
1. Enter the following command and provide values when prompted. If necessary, use the host name of the publishing instance as the Common Name. The host name is DNS-resolvable name for the IP address of the render:

   ```shell
   ./CA.sh -newreq
   ```

   If you are using a third-party CA, send the newreq.pem file to the CA to sign. If you are acting as the CA, continue to step 3. 

1. To sign the certificate using the certificate of your CA, enter the following command:

   ```shell
   ./CA.sh -sign
   ```

   Two files named `newcert.pem` and `newkey.pem` are created in the directory that contains your CA management files. These two files are the public certificate and private key for the rendering computer, respectively.

1. Rename `newcert.pem` to `rendercert.pem`, and rename `newkey.pem` to `renderkey.pem`.
1. Repeat steps 2 and 3 to create a certificate and a public key for the Dispatcher module. Ensure that you use a Common Name that is specific to the Dispatcher instance.
1. Rename `newcert.pem` to `dispcert.pem`, and rename `newkey.pem` to `dispkey.pem`.

### Configuring SSL on the Render Computer {#configuring-ssl-on-the-render-computer}

Configure SSL on the render instance using the `rendercert.pem` and `renderkey.pem` files.

#### Converting the Render Certificate to JKS (Java&trade; KeyStore) format {#converting-the-render-certificate-to-jks-format}

Use the following command to convert the render certificate, which is a PEM file, to a PKCS#12 file. Also include the certificate of the CA that signed the render certificate:

1. In a terminal window, change the current directory to the location of the render certificate and private key.
1. To convert the render certificate, which is a PEM file, to a PKCS#12 file, enter the following command. Also include the certificate of the CA that signed the render certificate:

   ```shell
   openssl pkcs12 -export -in rendercert.pem -inkey renderkey.pem  -certfile demoCA/cacert.pem -out rendercert.p12
   ```

1. To convert PKCS#12 file to Java&trade; KeyStore (JKS) format, enter the following command:

   ```shell
   keytool -importkeystore -srckeystore servercert.p12 -srcstoretype pkcs12 -destkeystore render.keystore
   ```

1. The Java&trade; Keystore is created using a default alias. Change the alias if desired:

   ```shell
   keytool -changealias -alias 1 -destalias jettyhttp -keystore render.keystore
   ```

#### Adding the CA Cert to the Render's Truststore {#adding-the-ca-cert-to-the-render-s-truststore}

If you are acting as the CA, import your CA certificate into a keystore. Then, configure the JVM that runs the render instance to trust the keystore.

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (sbroders@adobe.com)
Last Modified Date: 2014-08-12T13:11:21.401-0400

<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">The jetty http service has properties to specify trusted CA certificates for mutual SSL for 6.0. Whether they are operable is undetetermined. See https://issues.adobe.com/browse/DOC-4738.</p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;"> </p> 
<p style="font-family: tahoma, arial, helvetica, sans-serif; font-size: 12px;">For 5.6.1, you would specify the system property javax.net.ssl.trustStore, using the path to cacerts as value.</p>

 -->

1. Use a text editor to open the cacert.pem file and remove all the text that precedes the following line:

   `-----BEGIN CERTIFICATE-----`

1. Use the following command to import the certificate into a keystore:

   ```shell
   keytool -import -keystore cacerts.keystore -alias myca -storepass password -file cacert.pem
   ```

1. To configure the JVM that runs your render instance to trust the keystore, use the following system property:

   ```shell
   -Djavax.net.ssl.trustStore=<location of cacerts.keystore>
   ```

   For example, if you use the crx-quickstart/bin/quickstart script to start your publish instance you can modify the CQ_JVM_OPTS property:

   ```shell
   CQ_JVM_OPTS='-server -Xmx2048m -XX:MaxPermSize=512M -Djavax.net.ssl.trustStore=/usr/lib/cq6.0/publish/ssl/cacerts.keystore'
   ```

#### Configuring the Render Instance {#configuring-the-render-instance}

To configure the HTTP service of the render instance to use SSL, use the render certificate with the instructions in the *`Enable SSL on the Publish Instance`* section:

* AEM 6.2: [Enabling HTTP Over SSL](https://experienceleague.adobe.com/en/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)
* AEM 6.1: [Enabling HTTP Over SSL](https://experienceleague.adobe.com/en/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)
* Older AEM versions: see [this page.](https://experienceleague.adobe.com/en/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions)

### Configuring SSL for the Dispatcher Module {#configuring-ssl-for-the-dispatcher-module}

To configure Dispatcher to use mutual SSL, prepare the Dispatcher certificate and then configure the web server module.

### Creating a Unified Dispatcher Certificate {#creating-a-unified-dispatcher-certificate}

Combine the Dispatcher certificate and the unencrypted private key into a single PEM file. Use a text editor or the `cat` command to create a file that is similar to the following example:

1. Open a terminal and change the current directory to the location of the dispkey.pem file.
1. To decrypt the private key, enter the following command:

   ```shell
   openssl rsa -in dispkey.pem -out dispkey_unencrypted.pem
   ```

1. Use a text editor or the `cat` command to combine the unencrypted private key and the certificate in a single file that is similar to the following example:

   ```xml
   -----BEGIN RSA PRIVATE KEY-----
   MIICxjBABgkqhkiG9w0B...
   ...M2HWhDn5ywJsX
   -----END RSA PRIVATE KEY-----
   -----BEGIN CERTIFICATE-----
   MIIC3TCCAk...
   ...roZAs=
   -----END CERTIFICATE-----
   ```

### Specifying the Certificate to Use for Dispatcher {#specifying-the-certificate-to-use-for-dispatcher}

Add the following properties to the [Dispatcher module configuration](dispatcher-install.md#main-pars-55-35-1022) (in `httpd.conf`):

* `DispatcherCertificateFile`: The path to the Dispatcher unified certificate file, containing the public certificate and the unencrypted private key. This file is used when the SSL server requests the Dispatcher client certificate.
* `DispatcherCACertificateFile`: The path to the CA certificate file. Used if the SSL server presents a CA that a root authority does not trust.
* `DispatcherCheckPeerCN`: Whether to enable ( `On`) or disable ( `Off`) host name checking for remote server certificates.

The following code is an example configuration:  

```xml

<IfModule disp_apache2.c>
  DispatcherConfig conf/dispatcher.any
  DispatcherLog    logs/dispatcher.log
  DispatcherLogLevel 3
  DispatcherNoServerHeader 0
  DispatcherDeclineRoot 0
  DispatcherUseProcessedURL 0
  DispatcherPassError 0
  DispatcherCertificateFile disp_unified.pem
  DispatcherCACertificateFile cacert.pem
  DispatcherCheckPeerCN On
</IfModule>

```
