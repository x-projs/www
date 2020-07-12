---
title: Workaround For "Unable to decode certificate" Exception In X509Certificate2 Constructor
date: 2020-07-12 09:05:40
tags:
    - .net core
    - asp.net core
    - blazor
---

Today when I wrote some code in a blazor web assembly application, I found an interesting issue. The code looks like this:

    var key = "MIIDJjCCAg6gAwIBAgIIeIT...Jaoe79cl3deK1I4z2mlQYsvqCnzHbV/YutU7EJ9HQ";
    var bytes = Encoding.UTF8.GetBytes(key);
    var cert = new X509Certificate2(bytes);

Logic is simple. I just want to create a certificate from a certificate encoded string. Unfortunately, the above code throws
an exception on line `var cert = new X509Certificate2(bytes);`:

    Unable to decode certificate.

I believe it is a bug because the same code works well in a .Net console application. After some investigations, I found a
workaround, adding `-----BEGIN CERTIFICATE-----\n` and `\n-----END CERTIFICATE-----\n` to the encoded string, the modified
code like this:

    var key = "MIIDJjCCAg6gAwIBAgIIeIT...Jaoe79cl3deK1I4z2mlQYsvqCnzHbV/YutU7EJ9HQ";
    key = "-----BEGIN CERTIFICATE-----\n" + key + "\n-----END CERTIFICATE-----\n";      //<-- Work around!!
    var bytes = Encoding.UTF8.GetBytes(key);
    var cert = new X509Certificate2(bytes);

Now, the exception is gone.

I spent 2 hours on this issue. Hopefully, google will bring you here when you hit the same issue :). I've already filled a
[bug](https://github.com/dotnet/aspnetcore/issues/23881) at github, let's wait for the response/fix from the dotnetcore team.
