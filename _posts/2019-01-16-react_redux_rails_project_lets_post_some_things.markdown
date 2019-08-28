---
layout: post
title:      "React/Redux + Rails Project: Let's Post Some Things"
date:       2019-01-16 17:48:09 +0000
permalink:  react_redux_rails_project_lets_post_some_things
---


There are three Rs to this big boi of a project: React, Redux, and Rails.

The project is called: IdeaMe. It's an idea board that allows you to put Post-it like notes with a title and body onto a central board on the home page. LET THE CREATIVITY FLOW!

Let's get into some aspects of the app divided by framework:

React/Redux:
- Single page app! (How does it feel to do so much without refreshing the page? Real nice.)
- Lightweight
- Clear and clean organized
- Stateless components
- Containers
- React Routing
- more!

Rails-API:
- Data storage in a lightweight separate app
- SQL based tables and data manipulation
- Good ol' ActiveRecord

Ok so what is the app like really?

Well, in this project I wanted to keep it simple: Show all the posts on the homepage organized by most recently created. Give a guest the ability to add more posts.

How? Well there is a lot going on. So in this blog post, I'd like to focus on the main thing that I heard some colleagues have some issues with: how to bridge the gap between the API and the Web app.

The rails-api is set up with a Post table that has a `title:string` and `body:string`. At a basic level you need index, create, show methods for Posts, and make sure that you are rendering the data as json.
Here is an example of the create method:

```
def create
    @post = Post.new(post_params)
    if @post.save 
        render json: @post, status: 200
    else
        render json: {message: comment.errors}, status: 400    
    end
end    
```

An important note is how we are using post_params!
```
def post_params
    params.require(:post).permit(:title, :body)
end
```

When you test this out in Postman (which I highly recommend) the data you send should be something like:
`{ post: { title: "some title", body: "some body"} }` 
... your POST request requires `:post`, and then within those `:title` and `:body`. So that is the same formatting for the fetch POST request when you make it.

Now, to bridge that pesky gap.

I think there is a tendancy to overthink the connection between the rails api and the react app. In reality, all you are doing is POSTing and GETting to and from the API using the React/Redux side as the window. 

	

```
fetch('http://localhost:3001/api/v1/posts',
    {
        method: 'POST',
        mode: 'cors',
        headers: {
            "Content-Type": "application/json",
            // "Content-Type": "application/x-www-form-urlencoded",
        },
        body: JSON.stringify(post)
    })
    .then(response => response.json())
    .then(newPost =>{ 
        dispatch({type: 'ADD POST', payload: [newPost]})
    })
    .catch(error => console.log(error))
} 
```
		
		
That fetch request, and others like, is how you will communicate with your API. The API is just a storage mechanism that opens itself up when we run the server. The React/Redux side, as complicated as it is, is the interface through which the user can interact with that storage. 
    
This is POST request that my app uses to add a new item to the api. It should look pretty familiar if you have some experience with JavaScript. With some additions like the dispatch line. This is how the React/Redux operates: The action type dictates manipulation of the state defined in the reducer:

something like:
	
``` 
case 'ADD POST':
    return{
        ...state,
        posts: [action.payload, ...state.posts],
        loading: false
    }
```
                    
So we take the payload and add it to the posts which is in the defined state. It's now accessible by components that are linked to the store! 
        
There is a lot that goes into the building of a full application, even one as simple as a text posting app. 
When in doubt do the simplest things you know and go from there. Like a simple POST or GET request to your API. I hope this helps to get some clarity regarding in a realm full of possibilities!

Get Coding!
		
	
	

		



