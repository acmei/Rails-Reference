Rails Cheatsheet
----------------

##Startup Rails App
1.  cd into project folder
2.  `echo 'ProjectName' > .ruby-gemset`
3.  `echo '2.2.2' > .ruby-version`
4.  cd out and back in to project folder (wrappers happen...)
5.  `rvm gemset list` (yes it's on the gemset we just created)
6.  `gem install bundler`
7.  `gem install rails --no-ri --no-rdoc`
8.  `git status`
9.  `git add .`
10. `git commit -m "Created gemset."`
11. `rails new .`
12. `git commit "fresh Rails install"`
13. `touch Gemfile` (if doesn't already exist)
14. open and edit `Gemfile` with gems, save
15. bundle

##Show All Routes
`rake routes`

##Make a Controller
`rails generate controller controller_name`
- Example: `rails generate controller tasks`
- convention says controller name is plural
  - i.e. ClientsController preferred over ClientController

##Resource Routing
#####_All routes_
`resources :labels`

#####_Only certain routes_
` resources :labels, only: [:index, :show]`

#####_All routes EXCEPT_
` resources :labels, except: [:index, :show]`

#####_Taking a Block_
```
resources :labels do
  # 8 more routes generated
  resources :albums
end
```

#####_Nesting Routes_
Shouldn't nest more than one deep

```
  resources :labels do
    resources :albums
    resources :artists do
      resources :albums, only: [:index, :show]
    end
  end
```

#####_Collection Routes_
```
resources :albums, only: [:index, :show] do
  collection do
    get 'released/:year', action: 'by_year', as: 'by_year'
  end
end
```
- `get 'released/:year' => 'albums#by_year'`
- `as: 'bye_year'` allows for `by_year_albums_path` for linking
- also have `by_year_albums_url` for linking offsite

##Install Rails
#####_Must be in project folder_
`rails new .`

##Heroku Deployment
1. Make sure current project is committed, `git commit -m "Your message here"`
2. `heroku create`
3. `git push heroku branch:master`
4. `heroku run rake db:migrate`
5. `heroku run rake db:seed`

##Add Column
`rails generate migration add_column_to_tasks columnname:string columnname2:integer`
