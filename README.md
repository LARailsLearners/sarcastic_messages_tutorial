## 1 [Set up Rails](https://github.com/charliemcelfresh/sarcastic_messages/commit/ba43396bd8f6888962c6f36a761a56acc13b3c17)
#### Richard Cheng

Rails 5  
Ruby 2.3.0  
No database for now, we'll install / hookup Redis in a separate ticket  
Make one route, api/v1/ping, that gives a 200 response, and this response  
`{ pong: true }`  
This is a JSON API. Make sure you take out all the parts of Rails that we don't need: 
views and view-related code, JS and js - related code.  
Push your code to the repo

---

[All the files for the ticket above](https://gist.github.com/jendiamond/3c2dcc1ddb0c652dacce031719185317#file-1a-md)

#### Create a new Rails file for an API
$ `rails new sarcastic_messages --api`

#### Change the Gemfile**
**`Gemfile`**
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
#### $ bundle

#### $ `rails g controller Main ping or did you just write it?

#### Then update that controller like this so it returns "pong" when called.

**`app/controllers/main_controller.rb`**
```
class MainController < ApplicationController
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
end
```

#### Update the .gitignore or were these just created with the generate?

**`.gitgnore`**
```
/.bundle

# Ignore all logfiles and tempfiles.
/log/*
!/log/.keep
/tmp
```

**Richard, did you change anything in this file?**

**`config/application.rb`**
```
require File.expand_path('../boot', __FILE__)

require "rails"
# Pick the frameworks you want:
require "active_model/railtie"
require "active_job/railtie"
# require "active_record/railtie"
require "action_controller/railtie"
# require "rails/test_unit/railtie"

# Require the gems listed in Gemfile, including any gems
# you've limited to :test, :development, or :production.
Bundler.require(*Rails.groups)

module SarcasticMessages
  class Application < Rails::Application
    # Settings in config/environments/* take precedence over those specified here.
    # Application configuration should go into files in config/initializers
    # -- all .rb files in that directory are automatically loaded.

    # Set Time.zone default to the specified zone and make Active Record auto-convert to this zone.
    # Run "rake -D time" for a list of tasks for finding time zone names. Default is UTC.
    # config.time_zone = 'Central Time (US & Canada)'

    # The default locale is :en and all translations from config/locales/*.rb,yml are auto loaded.
    # config.i18n.load_path += Dir[Rails.root.join('my', 'locales', '*.{rb,yml}').to_s]
    # config.i18n.default_locale = :de
  end
end
```
#### Change the config routes

**`config/routes.rb`**
```
Rails.application.routes.draw do
  get 'api/v1/ping' => 'main#ping'
end
```



---
---
---



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

**``**
```

```
