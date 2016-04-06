## [Set up Rails 5 (without having Rails installed)](https://www.pivotaltracker.com/story/show/116834213)
#### [Richard Cheng's commit](https://github.com/charliemcelfresh/sarcastic_messages/commit/ba43396bd8f6888962c6f36a761a56acc13b3c17)

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

## [Add RSpec](https://www.pivotaltracker.com/story/show/116834227)

Write a test for the ping endpoint such that it outputs JSON documentation
using the [Zipmark RSpec api documentation gem.](https://github.com/zipmark/rspec_api_documentation)

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

===

**`.rspec`**
```
--color
--require spec_helper
```

===

**`config/environments/test.rb`**
```
Rails.application.configure do
  config.cache_classes = true
  config.eager_load = false
  config.public_file_server.enabled = true
  config.public_file_server.headers = { 'Cache-Control' => 'public, max-age=3600' }
  config.consider_all_requests_local       = true
  config.action_controller.perform_caching = false
  config.action_dispatch.show_exceptions = false
  config.action_controller.allow_forgery_protection = false
  config.active_support.test_order = :random
  config.active_support.deprecation = :stderr
end
```

===

**`spec/rails_helper.rb`**
```
ENV["RAILS_ENV"] ||= 'test'
require 'spec_helper'
require File.expand_path("../../config/environment", __FILE__)
require 'rspec/rails'

RSpec.configure do |config|
  config.infer_spec_type_from_file_location!
end
```

**`spec/spec_helper.rb`**
```
RSpec.configure do |config|
  config.expect_with :rspec do |expectations|
    expectations.include_chain_clauses_in_custom_matcher_descriptions = true
  end

  config.mock_with :rspec do |mocks|
    mocks.verify_partial_doubles = true
  end
end
```


[Output JSON docs for ping endpoint](https://www.pivotaltracker.com/story/show/116834227)

(https://github.com/charliemcelfresh/sarcastic_messages/commit/09cfd3b4cf7df3accf222cd54d7796f5bc33fdb0)


**`.gitignore`**
```
# Ignore bundler config.
/.bundle

# Ignore all logfiles and tempfiles.
/log/*
!/log/.keep
/tmp

+ .byebug_history
```

**`Gemfile`**
```
source 'https://rubygems.org'

ruby "2.3.0"

gem 'rails', github: 'rails/rails'
gem "active_model_serializers", github: "rails-api/active_model_serializers"
+ gem 'rspec_api_documentation'

group :development, :test do
  gem 'byebug'
  gem 'spring'
  gem 'rspec-rails', '~> 3.0'
end

group :production do
end
```

===

**`doc/api/index.html`**
```
<!DOCTYPE html>
<html>
<head>
  <title>API Documentation</title>
  <meta charset="utf-8">
  <style>
    
body {
  font-family: Helvetica,Arial,sans-serif;
  font-size: 13px;
  font-weight: normal;
  line-height: 18px;
  color: #404040;
}
.container {
  width: 940px;
  margin-left: auto;
  margin-right: auto;
  zoom: 1;
}
pre {
  background-color: #f5f5f5;
  display: block;
  padding: 8.5px;
  margin: 0 0 18px;
  line-height: 18px;
  font-size: 12px;
  border: 1px solid #ccc;
  border: 1px solid rgba(0, 0, 0, 0.15);
  -webkit-border-radius: 3px;
  -moz-border-radius: 3px;
  border-radius: 3px;
  white-space: pre;
  white-space: pre-wrap;
  word-wrap: break-word;
}
td.required .name:after {
  float: right;
  content: &quot;required&quot;;
  font-weight: normal;
  color: #F08080;
}
a{
  color: #0069d6;
  text-decoration: none;
  line-height: inherit;
  font-weight: inherit;
}
h1, h2, h3, h4, h5, h6 {
  font-weight: bold;
  color: #404040;
}
h1 {
  margin-bottom: 18px;
  font-size: 30px;
  line-height: 36px;
}
h2 {
  font-size: 24px;
  line-height: 36px;
}
h3{
  font-size: 18px;
  line-height: 36px;
}
h4 {
  font-size: 16px;
  line-height: 36px;
}
table{
  width: 100%;
  margin-bottom: 18px;
  padding: 0;
  border-collapse: separate;
  font-size: 13px;
  -webkit-border-radius: 4px;
  -moz-border-radius: 4px;
  border-radius: 4px;
  border-spacing: 0;
  border: 1px solid #ddd;
}
table th {
  padding-top: 9px;
  font-weight: bold;
  vertical-align: middle;
  border-bottom: 1px solid #ddd;
}
table th+th, table td+td {
  border-left: 1px solid #ddd;
}
table th, table td {
  padding: 10px 10px 9px;
  line-height: 18px;
  text-align: left;
}
  </style>
</head>
<body>
<div class="container">
  <h1>API Documentation</h1>

  <div class="article">
    <h2>MainController</h2>

    <ul>
      <li>
        <a href="maincontroller/get_&#39;pong&#39;_without_api_token.html">GET &#39;pong&#39; without api_token</a>
      </li>
    </ul>
  </div>
</div>
</body>
</html>
```
===

**`doc/api/maincontroller/get_'pong'_without_api_token.html`**
```
<!DOCTYPE html>
<html>
  <head>
    <title>MainController API</title>
    <meta charset="utf-8">
    <style>
      
body {
  font-family: Helvetica,Arial,sans-serif;
  font-size: 13px;
  font-weight: normal;
  line-height: 18px;
  color: #404040;
}
.container {
  width: 940px;
  margin-left: auto;
  margin-right: auto;
  zoom: 1;
}
pre {
  background-color: #f5f5f5;
  display: block;
  padding: 8.5px;
  margin: 0 0 18px;
  line-height: 18px;
  font-size: 12px;
  border: 1px solid #ccc;
  border: 1px solid rgba(0, 0, 0, 0.15);
  -webkit-border-radius: 3px;
  -moz-border-radius: 3px;
  border-radius: 3px;
  white-space: pre;
  white-space: pre-wrap;
  word-wrap: break-word;
}
td.required .name:after {
  float: right;
  content: "required";
  font-weight: normal;
  color: #F08080;
}
a{
  color: #0069d6;
  text-decoration: none;
  line-height: inherit;
  font-weight: inherit;
}
h1, h2, h3, h4, h5, h6 {
  font-weight: bold;
  color: #404040;
}
h1 {
  margin-bottom: 18px;
  font-size: 30px;
  line-height: 36px;
}
h2 {
  font-size: 24px;
  line-height: 36px;
}
h3{
  font-size: 18px;
  line-height: 36px;
}
h4 {
  font-size: 16px;
  line-height: 36px;
}
table{
  width: 100%;
  margin-bottom: 18px;
  padding: 0;
  border-collapse: separate;
  font-size: 13px;
  -webkit-border-radius: 4px;
  -moz-border-radius: 4px;
  border-radius: 4px;
  border-spacing: 0;
  border: 1px solid #ddd;
}
table th {
  padding-top: 9px;
  font-weight: bold;
  vertical-align: middle;
  border-bottom: 1px solid #ddd;
}
table th+th, table td+td {
  border-left: 1px solid #ddd;
}
table th, table td {
  padding: 10px 10px 9px;
  line-height: 18px;
  text-align: left;
}
    </style>
  </head>
  <body>
    <div class="container">
      <h1>MainController API</h1>

      <div class="article">
        <h2>GET &#39;pong&#39; without api_token</h2>
        <h3>GET /api/v1/ping</h3>



          <h3>Request</h3>

            <h4>Headers</h4>
            <pre class="request headers">Accept: application/json
Content-Type: application/json
Host: example.org
Cookie: </pre>

          <h4>Route</h4>
          <pre class="request route highlight">GET api/v1/ping</pre>




            <h3>Response</h3>
              <h4>Headers</h4>
              <pre class="response headers">X-Frame-Options: SAMEORIGIN
X-XSS-Protection: 1; mode=block
X-Content-Type-Options: nosniff
Content-Type: application/json; charset=utf-8
ETag: W/&quot;fbbd550d36c494fe568fd87b55e93954&quot;
Cache-Control: max-age=0, private, must-revalidate
X-Request-Id: 76ef017a-ec97-4efa-b627-63005362d58f
X-Runtime: 0.017681
Content-Length: 58</pre>
            <h4>Status</h4>
            <pre class="response status">200 OK</pre>
              <h4>Body</h4>
              <pre class="response body">{
  &quot;data&quot;: [
    {
      &quot;type&quot;: &quot;responses&quot;,
      &quot;attributes&quot;: {
        &quot;pong&quot;: true
      }
    }
  ]
}</pre>
      </div>
    </div>
  </body>
</html>
```

===

**`spec/acceptance/main_spec.rb`**
```
require 'rails_helper'
require 'rspec_api_documentation/dsl'

resource 'MainController', type: :request do
  let(:body) { JSON.parse(response_body) }

  before do
    header "Accept", "application/json"
    header "Content-Type", "application/json"
  end

  get '/api/v1/ping' do
    example "GET 'pong' without api_token" do
      client.get "api/v1/ping", nil, headers
      expect(status).to eq(200)
      expect(body["data"][0]["type"]).to eq("responses")
      expect(body["data"][0]["attributes"]["pong"]).to eq(true)
    end
  end
end
```

---
---

[Add SimpleCov](https://www.pivotaltracker.com/story/show/116834233)

Add the simplecov gem, so we get test coverage output

**``**
```

```

---
---

**``**
```

```

===

**``**
```

```

===

**``**
```

```