---
title: docker Q&A
date: 2022-08-01 12:49:22
tags:
  - docker
---

### Docker notes (Q&A)

1. [Dockerfile best practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

1. How to create a user when using base alpine image?
Alpine uses the command adduser and addgroup for creating users and groups (rather than useradd and usergroup).

```sh
FROM alpine:latest
# Create a group and user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
# Tell docker that all future commands should run as the appuser user
USER appuser
```