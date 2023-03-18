---
title: "Nestjs Azure App Service Deployment"
date: 2023-03-18T14:44:13Z
draft: false
---

Needed to do a few things to get this working:

Modify `main.js` to allow for listing to environment variable for port number.

``` typescript
await app.listen(process.env.PORT || 3000);
```

Set the startup command on the App Service to

``` bash
pm2 start dist/main.js --no-daemon
```

[Reference](https://azureossd.github.io/2022/02/11/Nest-Deployment-on-App-Service-Linux/index.html)