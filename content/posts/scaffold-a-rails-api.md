---
title: "Scaffolding a Ruby on Rails Api"
date: 2021-03-06T19:00:45Z
draft: false
---

## Visual Studio Code Setup

First, I recommend getting VSCode's Remote Container set up to allow for container-based development and not requiring adding dependencies to your local machine.

Create a directory that will store your Rails project.

`mkdir rails-api`
`cd rails-api`

Then download the Remote Containers VSCode extension, and add a `.devcontainer` file using the Ruby on Rails community image.

## Scaffolding the Rails API

Inside the container, create the Rails API using `rails new . --api`.

You can now push this 

## Set up Debugging for VSCode

To set up debugging inside VSCode, run the following commands:

```
bundle add ruby-debug-ide debase
```

Then add the following to `.vscode/launch.json`:

```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [{
        "name": "Rails server",
        "type": "Ruby",
        "request": "launch",
        "cwd": "${workspaceRoot}",
        "program": "${workspaceRoot}/bin/rails",
        "pathToRDebugIDE": "~/.asdf/shims/rdebug-ide",
        "showDebuggerOutput": true,
        "args": [
          "server"
        ]
    }]
}
```

You'll now be able to run and debug the API locally at port 3000.

Finally, edit the `.devcontainer/devcontainer.json` file to have `postCreateCommand` use `bundle install` so dependencies for the container will always load on build.

## Create endpoint

To create an endpoint, use the following command:

`rails g scaffold <entity> <fields>`

So as an example:

`rails g scaffold person name:string title:string`

Start the application and view the endpoint created above, noting that CRUD functionality is built in.

### Seeding Data

You may want to seed data - to do so, edit the `seeds.rb` file to include data for your class. After that, run the `rake db:setup` command to set up the DB with the migrations and seeded data correctly.

An easy way to automate this is to add the `rake db:reset` step to the `postCreateCommand` script for the Remote Container, running `bundle install && rake db:reset`. This will clear the DB on rebuild of the container, and run all migrations.

## Deploying to Heroku

### Setting up PostgreSQL

To deploy to Heroku, the Rails app needs to use Postgresql instead of sqlite.

First, set up Postgresql in your development container using [this guide](https://dev.to/imiked/starting-a-rails-app-using-vscode-containers-1gj9).

Next, set up the appropriate gems:

```
bundle add pg
bundle remove sqlite3
```

Finally, edit `config/database.yml`:

```
default: &default
  adapter: postgresql
  encoding: unicode
  host: db
  user: postgres
  password: <%= ENV.fetch("DB_PASSWORD") %>
  # For details on connection pooling, see Rails configuration guide
  # https://guides.rubyonrails.org/configuring.html#database-pooling
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>

```

Rebuild the container and run locally to verify everything working.

### Deploying

Once this is done, you can deploy the app to Heroku. You can do this manually
using Heroku CLI, or you can connect the app via GitHub and Heroku to enable
automatic deployments. If you run into issues, you can pull the logs from
Heroku CLI.

After the app is deployed, you'll need to migrate the DB changes using:

`heroku run rake db:migrate`

If seed data is desired, you can then run

`heroku run rake db:seed`

## Setting up CORS

To set up CORS capabilities, check [this guide](https://dev.to/phillipug/comment/16l66)

You can then verify with [this command](https://gist.github.com/exAspArk/07bbbafa2a9171b6c260989780cc0955)
