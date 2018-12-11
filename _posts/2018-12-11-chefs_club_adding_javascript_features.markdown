---
layout: post
title:      "Chefs Club: Adding JavaScript Features"
date:       2018-12-11 22:44:33 +0000
permalink:  chefs_club_adding_javascript_features
---


Let's face it, even though Chefs Club on rails dwarfed the OG Chefs Club, it could use a little flash and substance. 

So, I spent some time on adding some.

By using JavaScript and/or JQuery we can add powerful features. A few of the ones I focused on were:

1) A "Next Dish" link that displays the next dish belonging to the chef without having to refresh the page.
2) A "Chefs Library" that shows all the members.
3) A "Favorite Dish" list for quick access to the chefs favorite dishes.

**1. Next Dish Please**
The next dish link is one of the simpler things to acheive at its core. You need the function, the link on the page, and some dummy data. What makes this one special is the fact that we don't have to reload the page. 

The link, like any link, needs to do something when we click on it. But instead of routing to another url, or sending a new html request, I want the link to simply run a function, which will get information about the next dish object and display it on the current URL.

easy link: ```<a href="#" class="js-next" data-chef="<%=@dish.chef.id%>" data-id="<%=@dish.id%>">Next Dish</a>```
Notice that i've given the anchor (link) a few different ids. "data-"  allows you to set custom ids for your elements, which can be handy to refer to when building your fucntion... speaking of which...

Take a second and read the code, and see what you can gather:

```<script type="text/javascript" charset="utf-8">
  $(function() {
    $(".js-next").on("click", function(event) {
      event.preventDefault()
      let chefId = parseInt($(".js-next").attr("data-chef"));
      let nextId = parseInt($(".js-next").attr("data-id")) + 1;
      console.log("dish id value" + nextId)
      $.get("/chefs/" + chefId + "/dishes/" + nextId + ".json", function(data){
        let dish = data;
        console.log(dish)
        $(".dishName").text(dish["name"]);
        $(".dishCookTime").text(dish["cook_time"]);
        let i;
      for(i=0;i<dish["ingredients"].length;i++){
        $(".dishItem").text(dish["dish_ingredients"][i]["quantity"]);
        $(".dishItem").append(dish["ingredients"][i]["name"]);
        console.log("i value" + i)
      }
      $(".js-next").attr("data-id", dish["id"]);
      })
    })
  });
</script>```

The core characteristics of this function:
JQuery built in the view using the 'script' tag.

The function waits for the link of the class ".js-next" to be clicked. After that click, we grab the "chefId" and the "dishId/nextId" throught the link itself (see how handy the data- custom identifiers are?).  From there, the .get() method grabs the .json data from the url string constructed. That (data) is passed into the callback function chained to the get method. That function grabs the dish["name"] from the data (assigned to dish) and puts it into the appropriate div classes on the view page. Finally, we update the js-next id to the current dish to continue scrolling.

<iframe src="https://giphy.com/embed/XtdFIKkx6irtv4eWX4" width="480" height="270" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/arianagrande-ariana-grande-thank-u-next-XtdFIKkx6irtv4eWX4">via GIPHY</a></p>


**2. Chefs Library: Who's a member?**

The chef library displays the name of each chef that is in the database. That's simple enough, and doesn't really require heavy duty javascript or really much of anything: Essentially you take all the chefs in the controller and ...

```<h1>All the Chefs</h1>
<% @chefs.each do |chef| %>
    <br>
    <a href="/chefs/<%=chef.id%>"><%=chef.name%></a>
    <br>
    <div id="chef-<%=chef.id%>">
    <br>
    <button id="<%=chef.id%>" class="js-more" onClick="fetchChefs(this.id)" data-id="<%= chef.id %>">More Info</button>
    </div>
    <br>
<%end%>``` 

presto.. oh but wait... there's a button. Yeah, that's what makes this library a little more special.

The .js-more button I've created runs a function a lot like the link from before, but this time we are calling the function "fetchChefs()" with this.id passing through it.

fetchChefs():
```function fetchChefs(clicked_id) {
	// ajax   '/chefs
	console.log("hit fetch")
	let id = clicked_id-1
	console.log(id)
	$.get("/chefs.json", function(data){
		let chefs = data;
		let chef = chefs[id];
		let dishes = chef["dishes"]	
		let newChefText = "";
		let dishList = "";
		if(dishes == 0){
			newChefText = "<strong>New Chef!</strong>";
			$("#chef-" + clicked_id).html(newChefText);
		} else{
			dishes.forEach(function(dish){
				dishList += '<ol class="js-dish">' + "Dish Name: " + dish["name"] + " | Cook Time: " + dish["cook_time"] + '</ol>';		
			})
			$("#chef-" + clicked_id).html(dishList);
		}
	})
}```

The function gets the id of the button,gathers data from the chefs JSON, assigns it to some variables (just for the sake of cleanliness when we display the variables). The function checks to see if any dishes belong to the chef in question. If not, then it says "New Chef!". If the chef does have dishes, the function displays the dish information... but only then name and the cook time.

**3. Favorite Dishes**
Ok, so this one is the tough one. I wanted to use JavaScript to create a two buttons, a class called favorites, and a few functions (including one on the Favorites protype). The objective was to create a way to store a chefs favorite dishes in a session but click a "add to favorites" button, and then showing those favorites when they click a button on their profile page. As of writing this post, it does not work 100%. It accomplishes the goal, but only to a point.. so far you can add one favorite and then display that favorite using the button on the chef profile page.

Since I we've gone over some functions already, let's start with the class:

```class Favorites{
		constructor(){
			this.array = [];
			favoriteStore.faveDishes.push(this)
		}
	}

	Favorites.prototype.addToFavorites = function(dish){
		this.array.push(dish)
	};```
	
	The favorites class is constructed with an empty array. I also created ```let favoriteStore = {faveDishes = []}``` to hold on to the favorites in the storage. I probably could've just referred to the favorites.array when asking for the details to display. 
	
	Let's look at the function that's doing the heavy lifting:
	
	After clicking on: ```<button onclick="favoritesFetcher()">Add to favorites</button>```

This happens:	

```
	function favoritesFetcher(){
	let chefFavorite = new Favorites()
    const baseURL = 'localhost:3000'
	const url = window.location.href + '.json';
	let postData = {
		dish: document.getElementsByClassName('.dishName')
	}
	//get the data
	fetch(url)
		  .then(response=>response.json())
		  .then(json=> chefFavorite.addToFavorites(json))
		  console.log(chefFavorite)
	}```
	
	Then clicking on: ```<button id="favoritesHead" onclick="renderFavorites()"> Show Favorites </button>```

	This happens:
	
```	function renderFavorites(){
		let target = document.querySelector('#favoritesHead')
		let div = document.createElement('p');
		const favoritesArray = favoriteStore["faveDishes"][0]["array"];
		for(i=0;i<favoritesArray.length;i++){
			div.innerHTML = favoriteStore["faveDishes"][0]["array"][i]["name"];
		}
		target.parentNode.insertBefore(div, target);	
	}```

The first function creates a new Favorites class that is assigned to chefFavorite. I grab the current URL of that dish show page with ```window.location.href``` which is the url of the current page. Note that we are using ```fetch()``` here! Like I said I wanted to show off a little JavaScript. What's cool is that using fetch on the show page in JSON gives us all the data about the dish. The``` .then()``` statements push the response to json then to our prototype function on the model created earlier. The second function grabs the name (THOUGH WE COULD GRAB MORE!) and diplays the name of the dish and inserts it as an element on to the page. That element is created INSIDE THIS FUNCTION! I'm very excited about this because I love how powerful JavaScript is!

Adding features using a powerful language like JavaScript can really open up the efficiency and abilities of your application! 

Happy Coding!




