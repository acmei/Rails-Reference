Rails Reference
----------------

##Startup Rails App
1.  cd into project folder
2.  `echo 'ProjectName' > .ruby-gemset` 
    ex) `echo 'TaskListRails' > .ruby-gemset`
3.  `echo '2.2.2' > .ruby-version`
4.  cd out and back in to project folder (wrappers happen...)
5.  `rvm gemset list` (yes it's on the gemset we just created)
6.  `gem install bundler`
7.  `gem install rails --no-ri --no-rdoc`
8.  `git status`
9.  `git add .`
10. `git commit -m "Created gemset."`
11. `rails new .`
12. `git add .`
12. `git commit "fresh Rails install"`
14. open and edit `Gemfile` with gems, save
    - for better errors (in development section):  `gem 'better_errors'` & `gem 'binding_of_caller'`
    - for pry console (in development section): `gem 'pry-rails'`
    - if deploying to heroku: move sqlite3 gem to development, then add 
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


##RSpec Rails
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
_Make sure your sqlite3 gem is under development group then add gem 'pg'_
```ruby
group :production do
  gem 'pg'
end
```

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


## User Authentication
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

