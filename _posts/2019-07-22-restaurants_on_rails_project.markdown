---
layout: post
title:      "Restaurants on Rails Project"
date:       2019-07-22 21:34:01 +0000
permalink:  restaurants_on_rails_project
---


What I ended up making is an app that users can look at, add, or review restaurants. They can also search restaurants by city, and look at the reviews by most recently added, star rating from high to low, or star rating from low to high. This project was definitely difficult for me, and for two reasons: Life and Code. (but really, when aren't these the reasons, haha) <br><br>

## Life
Throughout this entire process, so far, I've had to keep telling myself that it's ok to not hit every deadline I set for myself. That I need to remember that I'm doing this WITH a full-time job, and it's ok to change your schedule every once in awhile for the sake of self care. I've done some things recently that I'm trying to look at as a good change, instead of making myself feel bad for indulging in, while also trying to get back into some old habits.
1. Taking some schedule time off to do things that I want to do -- One of my best friends is getting married later this year, and I'm in the bridal party. Things for the wedding really revved up at the beginning of my rails project. Between the bridal shower, bachelorette weekend, dress fittings, hair and makeup consultations, and doing all of this while having to travel back and forth between DC and Philly, there has been a lot that I've felt guilty about. I've dedicated all of this time into her wedding, instead of code. As I've gone throughout the summer, I've really tried to change my mindset about this, and other things that I'm doing with my friends. It's ok to go offline, and live in the moment. I'm still kicking butt through this program, and my time will come, but I don't want it to come at the sacrifice of my friendships and memories to last a lifetime.
2. Scheduling out time to code -- For most of my time with Flatiron, I've had my calendar that has scheduled out my time at "real life" work, and my time to code. I kind of lost this trend right before my rails project, and it showed. Scheduling out specific times to do work is motivating for me, and because I haven't done it recently, getting in the mood to do work has been hard. I've *very* recently gotten back into this habit, and I'm hoping it helps my learning through JS go faster, and smoothly, since I'll have my time goals clearly set. 
3. Location, location, location -- Working at home is not ideal for me, personally, and I've stopped caring about that. Getting back in the groove of coding at WeWork, or Panera, or literally almost anywhere besides my living room is also something that I've committed to myself to start doing again. Going somewhere else to code, especially WeWork has really cut the distractions for me, and I've been able to get a lot of good work done, while staying *mostly* focused, and even meeting new people at times! <br>

## Code
One of my main struggles (but achievements) through this product has been nested forms, and not so much as making them, but making them work. I found myself in a pretty interesting, mostly difficult situation with one of my forms. <br>
This *was* my form for making a new restaurant:
```
<%= form_for @restaurant do |f| %>
    <%= f.label :name %>
    <%= f.text_field :name %><br><br>
    <%= f.label :street_address %>
    <%= f.text_field :street_address %><br><br>
    <%= f.label :genre %>
    <%= f.text_field :genre %><br><br>

    <p>Select a city:</p>
    <%= f.collection_select :city_id, City.all, :id, :name, include_blank: 'Select One' %>

    <p>Or add a new one:</p>
    <%= f.fields_for :city do |city_attributes| %>
        <%= city_attributes.label :name %>
        <%= city_attributes.text_field :name %>
    <% end %><br><br>

    <%= f.submit "Create Restaurant" %>
<% end %>
```
What I wanted to be able to do with my nested form, was create a new city, if the city you were looking for was not already an option through the collection_select. My Restaurant Model looked like:
```
class Restaurant < Application Record
    has_many :reviews
    has_many :users, through: :reviews
    belongs_to :city
		
	accepts_nested_attributes_for: city
```
And my restaurants#create controller method, and strong params looked like:
```
def create	    
        @restaurant = Restaurant.new(restaurant_params)
        @restaurant.build_city(id: params[:restaurant][:city_id])	
        if @restaurant.save!
            redirect_to restaurant_path(@restaurant)
        else
            @restaurant.build_city
            render :new
        end
end
```
```
def restaurant_params
    params.require(:restaurant).permit(:name, :street_address, :genre, :city_id, :search, city_attributes: [:name])
end
```
(The way my code is written above might not have worked in some other ways, as I was doing **A LOT** of trial and error to get this issue fixed)<br><br>
The issue I was having, was the city created through the nested form would not save. It would try to save my instance of a restaurant and then I would get a "City cannot be nil" error, or something along those lines. I was legit going crazy, because I could not figure out what was happening!! My city was getting passed through the params, or the city_id with the collection_select would be getting passed through, and then something would go terribly wrong at the save. And then, came my coding angel. What I/we/everyone who gave me any input with this, ended up doing, was creating a custom setter method. Creating a custom setter also fixes the issue to not create any duplicates. With `accepts_nested_attributes_for`, a new city would have been created every time, whether or not it already existed, and I had set validations that a city must have a unique name. With this custom setter, we can control the way cities are created while also creating cities *while* creating restaurants. What came of this was...<br>
Updated form:
```
<%= form_for @restaurant do |f| %>
    <%= f.label :name %>
    <%= f.text_field :name %><br><br>
    <%= f.label :street_address %>
    <%= f.text_field :street_address %><br><br>
    <%= f.label :genre %>
    <%= f.text_field :genre %><br><br>

    <p>Select a city:</p>
    <%= f.collection_select :city_id, City.all, :id, :name, include_blank: 'Select One' %>

    <p>Or add a new one:</p>
    <%= f.fields_for :city, @restaurant.build_city do |city_fields| %>
        <%= city_fields.label :name %>
        <%= city_fields.text_field :name %>
    <% end %><br><br>

    <%= f.submit "Create Restaurant" %>
<% end %>
```
Updated model/custom setter method:
```
class Restaurant < Application Record
    has_many :reviews
    has_many :users, through: :reviews
    belongs_to :city
		
    def city_attributes=(city)
        self.city = City.find_or_create_by(id: city_id)
        self.city.update(city)
    end
```
Updated restaurants#create:
```
def create
    @restaurant = Restaurant.new(restaurant_params)
        
    if @restaurant.save
        redirect_to restaurant_path(@restaurant)
    else
        @restaurant.build_city
        render :new
    end
end
```
After this IT WORKED!!! I could finally either create new cities, or select from any previously created city through collection_select. (And honestly, once this finally worked, a lot of my motivation for this project came back, haha.) This project definitely had a lot of opportunities for continued learning, and this was the one that probably stumped me the most!

