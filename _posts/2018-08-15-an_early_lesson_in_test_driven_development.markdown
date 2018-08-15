---
layout: post
title:      "An Early Lesson in Test Driven Development"
date:       2018-08-15 00:17:38 -0400
permalink:  an_early_lesson_in_test_driven_development
---



I like the concept of Test Driven Development (TDD). I'm happy that the Flatiron web development course is introducing TDD gradually and in parallel with learning to code. 

Early in the course, however, I had quite a challenge with the RSpec tests for the Tic Tac Toe game that we are building step-by-step over time. After I worked through the challenge, I felt I had been the protagonist in my own [morality play](https://en.wikipedia.org/wiki/Morality_play)!   I had met and wrestled with Wicked RSpec, who threw down obstructions and perils to thwart my precious and beautiful Ruby from entering the castle door (aka code that worked but didn't pass the tests). 

I had created quite a saga from an easy set of code and corresponding tests! Yet, it was worth the drama because -- spoiler alert -- in the end I gained the Prize, a key lesson, which will be worth my weight in gems over time. I offer my tale in the hope it will help fellow beginners, as well as seasoned travelers, on the coding journey  . . . . . . . . .

An early step in building the Tic Tac Toe (TTT) game was to create a #display_board method to display an empty, standard game board using ASCII characters. There were only 3 required features: 

* Each cell is presented by a string with 3 spaces:   "   "
* Each row has 3 cells, the middle cell separated by 2 | (pipe) characters:
* There are 3 rows, with 2 separating lines of 11 - (dash) characters: -----------

My code for this first step is below. 

<blockquote class="imgur-embed-pub" lang="en" data-id="LdlvgSW"><a href="//imgur.com/LdlvgSW">View post on imgur.com</a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>

To my delight, the code displayed a lovely tic tac toe board with the required features and passed the RSpec tests!  

The next coding task was to display the same empty TTT board, as well as boards containing hypothetical player moves using "X" and "O" characters. 

I wanted the code first to pass the tests related to displaying an empty board before I added "move" characters to the board array. I assumed I could use, essentially, the same code for printing the board as the first TTT board code.   

I moved the board array specifying the status of the game board cell contents outside the #display_board method and added a single argument. The argument can be used to call the board array containing all "empty" elements (3 spaces) or one or more "filled" elements (an "X" or "O" surrounded by spaces to maintain a total cell width of 3 spaces). 

I was confident it would pass the tests because it displays the same standard TTT board as the first set of code! 

![](https://1drv.ms/u/s!ArAVgxVYhRa-cw-u-HBvCRHevig)

Alas, here is where RSpec entered stage right and began taunting me!  This code failed the first test specifying a blank TTT board.  The surprise was that the test results said my board's cells were too narrow (see white arrow)! How could that be when, clearly my board array defined cells that were three spaces wide, per specifications, using the string "   ". 

![](https://1drv.ms/u/s!ArAVgxVYhRa-dEQA4oLiXCIMq7U)

After several minor edits and failed tests (AKA descent into rabbit holes), I recoded the #display_board method as shown below to see if the string interpolation I used to print the row divider lines was a problem. I didn't feel confident in this version because I had no basis in Ruby documentation to support it, but it was worth a try.  However, version 2 of the second board code failed the same test in the same way as version 1!  The board cells were too narrow! 

![](https://1drv.ms/u/s!ArAVgxVYhRa-dkT8lBKO0x2wFrw)


At this point I had no more tricks in my bag. I had dug into Ruby documentation but could find nothing in my code that would create cells narrower than 3 spaces!  Finally, instead of trying to interpret the test error messages, I considered reading the test code. At the time, I thought this a form of cheating. Since I hadn't written the tests and was new to TDD, I felt like reading the test code was something akin to peeking at crossword puzzle answers! 

Thankfully, as the protagonist in this drama, I resolved this minor moral dilemma and was rewarded with the Prize!  the Answer!  the Grail!  

Um, actually, it was more like the Duh! Moment.

The tests were expecting the blank board array elements to be a single space string " " not 3 spaces as I had used. (And guess what? The same information was staring at me in the lab instructions!) As a result, the only way I could add spaces to the board cells was by adding spaces, as strings, in the #display_board puts statements. This third version of the code passed the tests!  

![](https://1drv.ms/u/s!ArAVgxVYhRa-dxYO9mNwDEissL8)


Like any morality play, the plot, on its face, is not really the story here. That is, the take-home lesson for me was only tangentially about code and RSpec tests. I was reminded that to write good code requires using soft skills, as well as technical skills. In this tale, I had only to find my Zen and review the information I already had at hand, not actually learn anything new about Ruby or RSpec. I focused too narrowly on finding a technical answer, so I overlooked the key information in plain view. In addition, I learned that building on prior versions of code may often be efficient, I should not assume that is the case. Looking with fresh eyes at the specs for a new task or project is always a good idea. 

