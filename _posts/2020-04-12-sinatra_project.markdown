---
layout: post
title:      "Sinatra Project "
date:       2020-04-12 22:43:52 +0000
permalink:  sinatra_project
---


I decided to make an Aerial Move tracker for the Sinatra project. It is a journal type app where a user can track what they learn in their Aerial Arts class. This project was challenging but it was great to see the code come to life in a web browser. The most challenging part of the project was trying to keep track of all the moving parts that needed to be connected together. I used the corneal gem which was a tremendous help in setting up the files and gems.

I first started building my project by building the models.  I setup two Active Record tables. One for Users and the other for Aerial Entries. The Users table includes a user’s name, username and password (I used the bcrypt gem for password security). The Aerial Entries table includes a user_id, the move name, apparatus, difficulty, description and text box for a link to an image. After the tables were setup I setup the User class, an Aerial Entry class and their associations. My app’s User has many 
Aerial Entries and an Aerial Entry belongs to a user. Next, I setup the controllers (and the HTML views) which includes the flow of a user signing up, logging in, and logging out. The other controller is an Aerial Entry controller which includes the flow of a User creating new entries, editing their entries, viewing all entries and deleting their entries. Lastly, I played around with the CSS in order to style the pages.

An important aspect of our project was to include CRUD actions. The update route was challenging because we needed to remember that we have to put Rack::MethodOverride in our config.ru file in order to access the PATCH method. The PATCH method is a request method for making changes to an existing entry. Firstly we needed a get request in order to render the edit.erb page which contains the form for editing a given instance model. Then this form will send a patch request to our patch action. My patch method was written as: 

```
 patch '/aerial_entries/:id' do 
        set_aerial_entry
        if logged_in?
            if @aerial_entry.user == current_user && params[:move_name] !="" && [:apparatus] !="" && [:difficulty] !="" && [:description] !="" && [:image] !=""
                @aerial_entry.update(move_name: params[:move_name], apparatus: params[:apparatus], difficulty: params[:difficulty], description: params[:description], 
                image: params[:image])
                @aerial_entry.save
                redirect "/aerial_entries/#{@aerial_entry.id}"
            else 
                redirect "users/#{current_user.id}"
            end 
        else 
            redirect '/'
        end 
    end 
```

The above actions job is to find the correct entry, update it, save it and redirect to the User's show page. The set_aerial_entry method is a method that finds a User's particular Aerial Entry. Next we enter the first if/else statement. If the User is logged in they will be able to edit an entry. If they're not logged in then they are redirected to the '/' method. 

If the User is logged in, then they enter the second if/else statement. Here a User can update their entry but they will need to make sure that none of the fields are empty. If a field is empty they will be redirected to their Users show / profile page. If the fields are not empty, then it will be saved and they will be redirected to the instance of that particular Aerial Entry. 

There are several aspects of this app I would love to improve. I'd love to have more flash messages, play around with the CSS some more, and clean up some of the methods. Maybe in the future I can try to improve it!

