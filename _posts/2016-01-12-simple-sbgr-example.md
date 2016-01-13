---
layout: post
title:  "Setup and begin to play with SevenBridges R API"
date:   2016-01-12
author: Sean Davis
categories: sbg api
---
The [SevenBridges Cancer Genomics Cloud](https://cgc.sbgenomics.com/) offers an API for interacting with objects in their system. While this API is REST-based, a client library, in this case based in R, makes interacting with it even easier. [road2stat](https://twitter.com/road2stat) and collaborators have contributed the R package to [Bioconductor](https://bioconductor.org) where documentation and instructions for installation are available.

The concept of an API for genomic data analysis is perhaps a bit foreign to folks. The idea, though, is pretty simple. The Cancer Genomics Cloud is running in the Amazon Web Services (AWS) cloud. A few simple functions are available to interact with files, tasks, workflows, etc. These functions can be called from anywhere using basic web technologies (HTTP, basically). By wrapping these web calls into R functions, the API can be easily programmed using R, but the major work is done on the cloud. Note that any environment that supports R can utilize this system. I am going to be doing this little example on my laptop, for example.

*I am going to assume that the reader has access to the SevenBridges Cancer Genomics Cloud pilot.*

To get started, we first need to install the [sbgr](http://bioconductor.org/packages/sbgr).

``` r
require(sbgr) 
```

    ## Loading required package: sbgr

At this point, we should have the `sbgr` package installed. The first step to using it is to get an authenticated connection. To do so:

1.  login to the [SevenBridges Cancer Genomics Cloud](https://cgc.sbgenomics.com/) and choose an appropriate login method.
2.  After logging in, in the upper right hand corner, pull down under your login name and click `settings`. This will open a new page.
3.  On the left side of the screen, click the "developer" tab.
4.  Generate an authentication token or copy the one that is already there. I will refer to that string as the "token".

We are now ready to authenticate using the `Auth()` function:

``` r
token = Sys.getenv('SBGR_TOKEN')
a = Auth(token,
         platform='cgc',
         version = 'v2')
```

Note that I had to specify the platform as 'cgc' to get access to the Cancer Genomics Cloud instance of SevenBridges. That platform is running version 'v2', so I also need to specify that in the call to `Auth()`.

We are going to be creating a new project, below. In order to do that, we need to get billing information for the current account.

``` r
b = a$billing_groups()
b
```

    ## $href
    ## [1] "https://cgc-api.sbgenomics.com/v2/billing/groups?offset=0&limit=25"
    ## 
    ## $items
    ## $items[[1]]
    ## $items[[1]]$id
    ## [1] "0bc96ee8-00dc-4386-b081-ba6fc2cf711f"
    ## 
    ## $items[[1]]$href
    ## [1] "https://cgc-api.sbgenomics.com/v2/billing/groups/0bc96ee8-00dc-4386-b081-ba6fc2cf711f"
    ## 
    ## $items[[1]]$name
    ## [1] "Pilot Funds (sdavis2)"
    ## 
    ## 
    ## 
    ## $links
    ## list()
    ## 
    ## attr(,"response")
    ## Response [https://cgc-api.sbgenomics.com/v2/billing/groups]
    ##   Date: 2016-01-12 23:56
    ##   Status: 200
    ##   Content-Type: application/json
    ##   Size: 270 B

I can easily create a new project by calling `project_new()`.

``` r
a$project_new(name = "APITesting", description = "API tutorial",
              billing_group_id = b$items[[1]]$id)
```

We can create a new project for the purposes of playing:

``` r
p = a$project()
```
