---
layout: post
title:      "setState: a Closer Look and Callback Example"
date:       2019-02-19 03:45:08 +0000
permalink:  setstate_a_closer_look_and_callback_example
---


I came across something when working on a feature to my app [IdeaMe](https://github.com/kakhlaghi/IdeaMe).

I was implementing a simple voting system on my idea posting app: up arrow to increase votes, down arrow to decrease votes. The API is built in Rails, this is where I'm sending the votes, and the front end is React/Redux. I'm going to focus more on this specific instance of setState rather explain the voting mechanism, but look out for a future blog post about setting up that mechanism later.

The PROBLEM:
Trying to change the state containing the votes to send to the fetch request action. The state needs to change so that I can use it in my update (PUT) action in my API. The change of state should rerender the page with the appropriate new amount of votes. The reducer for my 'ideas/posts' will handle the info from the action. If this doesn't all make sense focus on the main problem: I need to change the state for the fetch request.

So, I know what you are thinking: just... use setState. Well I tried that!

```  this.setState({
            votes: this.state.votes + 1
        })```
				
A simple setState that can normall get the job done. NOT THIS TIME. This time, the API action would go through. The vote changed on the backend... BUT.. the frontend did NOT update. You'd have to hit refresh... and who wants that? No one.

<iframe src="https://giphy.com/embed/YOmera3t2deNi" width="480" height="294" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/reaction-doctor-who-why-YOmera3t2deNi">via GIPHY</a></p>

Yeah. That's what I thought. Well let's look at the [documentation](https://reactjs.org/docs/react-component.html#setstate).. let's see.. OH:

> Think of setState() as a request rather than an immediate command to update the component. For better perceived performance, React may delay it, and then update several components in a single pass. React does not guarantee that the state changes are applied immediately. ... setState() does not always immediately update the component. It may batch or defer the update until later. This makes reading this.state right after calling setState() a potential pitfall. 
> 

Oh OK. So, React can delay my state change for better perceived performance...meaning that it just won't happen. Hm.. so what do I do?

>
Instead, use componentDidUpdate or a setState callback (setState(updater, callback)), either of which are guaranteed to fire after the update has been applied. If you need to set the state based on the previous state, read about the updater argument below.
>

Let's recap for a sec. React can postpone setState to optimize performance. If you call this.state right after setState() it probably won't work. I'm trying to call this.state right after setState() in order to give the action the new state to PUT into my API. According to the documentation, componentDidUpdate or a setState callback should fix this.

I tried the componentDidUpdate, but unfortunately that leads to an infinite loop of updates. That's why react also has shouldComponentUpdate. The shouldComponentUpdate would allow me to avoid loops by telling the app a boolean (false to not update or true to update). This can help unnecessary rendering. BUT even react says that this is more of emergency hatch situation. I want to use this vote feature regularly... and last time I checked, you shouldn't use emergency hatches regularly. Plus, there ended up being a much more elegant solution to this problem.

Instead of using a bunch of methods or something, wrap the return value of setState in a callback function. Doing so will get the update to happen after the setState so you can access it immediately.

A callback with setState is straight forward, lightweight, just all around pretty great.

basic format: this.setState(updater, callback)

In action. *denotes callback setState

```addVotes = (event) => {
        event.preventDefault();
        let newVotes = 0
        if(event.target.className == 'upvote'){
            newVotes = this.state.votes + 1
        } else {
            newVotes = this.state.votes - 1
        }    
        *this.setState({ votes: newVotes}, () => this.props.votePost(this.state))

    }```
		
The updater here is a key value pair assigning the value of newVotes to the key of votes (which is initially set to the current value of votes belonging to that post). The callback that follows is an arrow function that holds the method belonging to the action "this.props.votePost" into which we are passing "this.state" which is updated from the upater.

Elegant. Exquiste. Sophisticated. Calvin Klein.

<iframe src="https://giphy.com/embed/V2AkNZZi9ygbm" width="480" height="203" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/will-ferrell-zoolander-V2AkNZZi9ygbm">via GIPHY</a></p>

See you next time!
		
