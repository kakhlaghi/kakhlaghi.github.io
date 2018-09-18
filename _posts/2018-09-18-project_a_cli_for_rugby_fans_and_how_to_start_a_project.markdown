---
layout: post
title:      "Project: a CLI for Rugby Fans (and how to start a project)"
date:       2018-09-18 19:43:44 +0000
permalink:  project_a_cli_for_rugby_fans_and_how_to_start_a_project
---


The haka, high-speed tackles, piles of massive men, incredible displays of athleticism and split-second decision making. This is Rugby.

For rugby fans, the All Blacks represent an incredible team of talented players stunning the world with their abilities, and breaking the hearts of other teams and their fans the world over.
For rugby fans from New Zealand, the All Blacks are an institution, a source of nationalistic pride, a public figure, and so much more.


<iframe width="560" height="315" src="https://www.youtube.com/embed/yiKFYTFJ_kw" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>



As sort of a tribute, I wanted to write a program that simply presented the basic information for each player of the current All Blacks squad.

Starting a project was always my weak point. There's something about the process of coding from scratch that didn't initially click with me. I wanted to get this project done effectively, but also quickly. 

Instead of immediately jumping into coding and writing what I already knew how to do, I decided to organize and try with this approach:

**Factor 1: What do I want this to do?**

Like I said, I wanted this to display the current All Blacks info for each player... yeah... but what does that mean?
I opened up a .txt document and started typing a sort of "table of contents" for my code:

Run Program
  * Greeting because it's nice
					    * Main Menu
							  * "List"  lists the full roster scrapped from webpage
							  * "Help"  describe program and give brief instructions
							  * "Quit"  exit the program
							
							* "List"
							  * Full roster  scrape from webpage
							  * Player info menu  scrape from webpage
							  *For chosen player: display player information, methods to store and display the information in a cohesive manner
				
							Return to Main Menu: Rinse and Repeat!
							
	The goal here wasn't to write out all of my code before testing it. It was to simply help me keep my project organized. The lines of definitions, methods, classes, if statements, all of the actual code can get removed from a project
							
	This is what I wanted to happen when a user used my gem. This list was my basic guide for the rest of my project. Each bullet point was a goal-filled with potential mini-goals. Now to coding!
	
	Lesson Learned: having a simple outline to refer to might seem tedious and unnecessary, but boy-howdy does it help.
	
**Factor 2: Starting the code**

I was able to proceed pretty steadily at the beginning. Defining my classes: a CLI controller, a scraper, and an organizer in three separate .rb (ruby) files.

The gems were pretty straightforward as well:
--Pry : because who doesn't love testing with pry.
--Nokogiri : because I wanted to scrape the All Blacks web page for the information.
--Require_all: because it's a wonderfully simple way to load all of my code.

Then came the actual doings of the code... let's take my "List" objective from earlier as an example. This was the meat of what I wanted to do because involved scraping the page for the names, which on the site also acted as profile links to more information about each player.

I considered what information I wanted to gather for each player and how I wanted to store it. Each player would have its own unique value belonging to the attributes were of the same category across player (e.g. every player has a name and a profile page): this I could do with a hash. This also meant that each player had multiple things that needed to be assigned to one container... an array of hashes. For "List" it would look like this:

[ {:name => "guy1", :position => "guy1's profile url"}, {:name => "guy2", :position => "guy2's profile url"}] and so on.

So now I had my list of players. I could display the names of all of them now. Instead of shoving the whole hash out of there. I thought to take the hash and ".send" it to my organizer class to transform it to an array with the information I wanted. I ended up making this how the organized class was initialized because this was the main proponent of that class. Each name of a player represented not only the value of the name, but also where I would assign the other 


**Factor 3: Scraping the website**

I really like the All Blacks website. It's straight-forward, descriptive, attractive, nice. I also like Nokogiri for similar reasons. I had anticipated that using Nokogiri was going to be the most difficult part of the project. It was challenging. But I realized that Nokogiri really respects what you tell it (pretty much like all coding). 

Inspecting the webpage thoroughly is a crucial part of scraping. It can make or break your code.

Lesson learned: that site I'm looking at has a specific path of tags to the information I want... I just got to get that path right. Find the tags; clean up the data; boom you're in business.

**Factor 4: The Mental game**

The mental game is often a key factor of most objectives. Trying and failing is a natural part of coding...and life... and sports. To learn from failure, and the ability to accept it over letting it overwhelm you is often difficult. There are times where you need to walk away and come back, or where you need to get back to basics. Even a project as simple as mine had its challenges that were frustrating, especially for me. 

Keeping a clear head can really help keep the experience of coding enjoyable. It seems like an obvious thing, but it is important nonetheless. 
							
