# [**nanoTwitter**](https://nanotwiitter.herokuapp.com/)
A smaller version of Twitter built to implement multiple scalability techniques and test the application under different client loads to gradually improve performance.

- [**heroku link**](https://nanotwiitter.herokuapp.com/)
- [**database diagram**](https://dbdiagram.io/d/61fec79485022f4ee543ce4e)
-  [**prototype drawings**](https://github.com/bishal-baral/nanoTwitter/tree/main/doc/page_display)

**Team Members:** ```Bishal Baral, Susan Li, Hannah Whitmore```

### dependencies and gems:

    - npm
    - rake
    - sinatra
    - activerecord
    - sinatra-activerecord
    - pg
    - rack-test
    - sinatra-reloader
    - rerun
    - bcrypt
    - faker
    - hirb
    - sassc
    - yaml
    - activerecord-import
    - redis
    - puma
    - sucker_punch ~> 3.0

### instructions to run the app:

- clone the app
- install npm
- run 'bundle install' in the console
- configure the database settings for postgresql
- run "rake db:setup" to create a new database, run migrations and seed data
- to start the app in development mode, run "npm run start:dev"
  - to seed the data again in a completely new database, run "npm run re:seed"

### additional instructions

- install sass with 'brew install sass/sass/sass' or 'npm install -g sass'
- run 'npm run compile:sass' on a new terminal to run watch for write changes on scss files for auto compilation

### test instructions
- to make changes to the test database, add 'RAILS_ENV=test' to the typical rake create, migrate, seed etc. commands.
- to run to test console, run 'npm run console:test'

### change history

- version nt-0.1
  - created the basic skeleton for nanotwitter
  - initial setup with gems for sinatra, activerecord and postgresql
  - scripts for running the app in package.json
- version nt-0.2
  - created models, migrations and associations between different tables
  - added validations for the models
  - created seed data using Faker
  - wrote unit tests for models
- version nt-0.3
  - added sass and views for a few pages
  - uploaded a paper prototype for the app
  - created basic routes for the app and dummy fews for appropriate pages
    - route description in /doc/routes.MD\n
- version nt-0.4
  - added user authentication
  - added user sign up and sign in
  - added test for user authentication
  - modify the Rakefile to run app test all together
- version nt-0.5
  - added the following features:
    - create tweets with hashtags and mentions
    - like/unlike tweet
    - follow/unfollow user
    - search tweet, #hashtag and @mentions
  - home page with 50 recent tweets from followers
  - ability to view followers and users following
  - view personal profile and other users profiles
  - view the list of followers
  - implemented the search and tweet UI
- version nt-0.6 
  - imported the user, tweet and follow data from the csv data
  - created the test interface to start testing through the following test routes
    - GET /test/reset?user_count=u
    - GET /test/tweet?tweet_user=x&tweet_count=y
    - GET /test/status
    - GET /test/corrupted?user_count=u
    - GET /test/stress?n=n&star=u1&fan=u2
- version nt-0.7
  - set up loader.io to start testing the app 
  - used the seed data and test interface to test the app scalability
  - started to write logs and integrated Papertrail
- version nt-0.8
  - used redis to cache timeline tweets and followers 
  - experimented nt's performance under different client loads after
    - changing the database pool size and indexing the database depending on the most frequently searched column
    - changing the number of worker threads in the puma web server 
  - continued to document test results
- version nt-0.9
  - create a service oriented architechture to handle search, create tweets and load timline.
  - implemented background job for test/reset route.
  - continued to load test.
- version nt-1.0
  - made the following changes to tweet creation service 
    - updated the cache and the database in the background.
    - change the timeline cache of all the followers (fan-out)
  - improved the UI 
  - fixed critical bugs to ensure consistency in the timeline cache.


### Core Functionality

The features of nanotwiitter are the following:
- Authentication
  - login
  - logout
  - signup
- Tweet Interaction
  - create new tweets
  - like/unlike tweets
- User Interaction
  - view profiles
  - follow/unfollow users
- Timeline
  - view the most recent tweets
  - view followers/followees
- Search
  - @users
  - #hashtags
  - tweets
- Test Interface
  - ```get /test/reset?user_count=u```
  - ```get /test/tweet?user_id=x&tweet_count=y```
  - ```get /test/status```
  - ```get /test/validate?n=n&star=u1&fan=u2```
  - ```get /test/corrupted?user_count=u```
  - ```get /test/stress?n=n&star=u1&fan=u2```


### Nanotwitter Routes
    GET ROUTES
        /? 
        /users/:id 
        /users/:id/followers 
        /users/:id/followings 
        /hashtags 
        /welcome/? 
        /login/?  
        /signout/? 
        /signup  
        /search 
        /tweets 
        /tweets/:id 
        /tweet/create? 

    POST ROUTES
        /users/:id/follow 
        /login/?
        /signup/? 
        /search 
        /tweets 
        /tweets/like/ 


### CreateTweetService Routes
    GET ROUTES
        /api/create_tweet_service/create? 


### SearchService Routes
    GET ROUTES
        /api/search_service/search/user? 
        /api/search_service/search/hashtag? 
        /api/search_service/search/tweet? 
        
### Database Tables
    Follows
        follower_id, followee_id, created_at, updated_at
        Indexes on: followee_id, follower_id
    Hashtags
        title, created_at, updated_at
    Likes
        user_id, tweet_id, created_at, updated_at
        Indexes on: tweet_id
    Mentions
        user_id, tweet_id, created_at, updated_at
    Replys
        parent_id, reply_id, created_at, updated_at
    TweetHashtags
        tweet_id, hashtag_id, created_at, updated_at
    Tweets
        content, is_deleted, user_id, original_tweet_id, created_at, updated_at
        Indexes on: created_at, user_id
    Users
        name, username, email, password_digest, created_at, updated_at
        Indexes on: username

### Project Report

#### Introduction:
NanoTwitter is the baby version of the microblogging website -- Twitter. We first started by creating a mini clone of the Twitter web application to replicate the basic features available on the website like account creation, user interaction and timeline and database search. We used the lightweight Ruby web-framework Sinatra and the SQL database Postgres for our basic stack and used Heroku to deploy our application to the cloud. After setting up our repository on GitHub with the basic configurations, we started thinking out loud the associations and sketching the logical [database relationships](https://dbdiagram.io/d/61fec79485022f4ee543ce4e) for the app. We tested the models and migrations creating fake seed data using the [faker](https://github.com/faker-ruby/faker) gem both manually and through unit tests.

#### Building NanoTwitter
The next step was to build the User Interface for our nanotwitter app. We initially drew [prototype drawings](https://github.com/bishal-baral/nanoTwitter/tree/main/doc/page_display) inspired by the current Twitter application and started coding up the design. The web app was styled using [SASS](https://sass-lang.com/), a preprocessor for CSS. After having the desired style in place, it was time to set up authentication. This allowed us to start better simulating the Twitter experience with account creation features and let us store users and sessions for a particular user. Moreover, passwords were stored using the industry-standard encryption library [bcrypt](https://github.com/bcrypt-ruby/bcrypt-ruby).

At this stage, we were prepared to build our basic functionalities. After a user logged in, they should be able to search for users/hashtags/tweets, follow users and view their tweets in their timeline. They should be able to create their own tweets, like other tweets and view their list of follower and the followees. Furthermore, after the mentioned features were implemented, it was essential to test out these functionalities so many more tests were added to the suite and below is the screenshot of the test results.

<img src="https://github.com/bishal-baral/nanoTwitter/blob/main/public/images/tests.png" width="1000">

#### Preparation for Load Testing
Before even starting load testing, the following routes were to be implemented to make the testing process more convenient.
```
get /test/reset?user_count=u
get /test/tweet?user_id=x&tweet_count=y
get /test/status
get /test/validate?n=n&star=u1&fan=u2
get /test/corrupted?user_count=u
get /test/stress?n=n&star=u1&fan=u2 
```
Some more tests were written to make sure the above routes were working as desired. At this point, we needed ```/test/reset``` route to seed at least 1000 users which required it to imported in bulk to the database and make it further run as a background job to avoid timing out the application on the cloud which was done using the gems [activerecord-import](https://github.com/zdennis/activerecord-import)  and [sucker_punch](https://github.com/brandonhilkert/sucker_punch).

After the seed data was loaded, it was finally time to start testing the robustness of our app. The test results showed that loading homepage of a given user upto the client load of 250 for a minute started causing ```500 Internal Server Errors``` 20 seconds in and ```400 Timeouts```  50 seconds in. The full results of the initial load testing is available [here](https://github.com/bishal-baral/nanoTwitter/blob/main/public/initial_load_testing.pdf) on GitHub. Although results did not look pretty at all, it was exciting to see how our app would do if it were to required to handle such a load and finally move on to scaling.

#### Load Testing and Scaling NanoTwitter
The scaling techniques that we tried on our nanotwitter application in order are the following:
- Efficient Database Queries
- Caching
- Service Oriented Architechture
- Queuing
- Background Processing

(for each technique write how it made the app better/worse and improved the test results)


#### NanoTwitter 1.0
<img src="https://github.com/bishal-baral/nanoTwitter/blob/main/public/images/architechture.png" width="1000">


#### Conclusion


