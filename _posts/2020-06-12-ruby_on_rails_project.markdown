---
layout: post
title:      "Ruby on Rails Project"
date:       2020-06-13 02:05:53 +0000
permalink:  ruby_on_rails_project
---


**The App**

For my first Ruby on Rails project, I made an app that reviews haunted places! It allows users to add new haunted properties and review them. The review form has questions you would see on one of those paranormal investigator tv shows. For example, it asks what paranormal sights did you see? Were there any cold spots? Cold spots indicate the presence of a ghost! OooOoOooOO

**Models (relationships, validations and attributes)**

The app has three models. It has a property model, a review model and a visitor model. The visitor model is the same as a user model. I wish I named it user but when I first started making the app I thought visitor made the most sense because a visitor is visiting the haunted properties.

The associations and validations are: 

```
class Property < ApplicationRecord
    has_many :reviews
    has_many :visitors, through: :reviews #@property.reviews

    validates :name, :location, :description, presence: true 
end
```

```
class Visitor < ApplicationRecord
  has_secure_password

    has_many :reviews 
    has_many :properties, through: :reviews #@visitor.reviews

    validates :username, presence: true, uniqueness: true 
	end 
```

```
class Review < ApplicationRecord
    #join table
    belongs_to :visitor 
    belongs_to :property 

    validates :title, :sighting, :noise, :smell, :cold_spot, :summary, :date, presence: true 

end
```

This worked out pretty well for me except I wish I could easily tie a visitors username to a property form. I couldn't figure it out.

All of my tables attributes are listed above in the validations. I decided to validate the presence of all the attributes. I also added the uniqueness validation to my visitors username attribute.

**Scope methods**

I included two scope methods in the reviews model. The first one is called ordered_rating and it orders the highest / most terrifying reviews to the top. This scope method is found in the properties show page. This show page includes a properties name, location and description and below that is each review for that property. Those reviews are listed from scariest to least scary.

```
scope :ordered_rating, -> { order(rating: :desc) }
scope :most_recent_review, -> { order(date: :desc) }
```

The other scope method is called most_recent_review and it orders the most recent review to the top. It is found in the reviews index show page. This view page has a list of all of the reviews for all the properties and it is ordered from most recent to oldest. 

**Omniauth**

I used the google omniauth. Figuring out omniauth took a lot of time. However, I learned of a great blog that helped and I had help from my tech mentor! The blog post I referenced was created by a Flatiron graduate! One (of the several) issues I had was in the /config/initializers/omniauth.rb file I had:

```
Rails.application.config.middleware.use OmniAuth::Builder do
    provider :google_oauth2, ENV['GOOGLE_CLIENT_ID'], ENV['GOOGLE_CLIENT_SECRET']
  end
```

We googled the error that was thrown and found out I needed this:

```
Rails.application.config.middleware.use OmniAuth::Builder do
    provider :google_oauth2, ENV['GOOGLE_CLIENT_ID'], ENV['GOOGLE_CLIENT_SECRET'], skip_jwt: true
  end
```

**Nested resources **

The app has a nested new review route and a nested show view. In order to add a new review for a property a visitor (user) has to be logged in and on the property show page to make a new review of that particular property.

When a user is looking at the property show page they can click on and view the reviews for that particular property (nested show page). This show page took me a little while to figure out because I didn’t have the `property.reviews ` block written correctly.

I figured out the problem because when I hovered over the link for a review, the url didn’t make sense. The property_id number and review_id number in the url (property/property_id/review/review_id) seemed backwards or something. Then I saw the params on the error page and realized that I needed two arguments for the property_review_path and I originally only had one. Here is an image of the correct code:

```
 <% @property.reviews.ordered_rating.each do |r| %>
                <%= link_to "#{r.title}", property_review_path(@property, r) %> 
                Spookiness rating of <%= "#{r.rating}" %><br><br>
            <% end %>
```

**Validation errors**

I found out how to utilize validation errors in the ruby on rails guide. This is the code I have in views/layouts/_errors 

```
<% if object.errors.any? %>
  <div id="error_explanation">
    <h2><%= pluralize(object.errors.count, "error") %> prohibited this from being saved:</h2>

    <ul>
    <% object.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
<% end %>
```

I have this partial rendered in all the new view forms and also in the properties/show view page, and the properties/edit view page.

**Summary**

This project took me a lot of time but I feel like I learned a lot more from this project than I had from the previous two projects. I spent a ton of time using binding.pry, rails console,  googling information and utilizing rails/routes! I wish I had tried to use devise and bootstrap for this project. I will have to use them another time. Happy coding!


