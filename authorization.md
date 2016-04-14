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

