# [**nanoTwitter**](https://nanotwiitter.herokuapp.com/)

A smaller version of Twitter built to implement multiple scalability techniques and test the application under different client loads to gradually improve performance.

- [**heroku link**](https://nanotwiitter.herokuapp.com/)
- [**database diagram**](https://dbdiagram.io/d/61fec79485022f4ee543ce4e)
- [**prototype drawings**](https://github.com/bishal-baral/nanoTwitter/tree/main/doc/page_display)

**Team Members:** `Bishal Baral, Susan Li, Hannah Whitmore`

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
    - added a message queue to send/accept requests
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
  - `get /test/reset?user_count=u`
  - `get /test/tweet?user_id=x&tweet_count=y`
  - `get /test/status`
  - `get /test/validate?n=n&star=u1&fan=u2`
  - `get /test/corrupted?user_count=u`
  - `get /test/stress?n=n&star=u1&fan=u2`

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

### NanoTwitter Project Report

<img src="https://github.com/bishal-baral/bishal-baral.github.io/blob/main/nanotwitter-images/homepage.png" width="1000">

NanoTwitter is the baby version of the microblogging website -- Twitter. We started by creating a mini clone of the Twitter web application to replicate the basic features available on the website like account creation, simple user interaction and timeline and tweet database search. We used the lightweight Ruby web-framework Sinatra and PostgreSQL database as our basic stack and used Heroku to deploy our application to the cloud. After setting up our repository on GitHub with the basic configurations, we started thinking out loud the associations and sketching the logical [database relationships](https://dbdiagram.io/d/61fec79485022f4ee543ce4e) for the app. We tested the models and migrations creating fake seed data using the [faker](https://github.com/faker-ruby/faker) gem both manually and through unit tests.

#### Building NanoTwitter

The next step was to build the User Interface for our nanotwitter app. We initially drew [prototype drawings](https://github.com/bishal-baral/nanoTwitter/tree/main/doc/page_display) inspired by the current Twitter application and started coding up the design. The web app was styled using [SASS](https://sass-lang.com/), a preprocessor for CSS. After having the desired style in place, it was time to set up authentication. This allowed us to start better simulating the Twitter experience with account creation features and let us store users and sessions for a particular user. Moreover, passwords were stored using the industry-standard encryption library [bcrypt](https://github.com/bcrypt-ruby/bcrypt-ruby).

At this stage, we were prepared to build our basic functionalities. After a user logged in, they should be able to search for users/hashtags/tweets, follow users and view their tweets in their timeline. They should be able to create their own tweets, like other tweets and view their list of follower and the followees. Furthermore, after the mentioned features were implemented, it was essential to test out these functionalities so many more tests were added to the suite and below is the screenshot of the test results.

<img src="https://github.com/bishal-baral/bishal-baral.github.io/blob/main/nanotwitter-images/tests.png" width="1000">

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

Some more tests were written to make sure the above routes were working as desired. At this point, we needed `/test/reset` route to seed at least 1000 users which required it to imported in bulk to the database and make it further run as a background job to avoid timing out the application on the cloud which was done using the gems [activerecord-import](https://github.com/zdennis/activerecord-import) and [sucker_punch](https://github.com/brandonhilkert/sucker_punch).

After the seed data was loaded, it was finally time to start testing the robustness of our app. The test results showed that loading homepage of a given user upto the client load of 250 for a minute started causing `500 Internal Server Errors` 20 seconds in and `400 Timeouts` 50 seconds in. The full results of the initial load testing is available [here](https://github.com/bishal-baral/nanoTwitter/blob/main/public/initial_load_testing.pdf) on GitHub. Although results did not look pretty at all, it was exciting to see how our app would do if it were to required to handle such a load and finally move on to scaling.

#### Scaling NanoTwitter

<img width="500" alt="Unscaled Test Result" src="https://user-images.githubusercontent.com/64478834/166615385-9349f0c0-5c1c-4ad9-b29f-12f5d2f7aebf.png">

Initial implementation without scaling considerations had unsatifactory performances. We seeded our test database with two users and their following users, as well as 50 sample tweets for each user. Our server crashed at 200 users and the timeout rate was high. We went through the following scaling techniques attempting to improve the performance. Some succeeded and some made performance worse. 

- Efficient Database Queries
- Caching
- Service Oriented Architecture
- Queuing
- Background Processing

The details of each techniques and our experience implementing them are listed below:
##### Efficient Database Queries
Examining the logs, we found that the homepage timeline loading was the bottleneck, which required multiple accesses to the database and the sorting algorithm are complicated and time consuming. There were a few inefficient database patterns that we identified and fixed which improved the performance like the following:
- **N+1 problems**
This occurs when fetching one or more data and when there are N additional query calls which could have been fetched in one joint calls. We first loop through all followees of the user, then loop through all tweets for each followee.To tackle with this problem, we use joint database calls that obtain the data in one database query since it is more efficient.
- **Duplicated Database calls**
There were multiple places where we invoke the same database call, such as looking up user or tweets. We ensure that for every database call by saving the result to a variable and replacing the other duplicated database calls.
- **Indexing**
Indexing in the database is a data structure technique that minimizes disk accesses and provides fast search functionality. It will make write and update slower, because updating the data will require updating the index as well. However, search and read will be much faster. In our nano twitter, slow write or update is acceptable because it only happens with small writes but there are frequent reads and searches for massive size of records, so indexing is important and such a tradeoff is acceptable.
We also add indexes to all foreign keys(such as ```user_id``` in the ```Tweet``` table, and ```tweet_id``` in the ```Like``` table). We also add indexes to ```created_at``` in the ```Tweet``` table, because we use that to sort and access the tweet in loading the timeline.
**Special Note**: The performance of the test became worse when we first migrated the indexed database, but got much better when we tested again the next day. The conclusion was that the indexing process was still ongoing in the background after the migration was done, which slowed down the database accessing. 

##### Caching

<img width="500" alt="Test Result with Caching" src="https://user-images.githubusercontent.com/64478834/166615014-23ad19d8-d323-4110-bd72-f52183930f46.png">

Caching is the key scaling technique that greatly improved our test performance. It is an in-memory, key-value data storage that allows fast data load. With caching implemented(in our case through Redis, a persistent cloud application that supports caching), we were able to minimize time-consuming database calls and complex data process algorithms when loading some information.

We cached the following data for each user upon login:
- Follower and Followee List: A hashmap that maps user id to the user information map
- The User Timeline
    - A list containing the top 50 tweets information, sorted by time, that appears on a user homepage
    - Each tweet information is a hashmap of the tweet and some excess information that is needed when loading the homepage, such as Like Counts, and whether the current user has liked the tweet

For each cache, we set an expiry of 1 day so that in case the cache is corrupted or crashed, it can be reloaded. To implement this, we simply added another entry that kept track of when the data was saved for each data list for each user, and upon reading, if it had already past the 1 day mark since the cache was created, the data would be reloaded from the database.

There are a few operations that are interacting with the cache:
- **Homepage Loading(Read from cache)**
When a user logs in, their follower and followee list will be loaded and saved to cache. We then load the timeline using the followee list in the cache so we don’t have to access all followee records from the database for accessing all the required tweets. We use this cache as a volatile database, so that every change we make to the database, we update the cache as well, so that the cache will be valid throughout. It is also not a problem if the cache server for any reason crashes, because we are able to reload everything from the database when a piece of data is not found in the cache.

- **Follow/Unfollow(Update to Cache)**
If user A follow or unfollow user B, the cache will be updated and have the following consequences:
    - User A’s followee list will add or remove user B
    - User B’s follower list will add or remove user A
    - User A’s timeline will be reloaded from the new followee list
- **Potential Race Condition**
When there are concurrent write/update operations on the same piece of data, it’s possible that the latter write might cover the former ones, making the record inconsistent. Suppose two users are updating the follower/followee list at the same time, it’s possible that they both read the old list from cache, make their own updates, and write back to the cache. The latter will overwrite the former one and wipe it out.

   **Solution**: We implemented a lock such that for each user’s records in cache, only one user is allowed to read and write to the cache for a specific data list at a time.

   We add an additional entry called “lock” for timeline/follower/followee for each user, and let the cache updating start only if the saved value is false, and set it to true once start operation. When the update is done, we set it back to false and “unlock” the cache.
    
   There’s no need to worry about race conditions for timeline updates, because the timeline will be reloaded from the database from the followee list, and as long as the followee list is not corrupted, there won’t be race conditions for timeline write because the writes are supposed to be the same.

- **Create Tweet(Write to Cache)**
If a user creates a new tweet, the cache will be updated and the new tweet will be added to the top of the user’s and all his followers’ timeline. If the timeline exceeds 50 tweets, remove the bottom one(oldest tweet).

   We face a similar race condition described in the follow/unfollow cache update, such that the timeline can be updated by two different users at the same time to add a new tweet. We solve the problem through the same lock procedure.




##### Service Oriented Architecture
Instead of putting all functionality into a single process (monolith approach), a service oriented architecture puts parts of the functionality into separate services, each running in a separate process. Services communicate with each other in various ways - examples include calling a method object, over HTTP, or with queues. Each service is its own code base and can be tested separately and services can be distributed between servers, which is beneficial for scaling. 

Initially, we created separate asynchronous services for the create tweet service, timeline service, and the search service, but only kept the create tweet as a separate service. For the timeline service, making it asynchronous caused race conditions with the multiple threads. An example of this was loading a page twice and the timeline having different results. This occurred because with a separate thread that retrieved the followee list and pushed it to the homepage, every time we reload the page it looks different. So, refreshing multiple times created multiple threads that pushed back different versions to the home page. 

To avoid these race conditions, we removed the timeline as a separate service, and it is instead synchronous and part of the main app. We saw similar race conditions when the search service was asynchronous and additionally made it synchronous instead.

For the create tweet service, we made it a background job to avoid any race conditions. When a user creates a new tweet, that tweet gets added into their timeline and the timeline of their followees, and the oldest tweet is removed. All of these processes occur asynchronously — when a user hits the create tweet button, they are given an immediate response on the page, however all of the work done to push this tweet out is done in the background until the user refreshes again and the timeline is updated with the new tweet. Having the create tweet functionality done as a background job reduced the average response time but increased timeouts. 

<img width="500" alt="Screen Shot 2022-05-03 at 10 27 56 PM" src="https://user-images.githubusercontent.com/64478834/166615714-6746eb76-c16c-4987-9029-8169090e7f85.png">

##### Background Processing
Background processing saves jobs and processes them later without blocking the current thread. Background jobs process the slower tasks asynchronously, which can be helpful for horizontal scaling. Using background jobs is a way to achieve high concurrency and to scale programs.

The create tweet service and reset job were done using background processing. For the reset job, when we created 1000 users, generating all of their follower and followee lists took considerable time and the browser timed out.

Instead, we made the reset job a background process so that the browser gives an immediate response to hitting the reset button and the process is done in the background. The changes are displayed when manually refreshing your timeline. In general, using background processing caused more timeouts. When there is an error, such as a database connection error, in the background, the process doesn’t return that error but instead is stuck and waits until the browser times out. 

<img width="500" alt="Screen Shot 2022-05-03 at 10 18 09 PM" src="https://user-images.githubusercontent.com/64478834/166615089-24c45e59-560f-47ee-8dac-da23ef0f072a.png">

##### Queuing
Another scaling technique highly applicable to our service oriented architechture was queuing. Instead of making synchronous calls to the API endpoint of a given service, it was quite obvious that there needed a way to request ```CreateTweetService``` to create a tweet non-blocking way. We used the [bunny]() gem to use the [RabbitMQ]() service as our messaging queue and published tweet creation requests as json to our RabbitMQ client from the main nanotwitter app. In addition, ```CreateTweetService``` was configured to subscribe to the tweet creation queue and create the tweets as the requests come in. This decreased both the number of timeouts and the average response times which was a significant improvement from the previous attempts for the same payload.

<img src="https://github.com/bishal-baral/bishal-baral.github.io/blob/main/nanotwitter-images/queuing_test.png" width="500">

##### Debugging and Logging
Debugging nanoTwitter got more complicated as we increased the client load and we needed a proper tool to monitor and log events after a request was made to the server. We chose [PaperTrail](https://www.papertrail.com/) to record our logs in the cloud and use its fwatures to search through the logs more efficiently and debug our app.

##### Switching from Thin to Puma
Sinatra, by default was using ```Thin``` as its application server. We decided to compare its performance with ```Puma``` and came to the conclusion that Puma did a better job with less average response times and less number of timeouts. Since Puma is multi-threaded, it also allowed us to choose the number of ***Workers*** and ***Threads***. This helped us improve performance but posed a bigger challenge of avoiding race-conditions. Earlier in the report, we already talked about how we tried to avoid race-conditions in the case of nano-Twitter.

##### Known Bugs
1. When a new user signs up, it results in a ```Rack::Session::Cookie data size exceeds 4K - Cookie data size exceeds 4K``` error and redirects to the ```/welcome``` page. This error isn't occurring locally and is only on the heroku app.
2. CreateTweetService application on the heroku server under our free plan needs to wake up after long inactivity which initially creates a delay in creating tweets.
3. There are database connection errors when the client load reaches around 1600 and results in timeouts.


#### Final Thoughts

<img src="https://github.com/bishal-baral/bishal-baral.github.io/blob/main/nanotwitter-images/architechture.png" width="1000">

The above is the final architechture of our app and we have other ideas in mind that could make our app scale even better. Searching in our nanoTwitter app can be improved by tokenizing the search queries and everytime a new data is created in the database, we could update a search token's respective cache in the background or through messaging queues so that search query results are instant. 

