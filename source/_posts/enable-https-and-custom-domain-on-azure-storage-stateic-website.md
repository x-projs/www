---
title: Enable HTTPS And Custom Domain On Azure Storage Static Website
date: 2020-07-09 18:51:33
tags:
    - azure
---

In the previous post, we introduced how to [host a webassembly application on azure storage](/2020/07/09/host-a-webassembly-application-on-azure-storage/).
In this post, we'll introduce how to enable HTTPS and custom domain for your application.

### 1. Eable HTTPS

Azure storage doesn't support HTTPS protocol. You need use Azure CDN to enable HTTPS. Login to azure portal, oepn your storage account, select `Azure CDN`
on the left panel, create a new CDN endpoint:

![](/images/enable-https-and-custom-domain-on-azure-storage-static-website-1.png)

Make sure `Origin hostname` is pointed to the static website.

After CDN endpoint is created, click it, you should see the `Endpoint hostname` on the right side. It should be an https url:

![](/images/enable-https-and-custom-domain-on-azure-storage-static-website-2.png)

Open the url in the browser, you should see your applicaion.

### 2. Redirect HTTP to HTTPS

HTTP is not secure. You can redirect all HTTP requests to HTTPS endpoint. Select `Rules engine` on the right side, click `Add rule` on the top,
Add a `Request Protocol` and `Url Redirect` like this:

![](/images/enable-https-and-custom-domain-on-azure-storage-static-website-3.png)

After saving, use http protocol to access your CDN endpoint, you should be redirect to https protocol.

### 3. Add custom domain

Click `Custom domains` on the right side, and click `+ Custom domain` on the top:

![](/images/enable-https-and-custom-domain-on-azure-storage-static-website-4.png)

You should be navigated to a page where you can add custom domain:

![](/images/enable-https-and-custom-domain-on-azure-storage-static-website-5.png)

Before adding your domain, you need go to your domain provider, add a `CNAME` record of your domain pointed to your CDN endpoint. Like in this example,
our domain is `www.devtest.pm`, our endpoint host name is `myappdevtest.azureedge.net`, so we add a `CNAME` record in your DNS like this:

![](/images/enable-https-and-custom-domain-on-azure-storage-static-website-6.png)

After domain added, click it, you can enable HTTPS for your custom domain:

![](/images/enable-https-and-custom-domain-on-azure-storage-static-website-7.png)

Select `Certificate management typ` as `CDN managed`, Azure will create a SSL certificate for your domain and will renew it automatically. All these
are free.

The whole process might take 5 to 10 minutes. When it is done, you should be able to access your website via your domain.

### 4. Enable custom root domain

Enabling root domain, like `xxxx.dom` instead of `www.xxx.dom`, is more complex. Because azure doesn't support issuing SSL certificate for root domain.
You need create a SSL certificate by yourself and upload the certificate to azure. I don't want to make this post too long. Let's leave this topic to
the next post.
