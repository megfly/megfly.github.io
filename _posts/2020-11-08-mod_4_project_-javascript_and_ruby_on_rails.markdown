---
layout: post
title:      "Mod 4 Project -Javascript and Ruby on Rails"
date:       2020-11-09 00:00:10 +0000
permalink:  mod_4_project_-javascript_and_ruby_on_rails
---


Wow module 4 is a whirlwind of learning fun new things! For my mod 4 project I made a single page application for pizza lovers to post some of the wild toppings pizzas they've eaten! This project was challenging, fun and made me super hungry 😋 . The mod 4 project includes a javascript front end and a ruby on rails backend. In my app a user can create a pizza by creating a pizza with a title and a description and they can edit the pizza title and description. A person can also add toppings to the pizzas and delete toppings from pizzas.

Over the course of building wildtoppings I had several issues that had to be worked out. Some of the more time consuming (and frequent 😂 ) issues involved working with  json, creating objects, grabbing specific nodes, adding event listeners (mostly WHERE to add them in my code), and good ol syntax& spelling errors. 

I unfortunately spent many hours searching for a node like this one: `<input id="add-a-topping-btn">` by searching for it by class instead of id like this: `document.queryselector('.add-a-topping-btn')`. Dang those .'s and #'s 😂 . 

I also had some troubles with event listeners. I learned that you can't put a submit event listener on a button tag, you must put it on the form tag! I also learned (by trial and error and lots of debuggers) that event listeners need to go in the perfect spot. I spent a lot of time figuring out that a click event listener located in an iteration, shouldn't be located in the iteration because it would only work some of the times.

Grabbing correct nodes was also a learning experience. I found it helpful to setup the nodes I needed to grab with an id that contained the dataset number. For example:

```
let myOption = document.createElement("option")
myOption.setAttribute("data-id", `${pizza.id}`)
myOption.setAttribute("id", `option-${pizza.id}`)
```

Above is an example of when a user is adding a topping they have a dropdown that has a list of all the pizzas. So they type in their topping then select a pizza from the dropdown that they want to add the topping to. By setting the pizza "id" attribute with the pizzas actual id it made it easier for me to grab that specific pizza and manipulate that node/data in the code.

Figuring out how I wanted to render and use json data was time consuming and this was not a one time error for me! I used the 'fast_jsonapi' gem. fast_jsonapi is a gem that provides convention to the way our json is formatted. After using the bundled generator the gem creates serializer files for us.  The attributes must be included and we can include associated relationships! This is how my pizza serializer is setup:

```
class PizzaSerializer
  include FastJsonapi::ObjectSerializer
  attributes :title, :description, :toppings #associated relationship
end
```

Next we must render it in our controller actions in order to have access to it:

```
    def index 
        pizzas = Pizza.all 
        render json: PizzaSerializer.new(pizzas)
    end 
```

This is how it renders my pizza json (im using a googlechrome extension for JSON formatting which is wonderful...without it the data has no spacing and is very difficult to read):

```
{
"data": [
{
"id": "1",
"type": "pizza",
"attributes": {
"title": "Pickle Pizza",
"description": "Pickles on pizza!",
"toppings": [
{
"id": 1,
"ingredient_name": "Ranch Dressing",
"created_at": "2020-11-08T21:54:25.800Z",
"updated_at": "2020-11-08T21:54:25.800Z",
"pizza_id": 1
},
```

And lastly fetching json and turning it into objects with constructors also was a learning experience. I had troubles when I was making new objects but my new object declaration didnt match my constructor. This is what my pizza constructor looks like:

```
class Pizza {
    constructor(pizza, pizzaAttributes) {
        //debugger
        Pizza.all = []

        this.id = pizza.id
        this.title = pizzaAttributes.title
        this.description = pizzaAttributes.description
        this.toppings = pizzaAttributes.toppings
        Pizza.all.push(this)
    }
```

In my constructor argument I have pizza and pizzaAttributes because this was the easiest way to access the data I needed in the json. I ran into issues when I created a new object using `new Pizza(pizza.data)`. This was because I was only using one argument and it didnt match my constructor which has two arguments. This is how the pizza fetch looks now:

```
fetch(PIZZAS_URL, configObj)    
            .then(response => response.json())
            .then(pizza => {
    
                    let brandNewPizza = new Pizza(pizza.data, pizza.data.attributes)
```

Hooray two arguments! 

I will definitely be studying a lot before my assessment 😀. Have a nice weekend yall!



