---
layout: post
comments: true
categories: Android

title: "SYSU Library"
img: /images/android.jpg
abstract: "An Android university library application, sending GET/POST request to the official SYSU library website and parsing the returned HTML. Users can check the remaining collections of their desired books and add books to their favorite lists. Students are able to login in to check their borrowing history. "
tags: android java http SQLite html Jsoup 
---

## Android Developer Tools ![eclipse](./eclipse_logo.png) ![developer](./developer_logo.png)

I used the Android Developer Tools (ADT) plugin for Eclipse for our software development. However, support for the ADT in Eclipse has ended. Google recommends developers to migrate their app development projects to Android Studio as soon as possible.

## Relevant Package

[HttpClient](http://hc.apache.org/httpcomponents-client-ga/) : an useful package providing robust support for the HTTP protocol. I used it to send GET/POST request to our [official SYSU library website](http://library.sysu.edu.cn/)

[SQLite](http://www.sqlite.org/) : a software library that implements a self-contained, serverless, zero-configuration, transactional SQL database engine. I used it to store information including pictures, search history, favorites and etc.

[Jsoup](http://jsoup.org/) : a Java library for working with real-world HTML. I used it to parse HTML returned from the official website

## Principle

* get input from users of the Android application
* send GET/POST request to the official library website
* parse HTML returned by the official website
* present the results on the Android application

I simulated user behaviors on the official website, captured the network packet and analyzed its format in order to successfully parse the HTML it returns.

## Exhibition

![app1](./app1.png)
![app2](./app2.png)