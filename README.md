# Setting up a Rails API app
    Ruby - 2.6.1
    Rails - 5.2.3
    rails new bookstore-backend -T --skip-spring -C -M -d postgresql --api

##### Options:
    
  * **-T** amounts to skipping the builtin testing framework. We’ll test our API with something other than MiniTest, so let’s drop it for now.
  * **--skip-spring** removes Spring from our application; that’s a personal preference. If you don’t know what Spring does and whether you should remove it, I urge you to find out and decide for yourself.
  * **-C** skips Action Cable. No web socket communication for us!
  * **-M** skips Action Mailer, which wouldn’t be of any use to us.
  * **-d postgresql** of course sets us up for the PostgreSQL database.
  * **--api** - now we’re talking! This runs the Rails generator in the API mode, which sets us up for reading and writing JSON instead of pumping out HTML.

Rails app, this is really barebones. But we can trim this up a bit more:

* An app/jobs directory was generated for us. We know that there will be no jobs in this application for the foreseeable future, so we can drop that.

* There are four initializers that are commented out by default: `backtrace_silencers.rb, application_controller_renderer.rb, inflections.rb and mime_types.rb`. We may as well remove those.

One of the initializers will come in handy, though: cors.rb. We can uncomment what is already there and modify it to accept all origins:

    Rails.application.config.middleware.insert_before 0, Rack::Cors do
      allow do
        origins ENV.fetch('ALLOWED_ORIGINS') { '*' }
    
        resource '*',
          headers: :any,
          methods: [:get, :post, :put, :patch, :delete, :options, :head]
      end
    end

To use this, we need to uncomment rack-cors in the Gemfile.


##### Ref: 
    https://paweljw.github.io/2017/07/rails-5.1-api-app-part-1-setting-up-a-rails-api-app/