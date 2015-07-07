1. Add gem to development section `gem 'rspec-rails', '~> 3.0'`
2. `bundle`
3. `rails generate rspec:install`
4. To generate tests for Album model `rails generate rspec:model Album`
5. Go to spec -> model -> album_spec to edit specs
6. Add `--format doc` to .rspec file to see words