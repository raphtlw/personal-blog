---
title: "Create a Twitter Bot in Node.js"
date: 2021-05-01T19:08:03+08:00
draft: false
---

![](https://cdn-images-1.medium.com/max/800/0*eIdt1EeVcD7Pp2S7)

###### Photo by [freestocks.org](https://unsplash.com/@freestocks) on [Unsplash](https://unsplash.com)

How about a simple twitter bot which retweets/searches/replies on tweets? I recently made one&mdash;[check it out](https://twitter.com/checkra1nbot). Building twitter bots may sound like a huge thing, but it really is pretty easy once you understand the basics of JavaScript and Node.js. Today, we will be building a twitter bot using the “twit” package from [npm](http://npmjs.org). Please note&mdash;you will need to refer to the [twit documentation](https://www.npmjs.com/package/twit) for a deeper reference.

## Getting started

To get started, make sure you have Node.js installed (of course). To install it on Linux:

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
```

Then

```
nvm install node
```

In a new project

```
npm init
```

For all the prompts, just hit enter (defaults).

To install twit:

```
npm install twit
```

Now, we just need to create an account for the bot on [https://www.twitter.com](https://www.twitter.com)

* * *

After creating an account for the bot, head over to [https://apps.twitter.com/app/new](https://apps.twitter.com/app/new) to create a new twitter developer account for the bot.

Fill out the necessary fields in the form and click on the button “Create Your Twitter Application”. After creating the application, look for “Keys and Access Tokens” under the nav-panes and click on “Generate Token Actions” and then copy:

*   Consumer Key
*   Consumer Secret
*   Access Token
*   Access Token Secret

Create a new config.js file and add the credentials as follows:

```
// config.js  
/** TWITTER APP CONFIGURATION  
 * consumer_key  
 * consumer_secret  
 * access_token  
 * access_token_secret  
 */

module.exports = {  
  consumer_key: '',    
  consumer_secret: '',  
  access_token: '',    
  access_token_secret: ''  
}
```

* * *

## Building the bot

In the same folder, create an `index.js` file. We will need to instantiate a new Twit and Twitter object with the configuration file as needed.

```
var Twit = require('twit');  
var Twitter = new Twit(require('./config.js');
```

### Retweet function

Let’s create a function which retweets things. In order to do so, we can use the Twitter object to retweet the tweet passed in as a parameter to the function. The Twitter object (according to the documentation) retweets tweets passed in to the post function as an ID string. Which means that a tweet’s ID has to be given as a parameter to the post function.

```
/**  
 * Retweets a tweet passed into the function.  
 * @param {*} tweet  
 */  
function retweet(tweet) {  
  Twitter.post(  
    'statuses/retweet/:id',  
    { id: tweet.id_str },  
    function(err, data, response) {  
      // do something  
    });  
}
```

With this, you can do something after the function we passed in to the post function is called. The function, known as a callback, will be returned: `err, data, response`. This means that we can use the `data` returned to tell the console which tweet we retweeted (optional). The `data` returned is actually a `twitter` object.

Else, we can just log to the console if twitter sends us an error while retweeting the tweet.

```
/**  
 * Retweets a tweet passed into the function.  
 * @param {*} tweet  
 */  
function retweet(tweet) {  
  Twitter.post(  
    'statuses/retweet/:id',  
    { id: tweet.id_str },  
    function(err, data, response) {  
      if (err) {  // here  
        console.log(err.message);  
        return;  
      }  
    }  
  );  
}
```

### Searching function

We can also have a function that searches something and calls another function passed into the searching function as a parameter with the result after searching.

```
/**  
 * The searching function.  
 * @callback callback function which is called after the tweet is returned.  
 */  
function search(callback) {
  /**  
  * Gets a search key from data.js.  
  * @private  
  */  
  function getSearchKey() {  
    let searchKeys = require('./data.js').searchKeys;  
    return searchKeys[Math.floor(Math.random() * searchKeys.length)].toLowerCase();  
  }

  Twitter.get(  
    'search/tweets',  
    { q: getSearchKey(), count: 50, lang: 'en' },  
    function(err, data, response) {  
      let tweetList = [];  
      if (!err) {  
        for (let i = 0; i < data.statuses.length; i++) {  
          let tweet = data.statuses[i];  
          tweetList.push(tweet);  
        }  
        callback(tweetList);  
        return;  
      } else {  
        console.log(err);  
      }  
    }  
  );  
}
```

If you want to search for something and retweet a random result from your query, we can pass the retweet function into the search function, and set a interval in which the functions will be executed.

```
function main() {  
  search(retweet);  
}
```

We are calling the function but not actually running it. This means that we are not putting the () brackets after the `retweet` function. Only in the search function will it be called from inside.

Now, we can call the main function after a set interval.

```
main();  // Run the main function once first before running the interval ticks. This is optional.

setInterval(main, Math.floor(((Math.random() * 10) + 5) * 1000) * 60);
```

* * *

Please refer to the [twit documentation](https://www.npmjs.com/package/twit#usage) for more things you can do with twit.

Thanks for reading!
