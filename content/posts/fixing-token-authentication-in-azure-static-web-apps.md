---
title: "Fixing Token Authentication in Azure Static Web Apps"
date: 2021-01-03T14:00:36-05:00
draft: false
---

I recently moved my app [Journally](https://www.journally.io/) over to an Azure
Static Web App in order to save on costs and explore the new technology. When I
switched over, I found that anything related to using a bearer token was no
longer working.

A little bit of searching found [this post](https://github.com/Azure/static-web-apps/issues/34),
basically saying that Azure Static Web Apps uses the `Authorization` header when
communication between the UI and Function API. Easy solution: I changed Journally
to use `JournallyAuthorization` instead of `Authorization`, and everything works
after that.

Would have been good to have this documented, but nice to at least see there's a
relatively easy fix for it.