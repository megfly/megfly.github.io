---
layout: post
title:      "Omniauth Shenanigans with Ruby on Rails "
date:       2020-09-13 21:00:40 +0000
permalink:  omniauth_shenanigans_with_ruby_on_rails
---


One of the requirements for our Ruby on Rails project was to include a third party login! This was a struggle for me but i'm trying to see the light haha!


OmniAuth is a ruby authentication framework that allows apps to use third party authentication for their users. Instead of a user signing up through the app, the user can sign into the app using a third party (Facebook, GitHub, google etc) that they already have login information for. This is great for the user and the app developers because the user doesn’t have to remember another password and the app developers don’t have to worry about that storing and keeping that users information safe from hackers!


For my project I used Facebook as my third party login provider. The first step to utilizing OmniAuth in our app is to put the gem in our gemfile. Since the OmniAuth gem lets us use several different providers we will need to add the OmniAuth gem and the provider specific gem to our gem file. 


```
gem 'omniauth'
gem 'omniauth-facebook'
```


After we have bundle installed, we need to tell OmniAuth about our apps credentials. Each OmniAuth strategy is middleware. From what I understand, middleware is software that assists with the execution of a task. It helps communication between two systems that aren't directly involved with each other and 'bridges the gap' between them. So, we will need to have our app 'bridge the gap' with facebook. We will need to tell OmniAuth about our apps Oauth credentials. This code is telling our app to use a piece of middleware for the Facebook authentication strategy.


config/initializers/omniauth.rb 
```
Rails.application.config.middleware.use OmniAuth::Builder do
provider :facebook, ENV['FACEBOOK_KEY'], ENV['FACEBOOK_SECRET']
end
```


As seen in the above code we will need a facebook key and secret. In order to generate a key and secret we need to create an app on the Facebook developers site. After we get this key and secret we will need to put it in a file in our app that we want to store and hide from the world! 


The dot-env rails gem ensures that environment variables (seen above: key and secret) are correctly loaded into the ENV hash (ENV is a way to store key value pairs) securely. So after bundle installing the dot-env gem I created a file named .env and put the key and secret in that file. Then, in order to hide it from the world we put .env into our .gitignore file so we don’t accidentally commit the credentials for all the world to see.


gem file:
```
gem 'dotenv-rails', :groups => [:development, :test]
```


env file (MarathonReview/.env):
```
FACEBOOK_KEY= (whatever facebook gave you number)
FACEBOOK_SECRET = (whatever facebook gave you number)
```


gitignore (MarathonReview/.gitignore)
```
# Ignore application configuration
.env
```


Now, in order for a user to click on a link that will lead them to facebook login we will need to add a link to our home page and we need to redirect users to /auth/:prover/callback in our controller and controller action.


Here is my welcome view:
app/views/application/welcome.html.erb
```
<%= link_to('Login with Facebook', '/auth/facebook') %>
```


config/routes.rb
```
#fb omniauth logging in
  get '/auth/facebook/callback' => 'sessions#omniauth'
```


After our routing is setup I added a class method to the user model in order to use this method in the controller.


app/models/user.rb
```
 def self.from_omniauth(auth)
      @user = User.find_or_create_by(email: auth["info"]["email"]) do |user|
        user.password = SecureRandom.hex
      end
    end 
```


Our controller will need some logic in order to use the data that was received from the third party. The omniauth.auth key gives us our authentication hash which will contain information about the just authenticated user. I made a private method in my sessions controller to call on the omniauth.auth hash. I also created a seperate controller action for omniauth sessions or for creating a new user session because I found it easier.


app/controllers/sessions_controller.rb
```
def create
        @user = User.find_by(email: params[:user][:email]) #params from login form 
 
            if @user && @user.authenticate(params[:user][:password])
                session[:user_id] = @user.id #save the user id inside browser cookies and login
                flash[:alert] = "Login successful!"
                redirect_to user_path(@user)
            else 
                flash[:alert] = "Please try again!"
                redirect_to '/login'
            end 
    end 

    def omniauth 
        @user = User.from_omniauth(auth)
            @user = User.find_or_create_by(uid: auth['uid']) do |u|
            u.name = auth['info']['name']
            u.email = auth['info']['email']
            u.image = auth['info']['image']
                if @user.save 
                    session[:user_id] = @user.id 
                    redirect_to user_path(@user)
                else 
                    flash[:alert] = "Please try again!"
                    redirect_to '/login'
                end 
            end 
    end 
		
		 private 

    def auth
        request.env['omniauth.auth']
    end
end 
```


I'm still a little confused on some things but trying to see them clearer! Have a nice day :-)!
