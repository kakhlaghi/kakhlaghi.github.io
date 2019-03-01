---
layout: post
title:      "Deployment: React App with Rails API via Heroku"
date:       2019-03-01 16:57:20 +0000
permalink:  deployment_react_app_with_rails_api_via_heroku
---


Allright, let's be real, while having your projects available to be cloned on github is nice, there is something better: Deploying the projects and having a URL with which you can access the project. 

There are multiple services that you can use with free and paid plans. AWS (Amazon Web Service) and Heroku are very popular choices for their ease and affordabilty. Both of these services have their pitfalls and their perks. This post will cover my app deployment through Heroku specifically. The app is called Idea-Me and is made of a React/Redux Front-end with Rails Api Back-end.

**But... Like... What is Heroku?**

Heroku is one of the first cloud PaaS (platform as a service). It started off supporting Ruby exclusively in 2007. Now it supports multiple languages and is considered a polygot platform. It pretty much can handle everything you use for web apps and it keeps the manner in which you deploy fairly similar accross languages.

Signing up as simple as signing up for most other services, so I will leave that to you... Once you have an account you'll be able to deploy.

The basic strategy for deploying a React front Rails back app is to deploy two "separate" apps.

**Back to Front: Rails deployment**

I started with the backend for my app. I used the [getting started with rails page](https://devcenter.heroku.com/articles/getting-started-with-rails5) to guide me through it.

**NOTE** for ruby on rails deployment, you do not have to use the CLI. This can save some time in getting it to work. Especially if you are using a virtual machine running ubuntu/linux like I am. 

The biggest the task for me and rails deployment was making sure I am using Postgresql. Heroku doesn't like using SQLite ([more on this](https://devcenter.heroku.com/articles/sqlite3)). Unfortunately for me, I used SQLite when developing. Meaning, I had to covert it to postgresql. If you are planning on deploying with Heroku, save yourself the trouble and develop with postgresql.

Changing the database is outlined on the getting started page and can be reduced to:

-changing the gems ```gem 'sqlite3'``` to ```gem 'pg'```. Bundle install!

-adding the postgresql adapter to the config/database.yml page under default. The getting started page has a great example that I copy&pasted. I went back and changed the name of the database on the appropriate lines to one that I created in psql for the purposes of this app and added a ```host: localhost``` line to the test and development areas. Rake db:reset or rake db:migrate your new database. 

-specify your ruby version. This can be weird using rvm. I was using ruby 2.3.6 but Heroku wants a more recent version or it will not build. I changed the gemfile to specify ruby 2.5.3, installed it, but it gave me some error saying that I was using 2.3.6 and specified 2.5.3... right... this is a rvm thing. Basically you can use ```rvm use 2.5.3``` in terminal to tell the app what to use.

At this point you're pretty much ready to "create an app" on Heroku.

Now this next bit.. I love this about Heroku. Like I said, I didn't have to use the CLI because I linked the github repo to the setup. Now everytime I push changes to my repo on github heroku will auto build the changes into the app. I used this github connection to deploy the app as well. By the way this is essentially what you need to do for a ruby on rails app. It rocked. It rocked hard. 

Look there will be errors that you'll face that I won't go over... mainly because I probably didn't face them. There are subtleties to adjusting an app to deploy. This is also true for the frontend... speaking of which...

**The Frontlines: Reactjs**

Simply put, there are a few minor things to change. For one of them I had to use the CLI.

1) ```heroku keys:add``` this adds an SSH key to heroku for you. (this needed the CLI)

2) add: 
```"engines": {
    "node": "7.8.0",
    "npm": "4.2.0"
  }```
	
3) add: ```"react-scripts": "1.0.7"```

4) change all the RESTful calls to the backend that is now on Heroku: http://exampleapi.herokuapp.com 


5) add the app/push the changes

<iframe src="https://giphy.com/embed/bIyQjsWT2DFfO" width="240" height="159" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/plackers-bIyQjsWT2DFfO">via GIPHY</a></p>



That's this gist! I really hope that helps. Take it easy and you will make it through.


[MY APP](http://ideameapp.herokuapp.com)

BONUS:

If you're like me you're using the free plan on Heroku. When I deployed the react side of my app I kept getting an R14 error that said my memory usage was too high! OH NO! WHAT!? Turns out that you get 1 dyno to handle your app on the free plan. This causes my app to behave atypically a fair amount of the time. New posts wouldn't immediately appear after getting added some of the time... some of the time it worked perfectly. Refresh and it's there. It's unfortunate... but explainable. Until I can reduce my memory usage or I feel like upgrading my plan, I'll have to explain/demo the app in a very controlled way.


