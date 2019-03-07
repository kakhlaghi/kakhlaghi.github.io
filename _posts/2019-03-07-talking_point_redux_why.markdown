---
layout: post
title:      "Talking Point: Redux... Why?"
date:       2019-03-07 16:13:07 +0000
permalink:  talking_point_redux_why
---


This is going to be a quick post, but I think it's an important one. I had an assessment that looked into my react-redux app IdeaMe. Part of this assessment was to implement a new feature in the app using react exclusively. It was a weird experience that lasted probably like 5 minutes. But, it got me thinking: What is the real benefit of using Redux?

Redux is like an add-on to react. There is no necessity that comes out of it. What it does offer is simplification to a rather massive aspect of react: state management. React already manages state pretty darn well with its built-in toolset. However, once an app begins to get larger, we can see where redux becomes useful.

React allows data and state sharing between components, but in a way that can become problematic for larger apps. Simply put, data has to be passed through the parent component in order to reach sibling components. For simple parent-child-sibling relationships this is fine. However, the family tree can quickly grow complex. Several components would recieve data that is irrelevant to their purpose.

For example, look at your facebook home page... notice all those little versions of your profile picture that are spread out through different parts of the page. It could be the case that the image is stored as part of your user data. Then that user data has to be passed through the various componets containing those profile picture holders. In order to get to them, you might have to pass through other components like a navbar or sidebar or comment block... the potential for component weaving is pretty much limitless! So... redux offers a better way to handle it.

<iframe src="https://giphy.com/embed/3D5va69iZiwlLIZ6Lk" width="480" height="270" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/umbrellaacad-netflix-umbrella-academy-the-3D5va69iZiwlLIZ6Lk">via GIPHY</a></p>
(talk about complicated umbrella relationships)

**The Redux Way**
Redux provides a way to give every component direct access to the data by providing a store. The store... stores... the data. You use the redux [connect](https://react-redux.js.org/api/connect) function to bring that data from the store to the component of your choice. If that wording seems a bit odd... it's for a reason. The component is clueless about redux. What happens is a function called mapStateToProps grabs the data of your choice from the store (provided it is in the store) and maps that state based data to the props. Lastly, the connect function connects that mapStateToProps data to the component.

example mapStateToProps:

``` mapStateToProps=(state)=>({
   data: state.dataChoice
})

export default connect(mapStateToProps)(Component) ```

This is a very basic basic example, so see if you can plug an play your own variables!

From what I've read and experienced, this amazing data management process is Redux's forte. IF you don't really need this level of data manipulation and component connect, THEN maybe you don't really need redux. 

Right now, I'm working on a project right now, which I don't think needs redux... so, I'm challenging myself to do it both with and without redux. Look for that post later on!

Thanks for reading!

