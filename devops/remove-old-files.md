---
title: Remove old files
keywords: ubuntu shell find remove
summary: "Remove files older than x days within a directory"
sidebar: devops_sidebar
permalink: /remove-old-files/
folder: devops
---

## Command

``find ~/ftp/* -type f -mtime +365 -name '*.gz' -execdir rm -- {} +``

removes **files** in the **folder** `~/ftp/*` **older than** `365` **days** matching the **name** `*.gz` and execute on directory level. `-- {} +` makes things faster.

## Breakdown

* `find`: the unix command for finding files/directories/links and etc.
* `/media/bkfolder/`: the directory to start your search in.
* `-type f`: only find files.
* `-name '*.gz'`: list files that ends with .gz.
* `-mtime +7`: only consider the ones with modification time older than 7 days.
* `-execdir ... \;`: for each such result found, do the following command in ....
* `rm -- {}`: remove the file; the {} part is where the find result gets substituted into from the previous part.