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

## Modeling reality
Thankfully, we have the lovely money-rails gem to take care of that for us. Let’s drop it in right now into our Gemfile and install with bundle install. Afterwards it will need to be initialized with
    
    rails g money_rails:initializer
    
This will create a money.rb initializer. For now, we can drop in the simplest of configs there:

    MoneyRails.configure do |config|
      config.default_currency = :usd
    end

#### Scaffolding

    rails g scaffold Author name:string
    rails g scaffold Book author:belongs_to title:string price:monetize
    
* Notice how we can use `monetize` now as a field type? That’s all `money-rails`    
    
    
    rails db:migrate
    
#### Routes
    Rails.application.routes.draw do
      resources :authors do
        resources :books
      end
    
      root to: 'authors#index'
    end
    
#### Controllers

`BooksController`
    # ...
    
      private
    
      def set_book
        @book = @author.books.find(params[:id])
      end
    
      def set_author
        @author = Author.find(params[:author_id])
      end
    
      # ...    

#### Models

    class Author < ApplicationRecord
      has_many :books
    
      validates :name, presence: true
    end
    
    class Book < ApplicationRecord
      belongs_to :author
      monetize :price_cents
      validates :title, presence: true
    end
    
#### Seeding
    rake import:goodreads
    
## API versioning
We need to change our routes
    
    namespace :api do
      namespace :v1 do
        resources :authors do
          resources :books
        end
      end
    end

Then move our controllers into app/controllers/api/v1, so that we keep with the Rails convention.



    
        
##### Ref: 
    https://paweljw.github.io/2017/07/rails-5.1-api-app-part-1-setting-up-a-rails-api-app/
    https://paweljw.github.io/2017/07/rails-5.1-api-app-part-2-modeling-reality/
    