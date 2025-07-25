---
title: Optimize a Website for Cache Performance
description: Learn how to design your web site to maximize the benefits of caching.
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
redirecttarget: https://helpx.adobe.com/experience-manager/6-4/sites/deploying/using/configuring-performance.html
index: y
internal: n
snippet: y
---

# Optimize a Website for cache performance {#optimizing-a-website-for-cache-performance}

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-10-25T04:13:34.919-0400

<p>This is a redirect to /experience-manager/6-2/sites/deploying/using/configuring-performance.html</p>

 -->

>[!NOTE]
>
>Dispatcher versions are independent of AEM. You may have been redirected to this page if you followed a link to the Dispatcher documentation. That link was embedded in the documentation for a previous version of AEM.

Dispatcher offers several built-in mechanisms that you can use to optimize performance. This section tells you how to design your web site to maximize the benefits of caching.

>[!NOTE]
>
>It may help you to remember that the Dispatcher stores the cache on a standard web server. This means that you:
>
>* can cache everything that you can store as a page and request using a URL
>* cannot store other things, such as HTTP headers, cookies, session data, and form data.
>
>In general, many caching strategies involve selecting good URLs and not relying on this additional data.

## Use consistent page encoding {#using-consistent-page-encoding}

HTTP request headers are not cached and so problems can occur if you store page-encoding information in the header. In this situation, when Dispatcher serves a page from the cache, the default encoding of the web server is used for the page. There are two ways to avoid this problem:

* If you use only one encoding, make sure that the encoding used on the web server is the same as the default encoding of the AEM website.
* To set the encoding, use a `<META>` tag in the HTML `head` section, as in the following example:

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## Avoid URL parameters {#avoid-url-parameters}

If possible, avoid URL parameters for pages that you want to cache. For example, if you have a picture gallery, the following URL is never cached (unless the Dispatcher is [configured accordingly](dispatcher-configuration.md#main-pars_title_24)):

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

However, you can put these parameters into the page URL, as follows: 

```xml
www.myCompany.com/pictures/gallery.christmas.1.html

```

>[!NOTE]
>
>This URL calls the same page and the same template as gallery.html. In the template definition, you can specify which script renders the page, or you can use the same script for all pages.

## Customize by URL {#customize-by-url}

If you allow users to change the font size (or any other layout customization), make sure that the different customizations are reflected in the URL.

For example, cookies are not cached, so if you store the font size in a cookie (or similar mechanism), the font size is not preserved for the cached page. As a result, Dispatcher returns documents of any font size at random.

Including the font size in the URL as a selector avoids this problem:

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>For most layout aspects, it is also possible to use style sheets, or client-side scripts, or both. Either or both work well with caching.
>
>This method is also useful for a print version, where you can use a URL such as:
>
>`www.myCompany.com/news/main.print.html`
>
>Using the script globbing of the template definition, you can specify a separate script that renders the print pages.

## Invalidate image files used as titles {#invalidating-image-files-used-as-titles}

If you rendered page titles or other text as pictures, store the files so that they are deleted upon a content update on the page:

1. Place the image file in the same folder as the page.
1. Use the following naming format for the image file:  
  
   `<page file name>.<image file name>`

For example, you can store the title of the page myPage.html in the file myPage.title.gif. This file is automatically deleted if the page is updated, so any change to the page title is automatically reflected in the cache.

>[!NOTE]
>
>The image file does not necessarily exist on the AEM instance. You can use a script that dynamically creates the image file. Dispatcher then stores the file on the web server.

## Invalidate image files used for navigation {#invalidating-image-files-used-for-navigation}

If you use pictures for the navigation entries, the method is basically the same as with titles, just a bit more complex. Store all the navigation images with the target pages. If you use two pictures for normal and active, you can use the following scripts:

* A script that displays the page, as normal.
* A script that processes `.normal` requests and returns the normal picture.
* A script that processes `.active` requests and returns the activated picture.

It is important that you create these pictures with the same naming handle as the page to ensure that a content update deletes these pictures and the page.

For pages that are not modified, the pictures remain in the cache, although the pages themselves are auto-invalidated.

## Personalization {#personalization}

The Dispatcher cannot cache personalized data, so it is recommended that you limit personalization to where it is necessary. To illustrate why:

* If you use a freely customizable start page, that page has to be composed every time a user requests it.
* If, in contrast, you offer a choice of ten different start pages, you can cache each one of them, thus improving performance.

>[!NOTE]
>
>If you personalize each page (for example by putting the user's name into the title bar) you cannot cache it, which can cause a major performance impact.
>
>However, if you must, you can do the following:
>
>* use iFrames to split the page into one part that is the same for all users and one part that is the same for all pages of the user. You can then cache both of these parts.
>* use client-side JavaScript to display personalized information. However, you have to make sure that the page still displays correctly if a user turns JavaScript off.
>

## Sticky connections {#sticky-connections}

[Sticky connections](dispatcher.md#TheBenefitsofLoadBalancing) ensure that the documents for one user are all composed on the same server. If a user leaves this folder and later returns to it, the connection still sticks. Define one folder so it can hold all documents that require sticky connections for the website. Try not to have other documents in it. Doing so impacts load-balancing if you use personalized pages and session data.

## MIME types {#mime-types}

There are two ways in which a browser can determine the type of a file:

1. By its extension (for example, .html, .gif, and .jpg)
1. By the MIME-type that the server sends with the file.

For most files, the MIME-type is implied in the file extension:

1. By its extension (for example, .html, .gif, and .jpg)
1. By the MIME-type that the server sends with the file.

If the file name has no extension, it is displayed as plain text.

The MIME-type is part of the HTTP header, and as such, Dispatcher does not cache it. Your AEM application may return files that do not have a recognized file extension. If the files rely on the MIME-type instead, these files may be displayed incorrectly.

To make sure that files are cached properly, follow these guidelines:

* Make sure that files always have the proper extension.
* Avoid generic file serve scripts, which have URLs such as download.jsp?file=2214. Rewrite the script so that it uses URLs that contain the file specification. For the previous example, it would be `download.2214.pdf`.

