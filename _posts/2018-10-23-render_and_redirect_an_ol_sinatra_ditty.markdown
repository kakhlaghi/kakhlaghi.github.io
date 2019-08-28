---
layout: post
title:      "Render and Redirect: an ol' Sinatra ditty"
date:       2018-10-24 01:28:13 +0000
permalink:  render_and_redirect_an_ol_sinatra_ditty
---


Sinatra, a great singer, and also a powerful [domain specific language](https://en.wikipedia.org/wiki/Domain-specific_language) for web development. For this post, I'd like to focus on the latter of those statements. Specifically, I'd like to talk about rendering and redirecting using Sinatra. This post assumes some prior knowledge of coding, and Sinatra in general, but I hope it proves to be useful, even with limited knowledge.

**What is Rendering?**

Whenever you click a link or navigate your web browser to url, you are asking the web to do a few things: 

1) Gathering the data (if there is any data to gather)
Data in this case can take some different forms. For example, when you sign up as a new user on a website, you are filling out a form in a view (or template) with data. When you submit your form, you are sending that data out to be gathered. 

2) Load the associated views
Views are what the user...views. Look at a page. All the things you see are associated with a file of code behind the scenes. That file is the view. When we are rendering, we are taking the user request and navigating through a route to a view. 

3) Apply data to the views
A view when rendering can refer to code from the controller in order to present data or stored information to the user. For example, a profile page on Facebook probably uses a lot of the same code, but fills it in with information dynamically. By dynmically, I mean that it is presenting data unique to each user: your username, your photos. Now, you're not providing all of that every time, but Facebook has stored those items and associated it with your account. So, when you send in that login data, the controllers can take that provided username and password and find the associated information, and then give that to the view page so it can render everything.

4) Send the output to the page/user
Now that the view has all of this data loaded in, we have the code that displays it all using another view and some mediation by a controller method route.

...all of these comprise the process of rendering. The user makes a request (clicks a link, types in a web address), this request goes to our controller's appropriate route method, follows the code until we reach the next view. 

Here is an example"

Example of a view:
```
<h2>The simple place to let people know what you can cook</h2>

<% if logged_in? %>

  <p>Welcome, <%=current_user.username%></p><br>
  <a href="/dishes/new"> Create Dish </a>
  <a href="/chefs/<%=current_user.slug%>">View your dishes!</a>

<% else %>
<h2>Welcome, New User</h2>
  <a href="/signup">Sign Up</a>
  <a href="/login"> Sign In </a>
<%end %>
```
  
Example of a route in a controller
  
```
get '/signup' do
  if !logged_in?
    erb :'chefs/create_chef'
  else
    redirect to '/dishes'
  end
end
```
	
The example of the view I provided is from a home page or view. If we look at the line `<a href="/signup">Sign Up</a>` navigates to	`get '/signup' do`... so we have one view calling the route method get `'/signup'`. Get in this case means that we are going to display something at `https://www.someaddress/signup`. What are we displaying? The signup page. This is represented by another view chefs/create_chef. By calling erb on that address we are saying go do this view, and bring with it any instantiated data from the controller route/action. So, for example, if we put `@dishes = Dish.all` after the `get...do` we would be able to access `@dishes` in the erb view. For signup, it's not necessary, but if we wanted to display all the dishes beloning to a chef. We would need to make that data available often using a post as opposed to a get method. What is post? Briefly, you can think of post creates while get displays.


**What is Redirecting?**

We saw with rendering that there is some navigation between routes and views. In the above example we can see some of that navigation. In the view example provided above, the `<a href='/signup' ...` line is the link to the route example `get '/signup'` /signup to /signup. But look at the line after else `redirect to '/dishes'`... hm couldn't we use erb to render the dishes page? Well, we could. Sure. Yeah. 

BUT here's why not.. we don't always want that instantiated data. What if we were going to another completely unrelated page? Do you want to bring all of that data with you? Nope.

`Redirect_to` sends a new request to another URL without bringing along any instantiated variables created from the the controller route that is reqesting the redirect. It's essentially saying "OK we are done here leave everything behind and go to this new place". 

In very brief summary: Render keeps the data from the controller so you can access it in the view page your user requested. Redirect just goes to that next view without bringing data or variables defined in the controller.




