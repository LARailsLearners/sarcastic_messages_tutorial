## Authentication

[Roll auth from scratch just like we did in the haikus project.
The User model should have an "email" and "api_key" fields, but no first name, last name, etc.
Let's persist sessions in a Session model](https://www.pivotaltracker.com/story/show/117089935)

Allowing users to sign in and identify themselves is called authentication. Typically, we request an email address and a password to authenticate the user, so we can be sure whoever is signing in is the same person who created the account.

http://railsapps.github.io/rails-authorization.html

[Railscast #82 HTTP Basic Authentication](http://railscasts.com/episodes/82-http-basic-authentication?view=comments)



## Authorization

Is the api_key" field a string?
Charlie McElfresh·Apr-7 5:06 PM
@jendiamond yes, it will be a [SecureRandom.uuid](http://ruby-doc.org/stdlib-2.2.2/libdoc/securerandom/rdoc/SecureRandom.html)

#### apache http access file - simple authentication without a server

http_basic_authenticate_with :name => "user", :password => "password" 

##### [http-basic-authentication](http://railscasts.com/episodes/82-http-basic-authentication)

http://api.rubyonrails.org/classes/ActionController/HttpAuthentication/Basic.html

---

[Build an API in your Rails app now!](https://labs.kollegorna.se/blog/2015/04/build-an-api-now/)

[Token Based Authentication in Rails](https://www.codeschool.com/blog/2014/02/03/token-based-authentication-rails/)

SecureRandom  
http://stackoverflow.com/questions/6021372/best-way-to-create-unique-token-in-rails  
http://daniel.fone.net.nz/blog/2014/12/07/generating-unique-random-tokens/

---

### [API Sessions With Redis in Rails](http://billpatrianakos.me/blog/2013/10/14/api-sessions-with-redis-in-rails/)

**Our problem now is knowing if a user is**  
a) authorized to access certain controller actions in your API and  
b) keeping track of authorized users.


**three possible solutions are to**  
+ use sessions
Sessions aren’t really RESTful and since you’re dealing with an API you’d be forced to send a cookie to the client which only works if the client is a browser. If you use any kind of sessions you have now just barred any non-browser client from consuming your API.

+ the database
You could store all active tokens in your database with an expiration time and a cron job to kill sessions after that time but keeping track of sessions like that is not RESTful and it puts a lot of strain on your database with tons of reads and writes that have to take place upon every request

+ keep everything in memory (Redis)

===

Redis is an in-memory key-value store. 

A database is best for data that needs to be persisted for long periods.

Redis is great for when the data can be destroyed with no consequences. 

Redis can only hold as much data as you have free memory – that’s the catch. 

In this case you’re only storing short strings for active users for a limited period of time. 

If you’ve got a server with 1G of RAM then you should be able to handle at least a few thousands active users at the same time. In my case I have Redis backed up by my database so if the server or the app restarts and Redis loses all its data the database is used as a fallback. During normal operation, however, you’d never hit the database at all.

Our tokens in this application are temporary. Each user is given an access token by first submitting their API key. From there the app verifies the identity of the key and returns an access token along with an expiration time. 

So, in summary, we’re using Redis to track active API users via their tokens because it’s fast and cheap.

[authenticate_or_request_with_http_token](http://apidock.com/rails/ActionController/HttpAuthentication/Token/ControllerMethods/authenticate_or_request_with_http_token)

https://www.codeschool.com/blog/2014/02/03/token-based-authentication-rails/

===

An Example for using it.

Call it in a `before filter` in your Base or `Application Controller`.

```
before_filter :authenticate_through_api_client

def authenticate_through_api_client

# this block should return true or false
authenticate_or_request_with_http_token |token,other_options|
 Api client.find_by_client_key(token).present?
end
end
```

# Sample request type: it expects a token in headers as

Authorization:Token token=“your_token_goes_here”

Authorization is the key and Token token=“” is value


===

[Securing an API](http://railscasts.com/episodes/352-securing-an-api?autoplay=true)




