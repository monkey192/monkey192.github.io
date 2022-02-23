---
title: s3-cli-cheatsheet
date: 2022-02-23 18:53:06
tags:
    - s3
    - aws
---

| aws s3 cli                                                   | Description                                |
| ------------------------------------------------------------ | ------------------------------------------ |
| aws s3 ls                                                    | list bucket                                |
| aws s3 ls s3://{bucket-name}/{path}                          | list folder/ files                         |
| aws s3 mb s3://{bucket-name}                                 | create a bucket                            |
| aws s3 rb s3://{bucket-name}                                 | delete a bucket                            |
| aws s3 rb s3://{bucket-name} --force                         | force delete a bucket                      |
| aws s3 sync {folder path} s3://{bucket-name}/{path}          | sync (only update, add) (exclude deleting) |
| aws s3 sync {folder path} s3://{bucket-name}/{path} --delete | sync (only update, add) (include deleting) |
| aws s3 cp {folder path} s3://{bucket-name}/{path}            | copy folder from local to s3               |
| aws s3 mv {folder path} s3://{bucket-name}/{path}            | move folder from local to s3               |
| aws s3 rm s3://{bucket-name}/{folder path}                   | delete folder in bucket                    |
| aws s3 rm s3://{bucket-name}/{folder path} --recursive       | forcedelete folder in bucket               |
