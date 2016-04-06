## 1 [Set up Rails 5 (without having Rails installed)](https://github.com/charliemcelfresh/sarcastic_messages/commit/ba43396bd8f6888962c6f36a761a56acc13b3c17)
#### Richard Cheng

+ Rails 5  
+ Ruby 2.3.0  
+ No database for now, we'll install / hookup Redis in a separate ticket  
+ Make one route, api/v1/ping, that gives a 200 response, and this response  
`{ pong: true }`  
+ This is a JSON API. Make sure you take out all the parts of Rails that we don't need: 
views and view-related code, JS and js - related code.  
+ Push your code to the repo

---

[All the files for the ticket above](https://gist.github.com/jendiamond/3c2dcc1ddb0c652dacce031719185317#file-1a-md)

#### Step 1. In console/terminal: $ rails new sarcastic_messages -T -O

-T omits generating minitest. this is also useful when you want to setup a project to use rspec instead of minitest. just generate the project without any tests and then add rspec in.
-O omits generating the project with a database.

Now I didn't use --api because I didn't have rails 5 installed yet. I looked at the instructions and thought it was a bit hard. So I figure I'll do it the old school way and then add rails 5 in the gemfile later.

#### Step 2. Start removing stuff.
##### Step 2a. In the `app` folder, remove the following folders and their files: 
+ assets
+ helpers
+ mailers
+ views

**Add a folder called `serializers`.** 

So in the app folder you should only have 3 folders: 
+ controllers
+ models
+ serializers. 

And within those 3 folders, remove the `concerns` folders

##### Step 2b. in `/bin/setup remove` the following lines:
```
  puts "\n== Preparing database =="
  system "bin/rake db:setup"
  ```
since there's no database

##### Step 2c. in /config/environments/development.rb
remove config for `active_mailer`, and all the asset stuff so you only have the following:
```
Rails.application.configure do
  # Settings specified here will take precedence over those in config/application.rb.

  # In the development environment your application's code is reloaded on
  # every request. This slows down response time but is perfect for development
  # since you don't have to restart the web server when you make code changes.
  config.cache_classes = false

  # Do not eager load code on boot.
  config.eager_load = false

  # Show full error reports and disable caching.
  config.consider_all_requests_local       = true
  config.action_controller.perform_caching = false

  # Print deprecation notices to the Rails logger.
  config.active_support.deprecation = :log
end
```

##### Step 2d. in `/config/environments/production.rb`
same thing as above, remove any `config.asset` stuff:
```
  # Compress JavaScripts and CSS.
  config.assets.js_compressor = :uglifier
  # config.assets.css_compressor = :sass

  # Do not fallback to assets pipeline if a precompiled asset is missed.
  config.assets.compile = false

  # Asset digests allow you to set far-future HTTP expiration dates on all assets,
  # yet still be able to expire them through the digest params.
  config.assets.digest = true
```

##### Step 2e. in `/config/environments/test.rb`
Same again, remove `config_action_mailer`:
```
  # Tell Action Mailer not to deliver emails to the real world.
  # The :test delivery method accumulates sent emails in the
  # ActionMailer::Base.deliveries array.
  config.action_mailer.delivery_method = :test
```

##### Step 2f. in `/config/initializers`
Leave the following files and delete everything else.
+ `filter_parameter_logging.rb` 
+ `session_store.rb` 
+ `wrap_parameters.rb`


##### Step 2g. in `/config/application.rb`

Remove the following lines:
+ `require "action_mailer/railtie"`
+ `require "action_view/railtie"`
+ `require "sprockets/railtie"`

That's all for file removal.

===

#### Step 3. setup the gemfile like the following:

```
source 'https://rubygems.org'

ruby "2.3.0"

gem 'rails', github: 'rails/rails'
gem "active_model_serializers", github: "rails-api/active_model_serializers"

group :development, :test do
  gem 'byebug'
  gem 'spring'
end

group :production do
end
```

**Note the way I setup rails is by calling through the github location of the edge version.**

#### Step 4. $ `bundle install`

#### Step 5. Quick rails version verification. run $ `rails s` and see the rails version launched by the web server.

#### Step 6. routes. setup `config/routes.rb` like this:
```
Rails.application.routes.draw do
  get 'api/v1/ping' => 'main#ping'
end
```

#### Step 7. Add the main controller: $ `rails generate controller main --no-helper --no-assets`

**When i originally did this i didnt use those 2 flags, so i had to manually delete folders again. Doing it like this is much cleaner.**

#### Step 8. In the newly created `main_controller.rb` add this within the class:
```
  def ping
    render json: {
      data: [{
        type: "responses",
        attributes: {
          pong: true
        }
      }]
    }
  end
```

#### Step 9. Launch the rails server and test it. Either via browser or command line:

##### Step 9a from browser, go to http://localhost:3000/api/v1/ping
you should see this:

`{"data":[{"type":"responses","attributes":{"pong":true}}]}`

##### Step 9b from commandline, type curl `http://localhost:3000/api/v1/ping`

and you should see the same message come back.


---
---

[Add Rspec](https://github.com/zipmark/rspec_api_documentation gem)

Write a test for the ping endpoint such that it outputs JSON documentation
using the https://github.com/zipmark/rspec_api_documentation gem

---



**`Gemfile`**
```
source 'https://rubygems.org'

ruby "2.3.0"

gem 'rails', github: 'rails/rails'
gem "active_model_serializers", github: "rails-api/active_model_serializers"

group :development, :test do
  gem 'byebug'
  gem 'spring'
+ gem 'rspec-rails', '~> 3.0'
end

group :production do
end
```

**``**
```

```

**``**
```

```

**``**
```

```

**``**
```

```

**``**
```

```

**``**
```

```

**``**
```

```
