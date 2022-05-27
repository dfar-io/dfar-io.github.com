---
title: "Getting Started With Playwright"
date: 2022-05-14T13:01:41Z
draft: false
---

1. [Setting up with VSCode](#setting-up-with-vscode)
2. [Setting up video playback](#setting-up-video-playback)
3. [Setting up local environment variables with .env](#setting-up-local-environment-variables-with-env)
4. [Setting up Github Actions](#setting-up-github-actions)

## Setting up with VSCode

1. Create a dev container using the Ubuntu image (include Node.js), then rebuild into dev container
1. Install Playwright into your project with `npm i -D playwright`
1. Add the following `postCreateCommand` in `.devcontainer/devcontainer.json`

``` json
"postCreateCommand": "rm -rf node_modules && npm i",
```

1. Add the following to `package.json`:

``` json
"scripts": {
  "postinstall": "npx playwright install && npx playwright install-deps"
}
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

Create `.devcontainer/.env.example` and fill it with desired keys:

``` bash
NAME=
PHONE=
```

Copy this file as `.devcontainer/.env` and fill in the desired values:

``` bash
NAME=YourName
PHONE=1234567890
```

Create methods getEnvVars and verifyEnvVars for convenience in your script:

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

Add the following line to `devcontainer.json`:

``` json
// add environment variables from .env
"runArgs": ["--env-file", ".devcontainer/.env"]
```

Rebuild and verify usage:

``` typescript
const env = getEnvVars();

await page.fill('#txtGuestFirstName', env.FIRST_NAME);
```

## Setting up Github Actions

This allows for running Playwright jobs automatically either on demand or on a schedule from anywhere. Example below assumes an application reading an email and password from environment variables.

``` yml
name: Playwright Script

on:
  schedule:
  - cron: '0 20 * * *'
  workflow_dispatch:
  push:
    branches:
      - main

env:
  NODE_VERSION: '16.x'

jobs:
  run:
    runs-on: ubuntu-latest
    env:
      EMAIL: ${{ secrets.EMAIL }}
      PASSWORD: ${{ secrets.PASSWORD }}
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@v2
    - name: Setup Node ${{ env.NODE_VERSION }} environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: Install dependencies
      run: npm i
    - name: Run Playwright script
      run: node main.ts
```
