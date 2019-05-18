# Setting up a Rails API app
    rails new bookstore-backend -T --skip-spring -C -M -d postgresql --api

##### Options:
    
  * **-T** amounts to skipping the builtin testing framework. We’ll test our API with something other than MiniTest, so let’s drop it for now.
  * **--skip-spring** removes Spring from our application; that’s a personal preference. If you don’t know what Spring does and whether you should remove it, I urge you to find out and decide for yourself.
  * **-C** skips Action Cable. No web socket communication for us!
  * **-M** skips Action Mailer, which wouldn’t be of any use to us.
  * **-d postgresql** of course sets us up for the PostgreSQL database.
  * **--api** - now we’re talking! This runs the Rails generator in the API mode, which sets us up for reading and writing JSON instead of pumping out HTML.


##### Ref: 
    https://paweljw.github.io/2017/07/rails-5.1-api-app-part-1-setting-up-a-rails-api-app/