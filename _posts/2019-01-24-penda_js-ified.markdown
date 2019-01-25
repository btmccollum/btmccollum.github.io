---
layout: post
title:      "Penda, JS-ified"
date:       2019-01-25 02:37:18 +0000
permalink:  penda_js-ified
---

If I had to sum up the last few weeks, I think this image would pretty accurately describe it:

![](http://www.tricedesigns.com/wp-content/uploads/2015/09/jsatt.jpg)

I've been to happy to be dealing with JavaScript again. The things you can do with JS are just amazing, yet definitely frustrating at times to deal with or make work correctly. I'm just scratching the surface here but it's motivated me to look a lot deeper into vanilla JS, jQuery, and even some of the crazy libraries that are out there. This project was definitely an interesting one, but I'll be glad to not be doing hybrid projects anymore. It'll be nice to have a dedicated frontend rather than having one foot in rails and the other in JS at the same time. 

## Getting Started / Planning

I feel like I had a pretty good gameplan in place from the get-go. I had originally designed Penda with JS in mind back before the start of the Rails project and had a checklist of things that I wanted to, or will at some point, implement. The biggest piece is certainly the timer (since this is designed to be a time tracker application of sorts), but that will have to be something I come back to at a later point. 

![](https://i.imgur.com/5mwEJ1P.png)

I have a working timer function for now, it just doesnt look as pretty as I'd like. I came across a neat gem called FlipClock which works well out of the box, but doesnt quite match the asthetic I'd like to keep. 

Other than the timer, I really wanted to jump in and make as many things dynamically render as possible, so much so that I think I may have gone a bit too far and made my application feel almost like an SPA of sorts. Part of that I'll chock up to the fact that we are kind of doing a hybrid here, but it was also a good learning opportunity to figure out how to make so many things work concurrently or in succession where necessary. From my login page I can go 4, 5, 6 ,7, etc. layers deep without having to redirect or refresh which feels pretty cool to look back on. 

From a design standpoint, I gained some time because I had spent an extra day or two upfront on the Rails portion doing some minor stylization with bootstrap and a bit of custom CSS. Coming from a creative background, it was hard for me to stop with what I already have, but at the very least bootstrap is a great framework for getting a presentable product up quickly! 

## Challenges

Did I mention earlier how excited I get about playing with JavaScript? Weird, right? I'm surprised how good I felt about getting started with the JS for this project. It's been a while since I last used JS seriously, and I had never done much with AJAX prior to this, but I felt really comfortable working with JS and jQuery for this project. I realize that there are probably a lot of things I could be doing more efficiently in my code, but I'm happy with what I have so far and I'm always looking to learn new things! That being said there were a few moments in this project that were real pain points where I lost a few hours. 

The first big pain point I had was in trying to get a delete request to dynamically work. I was constantly able to hit the destroy action in its respective controller, but I was defeated time after time. I tried using the plain old ajax() call, post(), and even fetch() but nothing was working. I knew it was set up correctly in the code, but for some reason it just would not stop redirecting. I think I had something like 20-30 lines of different iterations I had tried. I'll admit that, rather embarassingly, I had not noticed that the reason it was not working was because my original delete request from the Rails project was set up on an anchor tag, rather than a form/button like it would traditionally be done-- no wonder I wasnt catching it! Once I had that realization, I was able to get it wired up correctly and working through an event listener like so:

![](https://i.imgur.com/Txa8hN8.png)

A lot of my event listeners take advantage of bubbling and the target element (event delegation) to properly catch what I was needing to. This was especially important since I am making use of one div with the class of js-Content to contain the information being dynamically rendered in each event. Doing so meant I didnt need to worry about the event listeners being removed and instead they would persist between each render. This also solved another issue I experienced for a bit, in that my event listeners were not properly working after data was dynamically rendered. Instead of having specific event listeners initially set up, and then having to rebind them after data was dynamically rendered, making use of event delegation from body made my life easier and code cleaner. 

## Closing Remarks

All in all, this was a very fun experience. It is amazing how many things you'll pick up when you're having fun with the subject and exploring a little bit. One of my favorite things I learned about in this experience was pushState(). This is probably something really basic, but I thought it was really neat to pick up on and incorporate. Using pushState() allows you to essentially rewrite the user's browser history and alter the address reflected in their address bar. That might sound weird, but when you're dynamically rendering a new page without a redirect or refresh, you can make use of that to update the url to update as well, so if a user refreshes the page they would still be on that view! Pretty nifty, right?? From there I created a small custom function to set the pushState and also set the historyInitiated value to true to trigger popstates when a user hits the back button:

```window.addEventListener("popstate", function(e) {
        if (window.historyInitiated) {
          window.location.reload();
          defineCurrentProject();
        }
    });
```

```
function setPushState(url) {
    window.historyInitiated = true;
    history.pushState(null, null, url);
}
```

Just something I wanted to share! If you made it this far, thank you for reading and feel free to check out [Penda](https://github.com/btmccollum/penda-app) if you're interested. Remember, JavaScript all of the things! 
