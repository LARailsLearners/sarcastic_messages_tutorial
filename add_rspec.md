## [Add RSpec](https://github.com/charliemcelfresh/sarcastic_messages/commit/e1c6368afb66fcda3f9b4dd39c701f4beb6bb4b6) & [Output JSON docs for ping endpoint](https://github.com/charliemcelfresh/sarcastic_messages/commit/09cfd3b4cf7df3accf222cd54d7796f5bc33fdb0)

#### [Kirk Chu](https://www.pivotaltracker.com/story/show/116834227)

Write a test for the ping endpoint such that it outputs JSON documentation
using the [Zipmark RSpec api documentation gem.](https://github.com/zipmark/rspec_api_documentation)

---
#### Add rspec-rails gem

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
run `Bundle install` and `rails generate rspec:install`

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

Add rspec_api_documentation gem to your Gemfile.

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
Then run bundle install

===

Create an acceptance test by making a new directory in the spec folder called acceptance test.

Make a new file called main_spec.rb and add:

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
Run your tests to see if they pass.

Run `rake docs:generate`
Run `open doc/api/index.html` to see the docs.
