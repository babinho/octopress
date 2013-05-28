---
author: babinho
comments: true
date: 2013-03-21 10:19:20
layout: post
slug: rails-integration-testing-with-minitestspec-and-capybara
title: Rails Integration testing with Minitest::Spec and Capybara
wordpress_id: 202
categories:
- Ruby
- Ruby on Rails
---

So you want to do integration testing for your application, good for you. If you have chosen [Minitest](https://github.com/blowmage/minitest-rails) and it's specing DSL [MiniTest::Spec](https://github.com/metaskills/minitest-spec-rails) as your test framework here is an example how to setup the integration tests to run fairly fast. Integration tests are similar to [Cucubmer](https://github.com/cucumber/cucumber-rails) features, if you have used that before. I just like this style better because Cucumber, IMO adds too much noise, but it serves you as application documentation. I will also user [Capybara](https://github.com/blowmage/minitest-rails-capybara) for the webpage testing.
Because we are using minitest-spec-rails gem, we don't need to tinker with the config/application.rb and test settings, because ActiveSupport::TestCase is a subclass of Minitest::Spec

First we will set up the gemfile, with the needed gems( I also use guard for my automated tests, but it's worth writing another blog post, soon hopefully)

```ruby
group :development, :test do
  gem 'minitest-spec-rails'
  gem 'capybara_minitest_spec' # for capybara integration and spec matchers
  gem 'capybara-webkit' # for headless javascript tests
  gem 'turn' # for prettier test output
end
```

After that we need to set up out test/test_helper.rb to use Turn and capybara

```ruby
require 'rubygems'
require 'rails/test_help'
require 'minitest/autorun'
require 'capybara/rails'
require 'turn'

class ActionDispatch::IntegrationTest
  include Rails.application.routes.url_helpers # for x_path and x_url helpers
  include Capybara::DSL
  include PossibleJSDriver # switching Capybara driver for javascript tests, look below
end

# switching to the javascript driver in integration tests
module PossibleJSDriver
  def require_js
    Capybara.current_driver = :webkit
  end

  def teardown
    super
    Capybara.current_driver = nil
  end
end
```

After you have everything set up you can use all of this in your integration tests, here is an example of one integration test, without and with javascript browser turned on(require_js turns on the webkit driver)

```ruby
require 'test_helper'
class LoginTest < ActionDispatch::IntegrationTest
  before { visit login_path }
  describe "signup text" do
    it "must have a signup text" do
      page.must_have_content("Don't have an account?")
    end
    it "must have a signup link" do
      page.find_link('Sign up').wont_be_nil
    end
    it "signup link must go to the signup path" do
      require_js # this one turns on the webkit javascript driver
      click_link('Sign up')
      current_path.must_equal(signup_path)
    end
  end
end
```

I have tested the integration test speed with :webkit as a default capybara driver and it runs at the same speed as the normal :rack_test driver, so you can go that way too.

Resources used:<br>
<https://github.com/metaskills/minitest-spec-rails><br>
<https://github.com/blowmage/minitest-rails><br>
<https://github.com/blowmage/minitest-rails-capybara><br>
<http://stackoverflow.com/questions/5655154/how-do-you-perform-javascript-tests-with-minitest-capybara-selenium/13296544#13296544><br>
<https://github.com/thoughtbot/capybara-webkit><br>
