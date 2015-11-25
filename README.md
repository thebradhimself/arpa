#Ar

Authorization Gem for Ruby and Ruby on Rails projects.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'ar'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install ar

After you install Ar and add it to your Gemfile, you need to run the generator:

    $ rails generate ar:install
    
This command will create some files that are needed to run the Gem.

|    File  |     Purpose   |
|----------|:-------------:|
| db/migrate/20140120201010_create_ar_tables.rb |  Migration to create the all **Ar** tables in your database (your name will include a different timestamp) | 
| config/locales/ar.en.yml |  Locales to use in Ar classes | 
| app/assets/stylesheets/ar/ar_accordion.scss |  Basic stylesheet to use with Ar views | 
| app/controllers/ar/resources_controller.rb  app/controllers/ar/roles_controller.rb  app/controllers/ar/profiles_controller.rb | Controllers to use the CRUD actions for each one |
| app/views/ar/resources/  app/controllers/ar/roles/  app/controllers/ar/profiles/ | All views to use the CRUD actions for each controller above |
| config/routes.rb |  Will add all routes into this file with all resources of Ar | 

After generate, you need to run the migration to create all Ar tables:

    $ rake db:migrate

**Obs.:** The migration file will create a associate table between **Profiles** and **Users** (the Users must exist in your Application before adding the Gem)

## Usage

First of all you must create the Resources, Roles and Profiles (each is avaliable in the paths listed above). After that you need associate **Profiles** with **User** (to do this, you need create by your own the associate form view, saving some profiles in some user). Done that you can use some Helpers generated by Ar. 

### Association between Profiles and Users

You just need have a method called **:profile_ids** inside the User model. This method should return a list of ids from profiles associated in the user.

You just add a HBTM association in User model:

```ruby
class User < ActiveRecord::Base
	has_and_belongs_to_many :profiles, class_name: 'Ar::Repositories::Profiles::RepositoryProfile'
end
```
With this you will be able to use the :profile_ids method.

### Controller helpers

Ar will create some helpers to use inside your controllers and views.

To verify if a user has access to some :controler and :action, use the following helper:

```ruby
has_access?('users', 'index')
```
**Obs.:** To that helper method works. You must have **:session** (In Rails app already has) and **:current_user** attribute or method.

---
If you want use that method inside another object you should use the **Ar::Services::Verifier** class;

You just need pass as arguments the :session and :current_user:

```ruby
verifier = Ar::Services::Verifier.new(session, current_user)
verifier.has_access?('users', 'index')
```

### Controller Filter

If you want create a filter to verify if the current_user has access and if not redirect to another route you can do this:

Create a method in ApplicationController and add as a before_filter callback from rails:

```ruby
class ApplicationController < ActionController::Base
	before_filter :authorize_user  
	
	 def authorize_user
    	controller = params[:controller]
    	action     = params[:action]		
      redirect_to some_url unless has_access?(controller, action
	 end

end  
```

**Obs.:** The **has_access?** method come from Controller Helper method which Ar gem has been created.


## Information

After generate, you will be able to access some paths for each Controller created:

```ruby
generate_resources_and_actions_resources GET    /resources/generate_resources_and_actions(.:format) ar/resources#generate_resources_and_actions
                               resources GET    /resources(.:format)                                ar/resources#index
                                         POST   /resources(.:format)                                ar/resources#create
                            new_resource GET    /resources/new(.:format)                            ar/resources#new
                           edit_resource GET    /resources/:id/edit(.:format)                       ar/resources#edit
                                resource GET    /resources/:id(.:format)                            ar/resources#show
                                         PATCH  /resources/:id(.:format)                            ar/resources#update
                                         PUT    /resources/:id(.:format)                            ar/resources#update
                                         DELETE /resources/:id(.:format)                            ar/resources#destroy
                                         DELETE /roles/:id(.:format)                                ar/roles#remove
                                   roles GET    /roles(.:format)                                    ar/roles#index
                                         POST   /roles(.:format)                                    ar/roles#create
                                new_role GET    /roles/new(.:format)                                ar/roles#new
                               edit_role GET    /roles/:id/edit(.:format)                           ar/roles#edit
                                    role GET    /roles/:id(.:format)                                ar/roles#show
                                         PATCH  /roles/:id(.:format)                                ar/roles#update
                                         PUT    /roles/:id(.:format)                                ar/roles#update
                                         DELETE /roles/:id(.:format)                                ar/roles#destroy
                                         DELETE /profiles/:id(.:format)                             ar/profiles#remove
                                profiles GET    /profiles(.:format)                                 ar/profiles#index
                                         POST   /profiles(.:format)                                 ar/profiles#create
                             new_profile GET    /profiles/new(.:format)                             ar/profiles#new
                            edit_profile GET    /profiles/:id/edit(.:format)                        ar/profiles#edit
                                 profile GET    /profiles/:id(.:format)                             ar/profiles#show
                                         PATCH  /profiles/:id(.:format)                             ar/profiles#update
                                         PUT    /profiles/:id(.:format)                             ar/profiles#update
                                         DELETE /profiles/:id(.:format)                             ar/profiles#destroy
```

## License

MIT License. Copyright Rachid Calazans.
