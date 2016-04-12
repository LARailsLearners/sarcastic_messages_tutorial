#### [All the files for 1 Set up Rails](https://gist.github.com/jendiamond/3c2dcc1ddb0c652dacce031719185317#1-set-up-rails)

**`.gitgnore`**
```
/.bundle

# Ignore all logfiles and tempfiles.
/log/*
!/log/.keep
/tmp
```
===

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

**`Rakefile`**
```
require File.expand_path('../config/application', __FILE__)

Rails.application.load_tasks
```

**`app/controllers/application_controller.rb`**
```
class ApplicationController < ActionController::Base
end
```

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

**`bin/bundle`**
```
#!/usr/bin/env ruby
ENV['BUNDLE_GEMFILE'] ||= File.expand_path('../../Gemfile', __FILE__)
load Gem.bin_path('bundler', 'bundle')
```

**`bin/rails`**
```
#!/usr/bin/env ruby
begin
  load File.expand_path('../spring', __FILE__)
rescue LoadError => e
  raise unless e.message.include?('spring')
end
APP_PATH = File.expand_path('../../config/application', __FILE__)
require_relative '../config/boot'
require 'rails/commands'
```

**`bin/rake`**
```
#!/usr/bin/env ruby
begin
  load File.expand_path('../spring', __FILE__)
rescue LoadError => e
  raise unless e.message.include?('spring')
end
require_relative '../config/boot'
require 'rake'
Rake.application.run
```

**`bin/setup`**
```
#!/usr/bin/env ruby
require 'pathname'

# path to your application root.
APP_ROOT = Pathname.new File.expand_path('../../',  __FILE__)

Dir.chdir APP_ROOT do
  # This script is a starting point to setup your application.
  # Add necessary setup steps to this file:

  puts "== Installing dependencies =="
  system "gem install bundler --conservative"
  system "bundle check || bundle install"

  # puts "\n== Copying sample files =="
  # unless File.exist?("config/database.yml")
  #   system "cp config/database.yml.sample config/database.yml"
  # end

  puts "\n== Removing old logs and tempfiles =="
  system "rm -f log/*"
  system "rm -rf tmp/cache"

  puts "\n== Restarting application server =="
  system "touch tmp/restart.txt"
end
```

**`bin/spring`**
```
#!/usr/bin/env ruby

# This file loads spring without using Bundler, in order to be fast.
# It gets overwritten when you run the `spring binstub` command.

unless defined?(Spring)
  require 'rubygems'
  require 'bundler'

  if (match = Bundler.default_lockfile.read.match(/^GEM$.*?^    (?:  )*spring
\((.*?)\)$.*?^$/m))
    Gem.paths = { 'GEM_PATH' => [Bundler.bundle_path.to_s,
*Gem.path].uniq.join(Gem.path_separator) }
    gem 'spring', match[1]
    require 'spring/binstub'
  end
end
```

**`config.ru`**
```
# This file is used by Rack-based servers to start the application.

require ::File.expand_path('../config/environment', __FILE__)
run Rails.application
```

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
    # Settings in config/environments/* take precedence over those specified
    # here.
    # Application configuration should go into files in config/initializers
    # -- all .rb files in that directory are automatically loaded.

    # Set Time.zone default to the specified zone and make Active Record
    # auto-convert to this zone.
    # Run "rake -D time" for a list of tasks for finding time zone names.
    # Default is UTC.
    # config.time_zone = 'Central Time (US & Canada)'

    # The default locale is :en and all translations from
    # config/locales/*.rb,yml are auto loaded.
    # config.i18n.load_path += Dir[Rails.root.join('my', 'locales',
    # '*.{rb,yml}').to_s]
    # config.i18n.default_locale = :de
  end
end
```

**`config/boot.rb`**
```
# Load the Rails application.
require File.expand_path('../application', __FILE__)

# Initialize the Rails application.
Rails.application.initialize!
```

**`config/environment.rb`**
```
# Load the Rails application.
require File.expand_path('../application', __FILE__)

# Initialize the Rails application.
Rails.application.initialize!
```

**`config/environments/development.rb`**
```

Rails.application.configure do
  # Settings specified here will take precedence over those in
  # config/application.rb.

  # In the development environment your application's code is reloaded on
  # every request. This slows down response time but is perfect for
  # development
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

**`config/environments/production.rb`**
```
Rails.application.configure do
  # Settings specified here will take precedence over those in
  # config/application.rb.

  # Code is not reloaded between requests.
  config.cache_classes = true

  # Eager load code on boot. This eager loads most of Rails and
  # your application in memory, allowing both threaded web servers
  # and those relying on copy on write to perform better.
  # Rake tasks automatically ignore this option for performance.
  config.eager_load = true

  # Full error reports are disabled and caching is turned on.
  config.consider_all_requests_local       = false
  config.action_controller.perform_caching = true

  # Enable Rack::Cache to put a simple HTTP cache in front of your application
  # Add `rack-cache` to your Gemfile before enabling this.
  # For large-scale production use, consider using a caching reverse proxy
  # like
  # NGINX, varnish or squid.
  # config.action_dispatch.rack_cache = true

  # Disable serving static files from the `/public` folder by default since
  # Apache or NGINX already handles this.
  config.serve_static_files = ENV['RAILS_SERVE_STATIC_FILES'].present?

  # Specifies the header that your server uses for sending files.
  # config.action_dispatch.x_sendfile_header = 'X-Sendfile' # for Apache
  # config.action_dispatch.x_sendfile_header = 'X-Accel-Redirect' # for NGINX

  # Force all access to the app over SSL, use Strict-Transport-Security, and
  # use secure cookies.
  # config.force_ssl = true

  # Use the lowest log level to ensure availability of diagnostic information
  # when problems arise.
  config.log_level = :debug

  # Prepend all log lines with the following tags.
  # config.log_tags = [ :subdomain, :uuid ]

  # Use a different logger for distributed setups.
  # config.logger = ActiveSupport::TaggedLogging.new(SyslogLogger.new)

  # Use a different cache store in production.
  # config.cache_store = :mem_cache_store

  # Enable serving of images, stylesheets, and JavaScripts from an asset
  # server.
  # config.action_controller.asset_host = 'http://assets.example.com'

  # Ignore bad email addresses and do not raise email delivery errors.
  # Set this to true and configure the email server for immediate delivery to
  # raise delivery errors.
  # config.action_mailer.raise_delivery_errors = false

  # Enable locale fallbacks for I18n (makes lookups for any locale fall back
  # to
  # the I18n.default_locale when a translation cannot be found).
  config.i18n.fallbacks = true

  # Send deprecation notices to registered listeners.
  config.active_support.deprecation = :notify

  # Use default logging formatter so that PID and timestamp are not
  # suppressed.
  config.log_formatter = ::Logger::Formatter.new
end
```

**`config/environments/test.rb`**
```
Rails.application.configure do
  # Settings specified here will take precedence over those in
  # config/application.rb.

  # The test environment is used exclusively to run your application's
  # test suite. You never need to work with it otherwise. Remember that
  # your test database is "scratch space" for the test suite and is wiped
  # and recreated between test runs. Don't rely on the data there!
  config.cache_classes = true

  # Do not eager load code on boot. This avoids loading your whole application
  # just for the purpose of running a single test. If you are using a tool
  # that
  # preloads Rails for running tests, you may have to set it to true.
  config.eager_load = false

  # Configure static file server for tests with Cache-Control for performance.
  config.serve_static_files   = true
  config.static_cache_control = 'public, max-age=3600'

  # Show full error reports and disable caching.
  config.consider_all_requests_local       = true
  config.action_controller.perform_caching = false

  # Raise exceptions instead of rendering exception templates.
  config.action_dispatch.show_exceptions = false

  # Disable request forgery protection in test environment.
  config.action_controller.allow_forgery_protection = false

  # Randomize the order test cases are executed.
  config.active_support.test_order = :random

  # Print deprecation notices to the stderr.
  config.active_support.deprecation = :stderr

  # Raises error for missing translations
  # config.action_view.raise_on_missing_translations = true
end
```

**`config/initializers/filter_parameter_logging.rb`**
```
# Be sure to restart your server when you modify this file.

# Configure sensitive parameters which will be filtered from the log file.
Rails.application.config.filter_parameters += [:password]
```

**`config/initializers/filter_parameter_logging.rb`**
```
# Be sure to restart your server when you modify this file.

# Configure sensitive parameters which will be filtered from the log file.
Rails.application.config.filter_parameters += [:password]
```

**`config/initializers/session_store.rb`**
```
# Be sure to restart your server when you modify this file.

Rails.application.config.session_store :cookie_store, key:
'_sarcastic_messages_session'
```

**`config/initializers/wrap_parameters.rb`**
```

# Be sure to restart your server when you modify this file.

# This file contains settings for ActionController::ParamsWrapper which
# is enabled by default.

# Enable parameter wrapping for JSON. You can disable this by setting :format
# to an empty array.
ActiveSupport.on_load(:action_controller) do
  wrap_parameters format: [:json] if respond_to?(:wrap_parameters)
end
```

**`config/locales/en.yml`**
```
# Files in the config/locales directory are used for internationalization
# and are automatically loaded by Rails. If you want to use locales other
# than English, add the necessary files in this directory.
#
# To use the locales, use `I18n.t`:
#
#     I18n.t 'hello'
#
# In views, this is aliased to just `t`:
#
#     <%= t('hello') %>
#
# To use a different locale, set it with `I18n.locale`:
#
#     I18n.locale = :es
#
# This would use the information in config/locales/es.yml.
#
# To learn more, please read the Rails Internationalization guide
# available at http://guides.rubyonrails.org/i18n.html.

en:
  hello: "Hello world"
```

**`config/routes.rb`**
```
Rails.application.routes.draw do
  get 'api/v1/ping' => 'main#ping'
end
```

**`config/secrets.yml`**
```
# Be sure to restart your server when you modify this file.

# Your secret key is used for verifying the integrity of signed cookies.
# If you change this key, all old signed cookies will become invalid!

# Make sure the secret is at least 30 characters and all random,
# no regular words or you'll be exposed to dictionary attacks.
# You can use `rake secret` to generate a secure secret key.

# Make sure the secrets in this file are kept private
# if you are sharing your code publicly.
```

```
development:
  secret_key_base:
83beac9e9449d5f16a63bc57561ea914aa987e8daa46de4e0eed7f25d906d62c3825da4841cc9718de718b6ec48c131694df0a53ae31cc88f1429f4c77ea702b
```
```
test:
  secret_key_base:
60c49a0bc1cbc8b1eeab4ce7192d3c6b1f42cf12caf6bb4c600c000061d009be7a7c6bcaf96a7dfc91fa975df5194a60d1c9d95f69e6003ef910e0a3abbf8939
```

# Do not keep production secrets in the repository,
# instead read values from the environment.
production:
  secret_key_base: <%= ENV["SECRET_KEY_BASE"] %>
```
