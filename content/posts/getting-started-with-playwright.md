---
title: "Getting Started With Playwright"
date: 2022-05-14T13:01:41Z
draft: false
---

## Setting up with VSCode

1. Create a dev container using the Ubuntu image (include Node.js), then rebuild into dev container
1. Install Playwright into your project with `npm i -D playwright`
1. Add the following `postCreateCommand` in `.devcontainer/devcontainer.json`

``` json
"postCreateCommand": "rm -rf node_modules && npm i && npx playwright install && npx playwright install-deps",
```

1. Create [the first script](https://playwright.dev/docs/library#first-script), run using `node main.ts`

## Setting up video playback

This allows for video playback to occur of all runs, very useful for debugging issues. An example where video will be played inside the `try` block and will generate a video regardless of success or failure:

``` typescript
(async () => {
    const browser = await webkit.launch();
    const context = await browser.newContext({
        recordVideo: { dir: 'video' }
    })
    const page = await context.newPage();

    try {
        // executing code
    } catch (err) {
        await context.close();
        throw err;
    }

    await context.close();
    await browser.close();
})();
```

## Setting up local environment variables with .env

Playwright's preferred method for handling variables is to use environment variables, so being able to locally define variables in your development container is very helpful.

1. Create `.devcontainer/.env.example` and fill it with desired keys:

``` bash
NAME=
PHONE=
```

2. Copy this file as `.devcontainer/.env` and fill in the desired values:

``` bash
NAME=YourName
PHONE=1234567890
```

3. Create methods getEnvVars and verifyEnvVars for convenience in your script:

``` typescript
function getEnvVars() {
  const envVars = {
    NAME: process.env.NAME,
    PHONE: process.env.PHONE,
  };
    
  verifyEnvVars(envVars)
    
  return envVars;
}

function verifyEnvVars(env_vars) {
  for (const property in env_vars) {
    const value = env_vars[property];
    if (value === undefined || value === '') {
      console.error(`Environment variable ${property} not defined.`)
      process.exit(1);
    }
  }
}
```

4. Add the following line to `devcontainer.json`:

``` json
// add environment variables from .env
"runArgs": ["--env-file", ".devcontainer/.env"]
```

5. Rebuild and verify usage:

``` typescript
const env = getEnvVars();

await page.fill('#txtGuestFirstName', env.FIRST_NAME);
```