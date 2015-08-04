Rails Reference
===============

Table of Contents
-----------------
- [Startup Rails App](#startup-rails-app)
- [Gems](#gems)
- [Show All Routes](#show-all-routes)
- [Make a Model](#make-a-model)
- [Make a Controller](#make-a-controller)
- [Add and Remove Columns from Migrations](#add-column)
- [Resource Routing](#resource-routing)
- [RSpec](#rspec)
- [Heroku Deployment](#heroku-deployment)
- [User Authentication](#user-authentication)
- [Callbacks](#callbacks)
- [Factory Girl](#factory-girl)

##Startup Rails App
Do this after forking from master, make sure you're on your own branch.

1.  cd into your 'project-forks' folder
2.  `echo 'ProjectName' > PROJECTFOLDER/.ruby-gemset` 
    - ex) `echo 'TaskListRails' > C3Projects--TaskListRails/.ruby-gemset`
3.  `echo '2.2.2' > PROJECTFOLDER/.ruby-version`
    - ex) `echo '2.2.2' > C3Projects--TaskListRails/.ruby-version`
4.  cd in to project folder (wrappers happen...)
    - `cd C3Projects--TaskListRails`
5.  `rvm gemset list` (yes it's on the gemset we just created)
6.  `gem install bundler`
7.  `gem install rails --no-ri --no-rdoc`
8.  `git status`
9.  `git add .`
10. `git commit -m "Created gemset."`
11. `rails new .`
12. `git add .`
12. `git commit "fresh Rails install"`
14. open and edit `Gemfile` with gems, save and bundle
  
    ###Gems
    - **better errors** (in development section):  
    ```ruby
    gem 'better_errors'
    gem 'binding_of_caller'
    ```
    - **pry console** (in development section): `gem 'pry-rails'`
    - **rspec** (in test section): `gem 'rspec-rails'`
      - `rails generate rspec:install`
      - Add `--format doc` to .rspec file to see words
    - **factory girl** (in test section): `gem 'factory_girl_rails', '~> 4.0'`
      - Add to config block in spec_helper.rb `config.include FactoryGirl::Syntax::Methods` and `require 'factory_girl'` to top of file
      - Create file to define factories: `touch spec/factories.rb`
    - **simplecov** (in test section): `gem 'simplecov', require: false`
      - add to spec/spec_helper.rb
      ```ruby
      require 'simplecov'
      SimpleCov.start 'rails'
      ```
      - add `coverage` to .gitignore
    - **bootstrap**: `gem 'bootstrap-sass'`
      - create custom.css.scss file for your own custom CSS
      - add to _custom.css.scss_
      ```css
      @import "bootstrap-sprockets";
      @import "bootstrap";
      ```
      - add to _app/assets/javascripts/application.js_
      ```
      //= require jquery
      //= require bootstrap-sprockets
      ```
      - restart rails server if already open
    - **bcrypt**: `gem 'bcrypt'`
      - add `has_secure_password` to User model associations
    - **carrierwave**: add `gem 'mini_magick'` BEFORE `gem 'carrierwave'` 
      - If imagemagick is not installed: `brew install imagemagick`
      - `rails g uploader image`, image can be called anything: avatar, cover_art, etc.
      - `rails g migration adds_image_to_albums`, to add a reference column (for the image) to database
      - Add `add_column :albums, :image, :string` to new migration file.
      - `rake db:migrate`
      - Open Album Model and add `# Mounted Objects -----------------------`
      - Add `mount_uploader :image, ImageUploader` under "Mounted Objects"
      - Go to uploaders/image_uploader.rb, uncomment line 7 `include CarrierWave::MiniMagick`
        - line 35 block allows you to resize image as it's uploaded, uncomment to resize images to thumbnails (50px by 50px)
        - add another block to create another version of image transformation (if desired)
        - uncomment file extensions on line 41 to allow only certain file ext. (jpg, png, gif)
      - Add `:image` to albums_params in AlbumsController
      - Able to use these methods `.image_url` and `.image_url(:thumb)`
      - Add `public/uploads/` to .gitignore
    - **HTTParty**: `gem 'httparty'`
      - `require 'HTTParty'` wherever you're using it
      - Example: 
        - `weather_url = "http://api.openweathermap.org/data/2.5/weather?q=Seattle&units=imperial"`
        - `r = HTTParty.get(weather_url)`
    - **OmniAuth**
      - Note different providers have their own gems they maintain and one can use:
      ```ruby
      gem 'omniauth'
      gem 'omniauth-github'
      ```
      - Go to GitHub to register new application (in profile settings --> applications --> Developer Applications -- Register New Application)
      - Authorization callback URL: `http://localhost:3000/auth/github/callback`
      - Follow remaining steps here: [OmniAuth](https://github.com/Ada-Developers-Academy/daily-curriculum/blob/master/topic_resources/omniauth.md)
    - if deploying to **heroku**: move sqlite3 gem to development, then add 
    ```ruby
    group :production do
        gem 'pg'
    end
    ```
15. `bundle install --without production`


##Show All Routes
`rake routes`


##Make a Model
`rails generate model modelname columnname1:type columnname2:type columname3:type`
- Example: `rails generate model student name:string cohort:string birthday:datetime`


##Make a Controller
`rails generate controller controller_name`
- Example: `rails generate controller tasks`
- convention says controller name is plural
  - i.e. ClientsController preferred over ClientController


##Add Column
`rails generate migration add_columnname_to_tablename column:type` 
- Example: `rails generate migration add_personid_to_tasks personid:integer`


##Remove Column
`rails generate migration remove_columnname_from_tablename column:type` 
- Example: `rails generate migration remove_personalid_from_tasks personid:integer`


##Resource Routing
####_All routes_
`resources :labels`

####_Only certain routes_
` resources :labels, only: [:index, :show]`

####_All routes EXCEPT_
` resources :labels, except: [:index, :show]`

####_Taking a Block_
```ruby
resources :labels do
  # 8 more routes generated
  resources :albums
end
```

####_Nesting Routes_
Shouldn't nest more than one deep

```ruby
  resources :labels do
    resources :albums
    resources :artists do
      resources :albums, only: [:index, :show]
    end
  end
```

####_Collection Routes_
```ruby
resources :albums, only: [:index, :show] do
  collection do
    get 'released/:year', action: 'by_year', as: 'by_year'
  end
end
```
- `get 'released/:year' => 'albums#by_year'`
- `as: 'bye_year'` allows for `by_year_albums_path` for linking
- also have `by_year_albums_url` for linking offsite


##RSpec
1. Add gem to development section `gem 'rspec-rails', '~> 3.0'`
2. `bundle`
3. `rails generate rspec:install`
4. Add `--format doc` to .rspec file to see words

####_Model Specs_ (example)
1. To generate tests for Album model `rails generate rspec:model Album`
2. Go to spec -> model -> album_spec to edit specs
3. Edit specs
4. To run, use `rspec` command in terminal

####_For Controller_ (example)
1. To generate tests for Albums controller`rails generate rspec:controller albums`
2. Go to spec -> controllers -> album_controller_spec to edit specs
3. Edit specs
4. To run, use `rspec` command in terminal

To run a spec for a specific folder
`rspec folder/path`
For example, `rspec spec/controllers`



##Heroku Deployment
_Make sure your sqlite3 gem is under development group and add gem 'pg' is in production group_

1. Make sure current project is committed, `git commit -m "Your message here"`
2. `heroku create`
3. `git push heroku branch:master`
4. `heroku run rake db:migrate`
5. `heroku run rake db:seed`

####_If db:drop doesn't work_
1. `heroku pg:reset DATABASE`
2. `heroku run rake db:migrate`
3. `heroku run rake db:seed`
4. `heroku restart`


##User Authentication
1. add `gem 'bcrypt'`
2. `bundle`
3. `rails g model User name:string email:string password_digest:string`
4. Add `has_secure_password` to User model (-> app/model/user.rb)
    - Methods available for models w/ 'password_digest defined': `user.password` and `user.password_confirmation`
    - will not save password if these two values don't match
    - `user.authenticate` allows one to check if password matches existing stored `password_digest`
    - `User.find_by(name: 'david').try(:authenticate, 'newd00les')`
5. Add `home_controller.rb` and `root 'home#index'` to show a home page
6. `rails g controller sessions`
7. Add `new`, `create`, and `destroy` methods to SessionsController.

####_Routes_
```ruby
root 'home#index'
resources :users
resources :sessions, :only => [:new, :create, :destroy]
```

##Callbacks
_Callbacks allow you to trigger logic before or after an alteration of an object's state._
- Example: `before_validation :ensure_login_has_a_value`
- Example: `after_validation :set_location, on: [ :create, :update ]`

####_Available Callbacks_

#####Creating an Object

- `before_validation`
- `after_validation`
- `before_save`
- `around_save`
- `before_create`
- `around_create`
- `after_create`
- `after_save`
- `after_commit/after_rollback`

#####Updating an Object

- `before_validation`
- `after_validation`
- `before_save`
- `around_save`
- `before_update`
- `around_update`
- `after_update`
- `after_save`
- `after_commit/after_rollback`

#####Destroying an Object

- `before_destroy`
- `around_destroy`
- `after_destroy`
- `after_commit/after_rollback`


##Filters
_Filters are methods that are run before, after or "around" a controller action._

- Example: `before_action :require_login`
- Example: `skip_before_action :require_login, only: [:new, :create]`
- Example: `around_action :wrap_in_transaction, only: :show`


##FactoryGirl
- Defining a factory within Book model:
```ruby  
FactoryGirl.define do
  factory :book do
    name "House of Leaves"
    author "Mark Z. Danielewski"
    description "House of Leaves is the debut novel by the American author Mark Z. Danielewski, published by Pantheon Books. The novel quickly became a bestseller following its release on March 7, 2000. It was followed by a companion piece, The Whalestoe Letters"
  end
end
```
- Using in testing:
```ruby
describe Book do

  describe "validations" do
    it "is valid" do
      expect(create(:book)).to be_valid
    end

    it "is invalid without a name" do
      expect(build(:book, name: nil)).to be_invalid
    end

  end
end
```

- old way: `@book = Book.create(name: "", author: "", description: "")`
- factory way: `@book = create(:book)` or `10.times { create(:book) }` or `@unsaved_book = build(:book)`
