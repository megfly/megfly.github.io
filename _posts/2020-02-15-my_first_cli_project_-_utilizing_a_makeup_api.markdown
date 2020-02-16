---
layout: post
title:      "My First CLI Project - Utilizing a Makeup API"
date:       2020-02-16 00:38:39 +0000
permalink:  my_first_cli_project_-_utilizing_a_makeup_api
---



Wow, this was a challenging experience!! For our first project, we had to build our own Ruby gem! The Ruby gem needed to provide a Command Line Interface (CLI) to an external data source (either by scraping a website or utilizing an API) in order to demonstrate our ability to write object-oriented ruby code.


I decided that I wanted to go the API route and found a free one that piqued my interest... yay makeup! I chose to utilize an API because they are usually kept pretty constant whereas scraping HTML can be less reliable. If the layout of the webpage changed then I would have to fix my code in order for it to work again. So, for my first project I thought an API may be easier to deal with. I had a lot of challenges while working on my project but I learned a ton (especially about github)! Binding.pry was the most useful tool ever and I greatly appreciate the help I received from our cohort tech mentor and the learn instruct videos. 


My CLI project begins by asking the user if they would like to see a list of makeup products or if they just want to exit. If the user decides to see a list of products, then a list of ten items appears. The program then asks which product they would like to learn about and the user will type in the name of a product from the list. After the user types in the product name, the program will give them the name of the product, the brand, the product type and a description. After they receive details on the product they can either go back to the product list or exit the program. It sounds so simple but it took me so long! Especially when I got stuck.


One of the most challenging parts was getting the users chosen product (ex. Lip Gloss) to display its details (name, brand, category and description). I spent a long time trying to figure out what was wrong. After using a binding.pry, it said that my "Lip Gloss" was returning an empty array. Whaaaaaat!!!! Eventually, after watching my tech mentor's live lecture I realized that the products in my product list have capital letters for each word, so I needed the user's input to be: "Lip Gloss", not "Lip gloss", not "lip gloss", not "LIP GLOSS". It had to match the exact spelling in order for the program to pull the info from the API.  So, in order to make each first word a capital I changed my users input variable to make sure my users input (no matter what they typed) passed the correct capital and lowercase letters. I looked on stack overflow and found this to work:

`input = gets.strip.split(/ |\_|\-/).map(&:capitalize).join(" ")`

From what I understood, this code will split my users words (even if its lip_gloss) with a space, then the .map is a way to call a method on each item and next we capitalize each letter and use join(" ") to turn the words into a string.


Wow I have a lot to learn!!!! I can't wait to learn more!! Happy coding!






