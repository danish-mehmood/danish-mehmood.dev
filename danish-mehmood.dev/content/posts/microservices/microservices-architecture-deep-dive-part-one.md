---
title: "Microservices Architecture Deep Dive: Part One"
subtitle: "What are microservices, what are their tradeoffs and how to decompose a monolith in to many microservices"
date: 2024-11-29T08:08:47+05:00
lastmod: 2024-11-29T08:08:47+05:00
draft: false
author: "danish mehmood"
authorLink: ""
description: "This blog series is all about microservices, we will start with simple definition of microservices and 
and go all the way to know how to implement them effectively and when do they make sense and when is it a stupid idea 
to implement microservices"
license: ""

images: ["images/posts/microservices/microservices_header.gif"]
featuredImage: "images/posts/microservices/microservices_header.gif"
featuredImagePreview: "images/posts/microservices/microservices_header.gif"
resources:
  - name: "featured-image"
    src: "featured-image.gif"
tags: ["microservices" , "distributed systems"]
categories: ["microservices"]


hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false
lightgallery: true
ruby: true
fraction: true
fontawesome: true
linkToMarkdown: true
rssFullText: false

toc:
  enable: true
  auto: true
code:
  copy: true
  maxShownLines: 50
math:
  enable: false
  # ...
mapbox:
  # ...
share:
  enable: true
  # ...
comment:
  enable: true
  # ...
library:
  css:
    # someCSS = "some.css"
    # located in "assets/"
    # Or
    # someCSS = "https://cdn.example.com/some.css"
  js:
    # someJS = "some.js"
    # located in "assets/"
    # Or
    # someJS = "https://cdn.example.com/some.js"
seo:
  images: ["images/posts/microservices/microservices_header.gif"]
  # ...
---

<!--more-->
