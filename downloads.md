---
layout: page
title: Downloads
---

Latest Version
-----------------
{% assign latest = site.data.downloads | first %} 
[FLIMfit {{ latest.version }}]({{site.baseurl}}downloads/{{latest.version}}), compatible with OMERO {{latest.ome-version}}

Previous Versions
-----------------
Previous versions of FLIMfit may be downloaded using the links below.
Older still version may be download from [the OMERO server](downloads.openmicroscopy.org/flimfit) 

| FLIMfit | OMERO compatibility |
|---------|---------------------|
{% for download in site.data.downloads %} | [FLIMfit {{ download.version }}]({{site.baseurl}}download/{{download.version}}) | OMERO {{download.ome-version}} | 
{% endfor %}