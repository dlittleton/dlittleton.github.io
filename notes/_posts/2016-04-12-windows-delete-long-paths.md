---
layout: post
title: "Deleting Long Paths on Windows"
---

Every now and then as a developer I find myself the not-so-proud owner of
a pathologically nested folder structure that Windows Explorer is unable
to delete. In the past, my usual approach has been to use `subst` to shorten
the offending path until I am able to get rid of it. However, I recently 
learned the following `robocopy` trick that works to get rid of the deeply 
nested folders much more easily:

    mkdir empty
    robocopy empty <deep-path> /purge
   