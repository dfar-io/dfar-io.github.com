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

## Setting up CORS

To set up CORS capabilities, check [this guide](https://dev.to/phillipug/comment/16l66)

You can then verify with [this command](https://gist.github.com/exAspArk/07bbbafa2a9171b6c260989780cc0955)

## Setting up authentication with JWT

First, add the `jwt` and `bcrypt` gems:

`bundle add jwt bcrypt`

Then, create a user object that will hold sign-in information:

`rails g scaffold user name:string email:string password_digest:string`

and edit the model created:

``` ruby
class User < ApplicationRecord
    has_secure_password

    validates_presence_of :name, :email, :password_digest
end
```

then add the following lines to `user_controller.rb`:

``` ruby
# Allows endpoint(s) to be accessed without authorization
skip_before_action :authorize_request, :only => :create
```

and modify the `create` action:

``` ruby
...
# POST /users
  def create
    user = User.create!(user_params)
    auth_token = AuthenticateUser.new(user.email, user.password).call
    response = { message: "Account created.", auth_token: auth_token }
  end
...
# Only allow a list of trusted parameters through.
    def user_params
      params.permit(:name, :email, :password)
    end
```

Then create `app/lib/json_web_token.rb`:

``` ruby
class JsonWebToken
    HMAC_SECRET = Rails.application.secrets.secret_key_base

    def self.encode(payload, exp = 24.hours.from_now)
        payload[:exp] = exp.to_i
        JWT.encode(payload, HMAC_SECRET)
    end

    def self.decode(token)
        body = JWT.decode(token, HMAC_SECRET)[0]
        HashWithIndifferentAccess.new body

    rescue JWT::DecodeError => e
        raise ExceptionHandler::InvalidToken, e.message
    end
end
```

and user service `app/auth/authenticate_user.rb`:

``` ruby
class AuthenticateUser
    def initialize(email, password)
        @email = email
        @password = password
    end

    def call
        JsonWebToken.encode(user_id: user.id) if user
    end

    private

    attr_reader :email, :password

    # verifies user credentials
    def user
        user = User.find_by(email: email)
        return user if user && user.authenticate(password)

        raise (ExceptionHandler::AuthenticationError, "Invalid credentials.")
    end
end
```

and authorize API request service `app/auth/authorize_api_request.rb`:

``` ruby
class AuthorizeApiRequest
    def initialize(headers = {})
      @headers = headers
    end
  
    # Service entry point - return valid user object
    def call
      {
        user: user
      }
    end
  
    private
  
    attr_reader :headers
  
    def user
      @user ||= User.find(decoded_auth_token[:user_id]) if decoded_auth_token
      # handle user not found
    rescue ActiveRecord::RecordNotFound => e
      # raise custom error
      raise(
        ExceptionHandler::InvalidToken,
        ("Invalid token #{e.message}")
      )
    end
  
    # decode authentication token
    def decoded_auth_token
      @decoded_auth_token ||= JsonWebToken.decode(http_auth_header)
    end
  
    # check for token in `Authorization` header
    def http_auth_header
      if headers['Authorization'].present?
        return headers['Authorization'].split(' ').last
      end
        raise(ExceptionHandler::MissingToken, "Missing token.")
    end
end
```

Then create the authentication controller:

`rails g scaffold_controller authentication`

and edit to look like the following:

``` ruby
class AuthenticationController < ApplicationController
  skip_before_action :authorize_request, :only => :authenticate

  # POST /login
  def authenticate
    auth_token = AuthenticateUser.new(authentication_params[:email], authentication_params[:password]).call

    render json: { auth_token: auth_token }
  end

  private

  def authentication_params
    params.fetch(:authentication, {})
  end
end
```

Create the ExceptionHandler class at `app/controllers/concerns/exception_handler.rb`:

``` ruby
module ExceptionHandler
    extend ActiveSupport::Concern
  
    # Define custom error subclasses - rescue catches `StandardErrors`
    class AuthenticationError < StandardError; end
    class MissingToken < StandardError; end
    class InvalidToken < StandardError; end
  
    included do
      # Define custom handlers
      rescue_from ActiveRecord::RecordInvalid, with: :four_twenty_two
      rescue_from ExceptionHandler::AuthenticationError, with: :unauthorized_request
      rescue_from ExceptionHandler::MissingToken, with: :four_twenty_two
      rescue_from ExceptionHandler::InvalidToken, with: :four_twenty_two
  
      rescue_from ActiveRecord::RecordNotFound do |e|
        render json: { message: e.message }, status: :not_found
      end
    end
  
    private
  
    # JSON response with message; Status code 422 - unprocessable entity
    def four_twenty_two(e)
      render json: { message: e.message }, status: :unprocessable_entity
    end
  
    # JSON response with message; Status code 401 - Unauthorized
    def unauthorized_request(e)
      render json: { message: e.message }, status: :unauthorized
    end
end
```

Now add the following to `application_controller.rb`:

``` ruby
class ApplicationController < ActionController::API
    skip_before_action :authorize_request, only: :authenticate, raise: false

    include ExceptionHandler

    # will call the below command on every controller action unless exempted
    before_action :authorize_request
    attr_reader :current_user

    def authorize_request
        @current_user = (AuthorizeApiRequest.new(request.headers).call)[:user]
    end
end

```

Finally, add the route for logging in at `routes.rb`:

``` ruby
...
post '/login', to: 'authentication#authenticate'
...
```

### Adding new user

With the configuration above, you can add a user by POSTing to `/user` with this payload:

``` json
{
  "name": "NAME",
  "email": "EMAIL",
  "password": "PASSWORD"
}
```

### Logging in



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
