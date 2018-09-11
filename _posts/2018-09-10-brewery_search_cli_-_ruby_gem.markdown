---
layout: post
title:      "Brewery Search CLI - Ruby Gem"
date:       2018-09-11 01:05:14 +0000
permalink:  brewery_search_cli_-_ruby_gem
---


That. was. FUN! While all the lessons have certainly been interesting, this is the first time where you're really on your own and trying to put everything together from scratch. While you do have a few resources you can reference back to, it's exciting (and a bit intimidating) starting  from scratch on a project. You hear a lot that you'll be spending a lot of time fixing errors and bugs, and before now I havent seen that as much, but after extensive testing I ran into my nth bug while filming my video demonstration (funny how all this happened only at the very end of the process). 

For my CLI gem I eventually ended up doing a Brewery directory (entitled 'Brewery Search') that leverages the database from www.BrewBound.com.  The gem itself allows a user to search through BrewBound's database of breweries, initially at the state level, and also provides the option of filtering down by city (see the bottom of this post for links if you'd like to check it out). For any of the results you can enter the number of the brewery for more information about them. I found a neat gem in the process named Launchy that helps with opening urls in the users default browser. This allowed me to add some functionality for a user to open any of the brewery's listed social media accounts or website if they wanted to. 

I think the most difficult step in this entire process was just finding something interesting to scrape. I had spent a good half a day just thinking about sites that I enjoy and would be interesting to srcape, while at the same time would not be too easy or too obscure. My first few ideas required APIs, which unfortuantely they were not issuing non-commerical keys for, which ruled those out. I really wanted to use Reddit's API, but I needed to figure out a way to mask my API's secret key while still allowing other users access. My next thought was beer! I love to brew with a few friends on weekends, and there are tons of databases out there for breweries and beer. By the way, at one point I thought about doing a CLI gem that helps better identify websites that might be suitable for scraping, I made a bit of progress on that one, but someone should totally do that!

The first few websites I tried unfortunately involved a bit too much Javascript, which did not make them a good fit for scraping. However, when I stumbled upon BrewBound, I was greeted by a beautiful scene in the DOM. There, right in front of me, was a clearly labeled table, and many many `<tr>` entries for each listed brewery! Although the class/id names were exactly distinct, they were laid out in a way that would still make them accessible without jumping through too many hoops:

			<tr>
            <td>
                <a href="/breweries/epic_brewing_company">
                    <figure>
                        <div class="center-cropped" style="width: 50px; background-image: url('https://d2pxm94gkd1wuq.cloudfront.net/BreweryLogos/Standard/530972861.epicbrewingcompany.jpg');">
                        </div>
                    </figure>
                </a>
            </td>
            <td>
                <a class="accented hidden-mobile bold " href="/breweries/epic_brewing_company">Epic Brewing Company </a>
                <div class="visible-mobile">
                    <a class="accented bold " href="/breweries/epic_brewing_company">Epic Brewing Company </a>

 I find scraping interesting, but it can definitely be tedious at times. I certainly discovered this when trying to pull brewery attributes from their profile pages. While the main page was more predictable, I had to come up with some interesting conditional checks in my code to account for whether or not a brewery had active job listings, formation dates, etc.. Unfortunately their profile pages just did not have any sort of identifying css selectors when it came to address and brewery overview. I'll admit it was hacked together, more so than I'd like, but hey at the end of the day it worked for what I needed. I had to do quite a bit of testing on many different breweries just to ensure that I wasnt missing any weird one offs, but in the end I felt confident that my code accounted for any twists. 
 
 From an overall design standpoint I feel like I had a much easier time with this by just talking myself through a story of what I wanted out of this and how it should work. Getting your thoughts down is definitely important and helps with thinking through some of the individual components. I found myself doing some TDD/BDD testing in the beginning, but quickly hit a wall with my knowledge of Rspec and different tests you can write. I definitely want to revisit that, but for the sake of time i decided to verbal walk through components as I added them in. The structure itself is relatively simple for a project of this nature, it's just more so the execution to realize your vision that presents a challenge sometimes. 
 
 I've talked a bit about one of my first road blocks up fronts, so I'd like to discuss some of the design complications. Without relying too much on outside gems, there were some things I wanted to figure how to do, such as how to scrape a list of results when there are multiple pages. This one gave me pause for a bit, but in looking out their url structuring, I started to see common patterns in how the site did their state searches, additional pages of results, and even all the different breweries. Once I identified this, it made it a bit easier to lay out ways of injecting user inputs to fill in the blanks and allow me to flesh those ideas out. 
 
 Once I got past some of the complications with scraping, I had to figure out a clean way to be able to have forked decision trees based on whether or not a user wanted to search by a state or filter down to a city while keeping a consistent user experience. I had to refactor this bit a few times but I was eventually able to pull some pieces out into simpler, common methods, that could be called on to make my code more DRY. 
 
Code aside, one of my larger frustrations with this process was publishing the gem and some silly oversights. If you've read this far and plan on publishing your gem, definitely look into how a Gemfile should be structured depending on how you have your directories/folders/etc. set up and named. This definitely gave me some heartburn for a bit before realizing it was trying to look for 'exe' rather than 'bin' like the bundler sets up by default. 

I had to have a bit of design fun with this too since it is all text based. I used an ASCII generator to create some fun beer themed art as a small welcome screen for a user when it's launched for the first time:
 
 ![](https://i.imgur.com/JXq1kUS.png)
 
 Overrall this was definitely a fun challenge and a great way of tying together all of the concepts covered thus far, especially with OO in Ruby. I'm definitely going to miss Ruby, but I know it wont be too long until we're back with rails :) 
 
Check Brewery Search out if you'd like:
 
RubyGems: https://rubygems.org/gems/brewery_search
Github: https://github.com/btmccollum/brewery_search 
 
 
 



