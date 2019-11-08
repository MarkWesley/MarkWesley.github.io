---
layout: post
title:      "Object Oriented Ruby and the Single Source of Truth"
date:       2019-11-08 00:25:43 +0000
permalink:  object_oriented_ruby_and_the_single_source_of_truth
---

I'm writing this post coming off the heels of my first project. In this project, I used an API to create a CLI Gem that allows the user to look through genres of TV shows to find one they are interested in, drill down further to find all the shows for that genre, and take it one step further to find an overview of each of those shows. This was my first time working with an API and it was definitely a challenge.

In order to build the functionality of this program, I had to tie together two separate API requests. The first was a list of popular TV shows, which showed a name, a genre ID, and an overview among other details. The second was for TV genres, which returned both a name and a genre ID for each genre, but did not contain a list of shows for each of these genres. So how to tie them together?? I know that each TV show has many genres - for example it could be a crime show that is also a drama. This was noted by a genre ID, but not by a genre name. And I know that each genre has many shows, but these aren't represented in the original API request - only a genre ID. 

During my review, I was asked to find a way to show that each object had many instances of the other object - and then it hit me. Both of these objects could have many instances of the other through a third class - a single source of truth.

So I set about refactoring my code to show just this. I built a third class called "tv_genres" that is initialized with a genre and tv show. From there, I was able to link the genre class to the tv shows class THROUGH the tv_genres class using the following code:

    def tv_genres 
        TV_Genres.all.select {|tv| tv.genre== self}
    end

    def tv_shows 
        tv_genres.map {|tv| tv.tv_show}
    end
		
The first method looks through all instances of the tv_genres class and finds all instances that have the same genre as the instance of the `genre` class that we are calling the method on. The second method then looks through those matching tv genres and returns a list of tv shows that belong to that specific genre.
		
I was also able to link the tv show class to the genre class using the following code:
		
	  def new_tv_genre(genre)
        TV_Genres.new(genre, self)
    end

    def tv_genres 
        TV_Genres.all.select {|tv| tv.tv_show == self}
    end

    def genres 
        tv_genres.map {|tv| tv.genre}
    end
		
The first method here is very important. It creates a new instance of tv_genres with an argument of a genre and the instance of the tv show itself, tying together the tv show with the genre that it belongs to. Without this method and the tv_genres class as our single source of truth, a tv show cannot know its genre because it only initialized with a genre ID. 

The second method looks through all instances of the tv_genres class and finds all instances that have the same tv shows as the instance of the tv show class that we are calling the method on. From there, the final method looks at the matching tv show and returns a list of all genres that belong to that show. 

After adding in the tv genres class, I cleaned up the code in my CLI class, getting rid of some unnecessary instance variables and condensing multiple instance methods into shorter and more concise methods, effectively making the code easier to read when someone else looks at my program. This whole process has been a huge challenge, but I am happy that I was asked to dig in a little more because it's only helped to solidify my knowledge. 
		



