---
layout: post
title:      "Droplet for Reddit"
date:       2019-03-11 18:56:03 +0000
permalink:  droplet_for_reddit
---

Ever since I started planning for my first portfolio project, I've wanted to do something that involved Reddit in some capacity. A lot of people check their social media in their sparetime or inbetween activities, but I opt for Reddit instead. There is something for everyone, even for some of the most far out ideas- I'd be shocked if a subreddit did not exist for it already. If you haven't used Reddit, you're really missing out! Seriously, go check it out (after you finish reading this, of course).  

Unfortunately, some of the previous frameworks we've been working with just didnt seem like the right opportunity for it, or may have been a lot more involved that I would have liked given their more stripped down features. Luckily, React/Redux presented the perfect opportunity for this. I even came to find that Reddit also uses React for their front end. With this in mind, I set out to make a customizable Reddit feed (think multi-Reddit if you're familiar with the site). I wanted a user to be able to link up their Reddit account and from there create a customized feed for only what they wanted to see.

Droplet is certainly a work in progress and something I plan on working on in the future. Looks walkthrough a user's experience and talk about what's going on here: 

## Accounts and Authorization

Upon visiting the site, a user is able to create a Droplet account, at which point they are prompted to link their Reddit account (or create/link a new one). The Reddit API unfortunately will not return account information other than the username, so in order to properly create an account I decided to require the bare minimum upfront, an e-mail and password.  Once the user moves to link their Reddit account a call is fired off to the Rails API which will set up the query params required by Reddit in order to hit their first step in the oauth process, letting a user provide access permission. In order to do so, we have to pass this back to React to then redirect the user. If the user accepts, then they are sent to the callback URL which then hits Rails again to update the user's account with Reddit specific information to fully create a Droplet account. 

## Creating a User's Feed

Once your account has been linked, you're directed to the home page, which contains a list of the 100 most popular subreddits (at the time the page is loaded) presented in the form of Bootstrap cards with the subreddit icon, name, description, and option to add it to your feed (or removed if it was added).

<img src="https://i.imgur.com/YnWI3Ny.png" alt="subreddit cards" width="250" height="200" />

Anytime the user comes back to this page, they're going to get a new list of the top 100 subreddits at that moment, which keeps it fun and fresh. Once added, a card will instantly update and it'll be obvious (in a subtle way) that it was added. At the top of this page, there is a button to jump to your feed, or a user can use the animated pop out side bar menu to select 'posts.' 

<img src="https://i.imgur.com/Bw4CPTK.png" alt="sidebar menu" width="250" height="200" />

Now to get to the meat of the application! Let's take a look at our posts. When a user is navigated to the '/posts' route, they'll be presented with a list of 100 posts from either their feed. If they have not created a custom feed yet, they'll get the first 100 posts from Reddit's daily best as a default option. Each time this page is visited, an axios get request is being sent to the Rails API to then request the last 100 posts from Reddit. This is happening from the posts container and being passed down through a few child components. The container itself is throwing a get request upon being mounted, at which point the results are passed down to a list component which then renders individual post components for each of the 100 posts that were fetched. 

<img src="https://i.imgur.com/WjEznOc.png" alt="list of posts" width="250" height="200" />

For each post a user will see: the title, a thumbnail preview image, the author, how many upvotes the post has, comment count, and how many times the post has been gilded. When a post is clicked, the post itself is loaded into a modal (think pop-up) and an action is fired off to the Rails API to fetch up to the first 100 parent comments from that post, which are loaded in asychronously. 

<img src="https://i.imgur.com/WSIuCts.png" alt="example post and comments" width="250" height="200" />

Lastly, a user is able to check out their Droplet profile through the profile route. The profile itself allows a user to see their current Reddit avatar (pulled from their reddit account, stored on the user model, and then displayed back), email, Reddit username, and a list of all the subreddits that make up their personalized feed along with the option to remove them if desired. Additionally there is a functioning delete account button if a user chooses to do so. The profile itself will also show the status of a users account and whether or not they have successfully linked their reddit account, otherwise a user will be able to click the link to do so. 

<img src="https://i.imgur.com/hqCajJP.png" alt="sample profile" width="250" height="200" />

## Challenges

For my first React/Redux project, I'm pretty happy with what I created, but that certainly came with some unique challenges and headaches. Starting off with this project I wanted to follow what the React team coins as "thinking in React." I knew what I wanted to have but misjudged the initial scope of it. I took this one with a top-down approach instead of a bottom up. In hindsight, I think it woud have been better to do something like this from the bottom up given how many small functional stateless components there potentially could be for all the little things that Reddit does. The more you know! 

### Design Approach

The one piece of advice I did not follow initially was to design a static product first and then go back and make it work dynamically. Instead, I did the two hand-in-hand which I would not recommend, as it certainly complicated things and dragged them out time wise. I can see how it would be simpler to have a design in front of me and then figure out how to wire it from there and make adjustments from that. I should have just done a quick rendition of how I wanted my components laid out and functioning and added logic to it from there. If you're reading this and about to start on a project of your own, I would highly recommend doing that.

### OAuth and the Reddit API

Of the two-ish weeks it took me to get this up and running, the majority of my time was spent figuring out how to work with the Reddit API in a user-friendly manner. Reddit is a great service, and they provide a lot of neat endpoints in their API, but they do NOT make it easy to work with their API (unless you're using Python and PRAW (python wrapper for the Reddit API)), at least in my opinion. Unlike a lot of the major services out there, there is not a well established or reasonably maintened OAuth2 strategy for Reddit. There is one that hasnt been maintained for 2 years, but still can be made to function. 

Another challenge here was working with the documentation. I find it a bit hard to follow along with what exactly they wanted, I'm assuming because PRAW so thoroughly takes care of it for a user, so quite a bit of time was spent there just making sure I had everything set up correctlly. I've been thinking I may try and make some recommended changes to their docs to help non-python users better understand what is being asked for or how their process in general works. 

For the sake of space, I'm going to skip over how to get a Reddit API key, assign them to envs, and make them available for use. This is an example Reddit OAuth flow for a React frontend and Rails API backend:

1. From the frontend we will make use of a custom linkRedditAccount() user action to send a get request to our Rails API, letting it know we are looking to link the current user's Reddit account.

```
// --------------- USER ACCOUNT ACTIONS ---------------

export const linkRedditAccount = () => {
  const data = { 
    headers: {
        Authorization: `Bearer ${sessionStorage.getItem('jwt')}`
    }
  };

 return dispatch => {
    fetch(`${baseUrl}/link_oauth`, data)
      .then(resp => resp.json())
        .then(json => {
          window.location = `${json.url}${json.query_params}`
        })
        .catch(console.error)
  }
}
```

2.  Our Rails API will then set up the first post request to the Reddit API to allow a user to provide us permission in order to obtain an access token. The Reddit API requires a state_token of your choosing (for your own use to ensure it's the same user), and several different param metrics (see below). Here I've chose to render this back to the frontend in order to properly handle the redirect required by Reddit. 

```
  def link_oauth
      state_token = Sysrandom.urlsafe_base64(32)
      current_user.update(state_token: state_token)

      @redirect_info = {
         query_params: {
            client_id: ENV['REDDIT_KEY'],
            response_type: "code",
            redirect_uri: ENV['REDIRECT_URI'],
            duration: "permanent",
            state: state_token,
            scope: "identity edit read save submit subscribe vote history"
         }.to_query,
         url: "https://www.reddit.com/api/v1/authorize?"
      }
      render json: @redirect_info
   end
```
	 
3. Next the user will be redirected to the Reddit authorization page to allow or deny access based on the scopes provided in step 2. In our second promise in the code from step 1, you'll see we are now redirecting the user to that page since this is a SPA. Once the user hits accept they will be sent to our callback URL which hits the Rails API again.

```
.then(json => {
          window.location = `${json.url}${json.query_params}`
        })
```

4. In the last step, the callback url send the user's request to the oauth controller to then update their account with pertinent information returned from Reddit. Then the user is redirected back to the home page of the app where they can now make use of Droplet's functionality.

```
def reddit
        user = User.find_by(state_token: params[:state])   
    
        user.update_from_omniauth(request.env["omniauth.auth"])

        if user.save
            user.linked = true
            user.save

            redirect_to 'http://localhost:3001'
        end   
    end
```

That covers the oauth flow for Droplet! The other part of this challenge was authentication between the frontend and backend. Initially (and for the future) I want to handle this in HTTPOnly cookies to make it more secure. For now I have both ends set up to work with JWT tokens and sessionStorage for reference. When a user creates an account or logs in, a token is created that references an id and this gets passed back to React/Redux to get stored in sessionStorage until a user logs out or resets their session. Anytime a call is made to our Rails API, the token gets passed back to Rails and is decoded and checked for validity before allowing a user to do anything. 

## Future Plans

Right now Droplet has some base functionality that allows a user to link a valid Reddit account, start creating a customized feed based on popular subreddits and then return results from that feed. As I continue to work on this I'd like to prioritize the following:
1. Allowing a user to search for or specify subreddits of their choice. Right now a user can only add subreddits returned from the 100 most popular rather than specifying what they'd like to include.
2. Infinite scrolling or load on scroll. Currently each get request only returns the max limit of 100 items and that's it. I'd like to look more into load on scroll to continue loading additional posts from reddit to better simulate Reddit and other popular sites and limit the need to manually refresh the page for new content. 
3. Child comments. The posts as is only load the 100 most recent parent comments, but does not pull the child comment trees. 
4. Moving authentication to HTTPOnly cookies and out of JWT/sessionStorage. 
5. Deploy to Heroku for easier use. 

Overall I'm very happy with where I'm at on this project, especially for my first ever React/Redux project. There were a lot of firsts in this one, like using Devise (partially at least) and figuring out the whole OAuth flow between my frontend, backend, and third party API. I know now that Reddit was built using React, so I really am curious if I'm anywhere close in some regards to how they handle certain elements of their page. Anyways, thank you for reading! Hopefully there will be more to come on Droplet in the future and if you're curious please check out the Droplet Repos on GitHub.

Droplet React/Redux Frontend Repo: [](https://github.com/btmccollum/droplet-web)
Droplet Rails API Backend Repo: [](https://github.com/btmccollum/droplet-api)


