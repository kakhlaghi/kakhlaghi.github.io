---
layout: post
title:      "Rails Project: the Return of Chefs Club."
date:       2018-11-14 15:46:49 +0000
permalink:  rails_project_the_return_of_chefs_club
---


In a previous project, I made a Sinatra app called Chefs Club. It allowed registering as a user/chef and posting dishes that belonged to that chef. 

What was it missing?
Well while we knew what dish the chef could make, we didn't really know anything about it. For example: ingredients.

It might seem like an easy task to through in another model, but the ingredients model presented an interesting series of challenges for this project. There were many, but, I want to focus in on 3 that cover a wide range of new features in Chefs Club.

New Challenges:
1. A many_to_many relationship between Dish and Ingredient. Every dish has many different ingredients, but we also want every ingredient to have many dishes. This many_to_many relationship allows the association of ingredient to dish without creating excess ingredients. To accomplish this relationship we will need a Join Model -- Dish_Ingredients.

2. Another thing that I'd like to do is use ActiveRecord:Scope methods to look through my dishes and organize them a bit. 

3. Finally, I don't want to make my users remember a password. We are going to let them sign in using github!

**First: the Join Model**

A join model is a magical way for two models to have a 'many_to_many' relationship.

```class Dish < ActiveRecord::Base
  has_many :dish_ingredients
  has_many :ingredients, through: :dish_ingredients
  belongs_to :chef
	
	class Ingredient < ActiveRecord::Base
  #has_many :chefs, through: :dishes
  has_many :dish_ingredients
  has_many :dishes, through: :dish_ingredients
	
	class DishIngredient < ActiveRecord::Base
  belongs_to :dish
  belongs_to :ingredient, required: false```
	
	Take a second and look at the three models I've presented. It's pretty intuitive which of these is the join model, but in case you're not sure: its DishIngredient. Note that the Dish and the Ingredient models has_many of each other *through* the join model DishIngredient.
	
	What's actually happening: by declaring the relationships has_many, and has_many through, we are saying the models are connected. This declaration allows streamlined matching of a model to another model, or in the case of has_many through: model to model through another model.
	
	If you aren't familiar with associations (e.g. has_many, belongs_to, etc.) check out this wonderful guide: [rails guide on associations](https://guides.rubyonrails.org/association_basics.html#the-has-many-through-association)
	
**Second: the Scope Method**

The dishes in a list is ... fine. But, I wanted to add a bit more organization. Each dish comes with a user submitted cook time as a float when created. We can use that to help organize our dishes.

Take a look at these scope methods:

```  scope :less_than_30, -> {where('cook_time <= 30')}
  scope :more_than_30, -> {where('cook_time > 30')}
  scope :less_than_60, -> {where('cook_time <= 60')}
  scope :more_than_60,-> {where('cook_time > 60')}```
	
	These scope methods named: less_than_30, more_than_30 and so on, provide methods to query and retrieve all dishes that have a cook timeless than or equal to whatever: {where('cook_time <= 30')} for example.
	
	Then you can call those methods when you want to use them.
	
	```<% @chef.dishes.less_than_30.each do |dish| %>```
	
	This line of code begins an interation over all of the dishes belonging to the instantiated chef that have a cook time of less than or equal to 30. The scope method allows you to select instances in the database you set up given a parameter you've described. I used these scope methods to organize the dishes in to "short", "medium", and "long" cook times. 
	
	**Third: Github Login!**
	
	This is a surprisingly easy, useful, and now pretty common feature to an app. I used [Omniauth](https://github.com/omniauth): a very useful gem for 3rd party login features. I chose omniauth-github mainly out of conveinence. 
	
	The steps for utilizing omniauth with github are as easy as:
	
* Add the gems: Omniauth and Omniauth-github to your gemfile
* Set up your application on github
* Create a .env (optional and requires adding the gem dotenv-rails to your file, but good practice!)
* Copy the key and secret from your github app page to your .env file
* Add the authoritization to your siginin path

The first step is easy enough, but the second step: On your github page, navigate to your settings page by clicking your icon in the top right corner and finding "settings". Scroll until you see developer settings in the far left column. Click that! You should now see a page call Oauth apps. Go ahead an add your app by clicking "New Oauth app". Fill in the necessary info. 

If you are developing your app still you can use localhost:3000. Also, for that callback url: localhost:3000/auth/github/callback. You can change your urls later.


Once you've set up the application on github, you'll see a page describing your app. Notice the lines:
 Client ID : some long string
 Client Secret: another long string
 
 Those long strings are unique to your app and are an integral part of this process.
 
 *.env*
 Create a file called .env in your root folder.
 Inside that paste your Id and secret numbers (samples shown below)
 
 GITHUB_KEY=247632982388118
GITHUB_SECRET=01ab234567890c123d456ef78babc901

The .env paired with the dotenv-rails application creates a secure way of keeping that information so anyone view your source page doesn't have access to those strings.

*config/initializers/omniauth.rb*
In the above path (you will probably need to create a file called omniauth.rb) add: 
```
Rails.application.config.middleware.use OmniAuth::Builder do
  provider :github, ENV['GITHUB_KEY'], ENV['GITHUB_SECRET']
end```

*config/routes.rb*
In your routes folder add a path to your callback (remember this from the Oauth setup page on github?)

probably like this:
 ```get '/auth/:provider/callback' => 'sessions#create'```
 
And then in what ever view you want your user to be able to login through github (probably your home page):
```<%= link_to('Log in with Github!', '/auth/github') %>```

The final thing you need to do is to build out your user and session models. The session model will refer to the auth code and do the "heavy lifting for the user".

```class SessionsController < ApplicationController
  def create
    @user = User.find_or_create_by(uid: auth['uid']) do |u|
      u.name = auth['info']['name']
      u.email = auth['info']['email']
      u.image = auth['info']['image']
    end
 
    session[:user_id] = @user.id
 
    render 'welcome/home'
  end
 
  private
 
  def auth
    request.env['omniauth.auth']
  end
end```

This example is pretty generic. Auth in this case contains all the information provided by github about our user. We then assign the aspects we care about to the user model (name, email, image). Note that the user has to contain these fields already to fill them in this way (all of which are strings).

There you have it!
A bit more lengthy then the other challenges but worth it! 

Chefs Club with new features... Done!

![](https://memegenerator.net/img/instances/61660649/done.jpg)



 
 
 







	
	
	

	
	
	
	




