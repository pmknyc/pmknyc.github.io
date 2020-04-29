---
layout: post
title:      "A Ruby CLI Application: Daily Science News"
date:       2020-04-29 11:00:16 -0400
permalink:  a_ruby_cli_application_daily_science_news
---

## What?  tl;dr

Daily Science News is a colorful yet serious little Ruby command line interface (CLI) application. It helps Users take a small daily “dose” of breaking news about real scientific discoveries as a cure for rampant science misinformation. This walk through a newbie coder’s first project build using Object Oriented Ruby may be helpful to others learning to code.

## Why?

I built this application as my first project in Flatiron School’s self-paced Software Engineering program‼ {trumpet fanfare}  The main educational goals were to use object-oriented Ruby to create a working application, preferably modeled as a [Ruby gem](https://rubygems.org/). A second learning goal was to provide the user with access to data from a website.

My vision for the application is to provide Users with quick access to learn about daily, evidence-based scientific discoveries. In the United States, we see increasing misinformation about science in public media resources, especially online. The problem is so serious that scientists not only are documenting it, they also are creating a new interdisciplinary[“science of science communication”](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6475368/) to research and improve how the public learns about and understands scientific knowledge and contributions to everyday life. As a biobehavioral researcher moving into a career in technology, I am keen on contributing to science education and communication. This project is a small step.

## Who? 

A typical User might be a parent whose children love science classes, and they want to help their kids learn more about what real scientists do. This parent – let’s call them Jenny -- wants a quick, easy way for the whole family to learn more about how science contributes to the real world. Daily Science News can help Jenny! 

The application shows the user 10 headlines of articles about current research findings from various scientific fields. Jenny and their family can choose a headline that sounds interesting then see a summary of the article, the university or institution where the research was conducted, and the date the article was posted to ScienceDaily, which is the data source used in the application. Users also see a link to the URL of the full article so they can read more about these fascinating discoveries by real scientists around the world!

## How?

### Staring at That Blinking Cursor

Until now, I had been writing Ruby code as part of student lab exercises guided by highly specified [RSpec](https://rspec.info/) tests. The tests reward me with cheery green feedback messages when I write code that creates the objects and return values specified in the tests, much nicer than the scary red font that delivers error messages! Facing a blank editor screen and empty project folder is a different matter! 

Enter the [Bundler](https://bundler.io/) gem! Bundler sets up a conventional folder and file structure to build a Ruby gem application. Several key files are preloaded with instructions, links to helpful resources and example code to get you started. Working with Bundler was the first in a series of learning adventures with [Ruby Gems](https://rubygems.org/)  in this project. 

### Finding Some Data

One of the first steps was finding a website with content that was interesting to me and feasible to access, given my current skill level. The bootcamp training prepared students to read website data using either the Ruby gem, [Nokogiri](https://rubygems.org/gems/nokogiri) for website scraping or an application program interface (API). I chose to use scraping. 

### Chopping That Data

Nokogiri is an amazing gem whose graphic name tells its story:  it chops, dices and slices (aka parses) HTML/XML documents. The key, however, is finding a website with well-formed HTML and minimal content that loads via JavaScript. I looked at many websites – a rabbit hole tale of epic scope! – to find content with an HTML/CSS layout that was feasible to parse with Nokogiri. While going down a rabbit hole can be distracting, after I navigate my way out of one, I try to notice something I learned along the way. In this case, inspecting a bunch of different sites improved my familiarity with HTML tags and CSS selectors such that when I found ScienceDaily, I could see how the author had created a much more systematic, navigable HTML/CSS structure than many sites I had seen. 

The ScienceDaily site categorizes news into 5 science areas: health, technology, environment, society and quirky (I want to be on the committee that chooses those!). Each area is featured, on a separate page, with subtopics that each include distinct lists of articles from which a user can choose. Like many of my peers working on this first project, I imagined building something bigger and cooler and more elaborate than I was ready to do. I wanted to include all 5 news topics, which would involve giving Users at least 3 decision points to navigate the application. After I began fleshing this out in code, I realized I needed to keep the structure and features fairly simple. Thankfully, the website I used offered a great option to help me do just that.

The site includes a Top Science page that appears to select headlines from all the other topical science pages based on, I assume, some metric of the articles’ popularity or other status indicator. One section of the Top Science page is a listing of the 10 latest headlines, which gave me a fantastic, assorted list of “breaking news” articles to share with Users in the first version of my application! 

### Building the Data into An Application

The application uses three classes: Article, Scraper and CLI. My aim for structure and process flow, with the single responsibility principle in mind, was to define each class with a distinct role, including its communication with the other classes. The application flowchart illustrates these relationships. 

![](https://imgur.com/82nfSVn)

Only the CLI class interacts directly with the User. It starts the program at the User’s command. This class contains the application’s main loop method that maintains process flow through the User’s decision points and information she sees on the console repeatedly until she chooses to exit. 

The Scraper is responsible for several methods containing Nokogiri code to collect the top science headlines and a few details about each article. I used constant variables to declare the home page and subpage URLs for Nokogiri to read.   

```
SITE = 'https://www.sciencedaily.com'
TOPSCI_PAGE = "#{SITE}/news/top/science/"

  def self.articles_list 
    scrape =  Nokogiri::HTML(open(TOPSCI_PAGE))
    articles = scrape.css('ul#featured_shorts li')
    articles.each do |headline| 
      title = headline.css('a').text
      url = headline.css('a').attr('href').value
      ScienceDaily::Article.new(title, url) 
    end
  end

  def self.add_article_features(chosen_article)
    site = Nokogiri::HTML(open("#{SITE}#{chosen_article.url}"))
      chosen_article.full_url = "#{SITE}#{chosen_article.url}"
      chosen_article.subtitle = site.css('h1#headline').text
      chosen_article.date_posted = site.css('dd#date_posted').text
      chosen_article.source = site.css('dd#source').text
      chosen_article.abstract = site.css('dd#abstract').text
    chosen_article  #article now has attributes
  end
	
	def self.topsci_headlines_update #update time of most recent headlines list
    scrape = Nokogiri::HTML(open(TOPSCI_PAGE))
    update_time = scrape.css('div#time').text.delete_prefix('updated ').chop
    update_time
  end
```

The Scraper’s ```topsci_headlines_update``` method grabs a little bonus information, which figures into plans for later versions of the app. The website updates headlines multiple times each day and shows an update time for the various headlines lists throughout the site. Users see the update time in this current Version 0.1.0, but it provides no functional updating effect because a User can access only headlines on display at the source site when her session is initiated. In a future version, it would be fun to allow users, if they keep a session open, to return and check for list updates, then see the newest headlines.

The primary model is the Article class, which creates and stores the article objects. Article acts as a kind of facilitator between Scraper and CLI. When the User asks to see the headlines list or a specific article, CLI requests the data from Article, but only Article talks to Scraper. If Article doesn’t find the data in its ```@@all``` array, it then gives a yell over to Scraper to go fetch the website data, then article initializes or modifies its objects. Article communicates information about articles and their features to the CLI class for display to the user. 

## Making It Pretty for Users. . . and Developers

I can easily get lost in the toy store of [Ruby Gems]( https://rubygems.org)! To keep it simple, I used the [colorize](https://rubygems.org/gems/colorize) gem to add a pop of color to the text displayed to the user. The article summaries are short paragraphs that displayed with ugly line wrapping in the middle of words. I handled that problem with a lesser known gem called [wrapify]( https://rubygems.org/gems/wrapify).

One aspect of my code is a bit unusual. I scoped almost all the methods as Class methods. Class methods make sense in the CLI and Scraper classes. The application is designed for one user to generate one active session, so it makes sense that it doesn’t need to maintain multiple instances of CLI or Scraper objects. In the Article class, however, there are multiple article instances! Even so, I was able to use class methods, except when instantiating the new article objects. This approach works but is not the typical pattern used for curriculum lab exercises. 

I’m fine with using an unusual coding pattern, for myself. However, as another student and I reviewed each other’s projects, he pointed out my use of Class methods and gave really helpful feedback. It’s important to code with an eye on the future, including my own ideas for new application features, as well as the possibility that other developers may read or contribute to my work via the public repository {GitHub link}. So, another great lesson was my coding buddy’s reminder to use good coding practices and patterns at all stages of the process. I am, believe it or not, looking forward to my technical review with the bootcamp instructors as part of this assignment to hear their feedback. I welcome feedback from readers, too!

## tl;dr

I would say the real purposes of building this application, as an early step in my coding education, were to experience the process and learn from amazing fellow students. A HUGE shoutout of thanks, especially, to 3 new coding pals for sharing tips and support!

Reading this post, I hope you will get a sense of how fun, maddening, empowering, disillusioning, celebratory, informative, social, and solitary (and, did I mention – fun?!) the process was. But I doubt it. Go build something yourself, and then you’ll see‼ 

