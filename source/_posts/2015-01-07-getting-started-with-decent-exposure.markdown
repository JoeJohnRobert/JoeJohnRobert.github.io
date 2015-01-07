---
layout: post
title: "Decent Exposure"
date: 2015-01-07 10:22:05 -0500
comments: true
categories: 
---
###A quick guide for getting started with decent_exposure Ruby gem

{% img center /images/decent_guitar.png 450 450 'image' 'images' %}

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Recently, I've been exploring working with the decent_exposure gem. With decent_exposure you can clean up your controllers and better encapsulate your code. Instead of setting instance variables in each action of your controller, decent_exposure sets up methods in your controller which you can directly call in your view, thereby decreasing repetition. 

#####First steps
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; The first thing you are going to do is add `gem 'decent_exposure'` to your gemfile and then of course `bundle`.  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; For the purposes of getting to know decent_exposure, I put together a simple application where musicians or `axe_masters` can keep track of their many `guitars`. `Guitars` are nested as a resource under `axe_masters`.  

```ruby 
Rails.application.routes.draw do
  root 'axe_masters#index'
  resources :axe_masters do 
    resources :guitars
  end
end  
```
#####Controller setup
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; When you first look at a controller which has adopted the decent_exposure way, your first thought might be how little code there actually is. There are no instance variables to define and there is very little going on in each action. You set up some methods for your views using with a call to the `expose` method provided to you by the gem. Then you set up some redirecting for the `create`, `update` and `destroy` actions and that's about it. 

```ruby
class GuitarsController < ApplicationController
  expose(:axe_master) 
  expose(:guitar, attributes: :guitar_params) 

  def create
    if guitar.save
      redirect_to axe_master, notice: "Axe saved!"
    else
      render :new 
    end
  end

  def update
    if guitar.save
      redirect_to axe_master, notice: "Axe updated!"
    else  
      render :show
    end
  end

  def destroy
    if guitar.destroy
      redirect_to axe_master, notice: "Axe deleted!"
    else
      redirect_to axe_master
    end
  end
        
  private
    def guitar_params
      params.require(:guitar).permit(:make, :model, :color, :axe_master_id)
    end
end
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; As you can see above, rather than defining `@axe_master` and `@guitar` instance variables in each action, you call `expose` and pass the object you'd like to reference. Magic, right? Nope, you guessed it; decent_exposure relies on a few conventions. Straight from the documentation, the conventions are as follows. With the `guitar` object in mind:

**1. There is an object named `Guitar`.**

**2. `Guitar` has a `.find` method that takes an id.**

**3. There is a params method that returns a Hash-like structure which has either an id key or a guitar_id key.**

You will also see, that there is no need to define index, new or show actions. Normally, you would just set these up with instance variables, but using the `expose` method, decent_exposure serves them up for you. You might be wondering, how does decent_exposure decide whether `guitar` should be a new instance or one that's already persisted? If there is an `id` key in params, like on a `show` page, decent_exposure will try to find the `guitar` with that `id`. Othewise, it will create a new instance, like in a form in the `new` template. If you wanted to search by something other than the `id`, you can set a value to the `finder_parameter` as so: 

```ruby
expose(:guitar, finder_parameter: :guitar_id)
```

#####Use with strong_parameters
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Another layer of implicitness that I that was pretty cool, is the fact that you don't even have to build out your create or update actions. Normally you would do something like this: 

```ruby 
  def create
    @axe_master = AxeMaster.find(params[:axe_master_id])
    @axe_master.guitars.build(guitar_params)
    if guitar.save
      redirect_to axe_master, notice: "Axe saved!"
    else
      render :new 
    end
  end
``` 
With decent_exposure, finding the `axe_master` in question and building a a `guitar` from the object all happens implicitly. In order to use strong_parameters, however, there are two bits of code you must add. First, when you `expose` the `guitar` object, you have declare the name of your params method that decent_exposure will look for:

```ruby 
expose(:guitar, attributes: :guitar_params)
```

Second, as of Rails 4, you need to add the following three lines to your `application_controller.rb` file. 

```ruby
decent_configuration do
  strategy DecentExposure::StrongParametersStrategy
end
```

With this in place you are all set to use strong_params. 

#####Views
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Now that our controller is good to go, let's take a look at the views. Just as you guessed, we can call the methods we set up with `expose` in our views rather than using instance variables. First, we have our template for creating or updating a `guitar`:

```erb
<%= form_for [axe_master, guitar] do |f| %>
  <p>
    <%= f.label :make %>
    <%= f.text_field :make %>
  </p>
  <p>
    <%= f.label :model %>
    <%= f.text_field :model %>
  </p>
  <p>
    <%= f.label :color %>
    <%= f.text_field :color %>
  </p>
  <%= f.hidden_field :axe_master_id, value: axe_master.id %>
  <p> <%= f.submit 'Submit!' %> </p>
<% end %>
```

Next, we have our simple `axe_master` show page:

```erb
<%= link_to "New Axe", new_axe_master_guitar_path(axe_master) %>
<h1> <%= axe_master.name %>'s Page </h1> 
<h3> Guitars </h3>
  <ul> 
    <% guitars.each do |axe| %>
    <li> 
      <%= axe.make %> - <%= axe.model %>, <%= axe.color %> 
      <%= link_to "Edit", axe_master_guitar_path(axe_master, axe) %>
      <%= link_to "Delete", axe_master_guitar_path(axe_master, axe), :method => :delete, :confirm => "Are you sure?" %>  
    </li>
    <% end %>
  </ul>
```

As you can see, we reference the `axe_master`, `guitar` and `guitars` methods rather than using any instance variables. In creating the `guitars` method with `expose`, decent_exposure allows you to customize how you would like to define a collection of objects by supplying a block. Let's say you only wanted Fender guitars:

```ruby
expose(:guitars) { Guitar.where(make: 'Fender') }
``` 

In using the `guitars` method on the `axe_master` show page, I assumed you would have to at least explicitly define that you wanted `guitars` to only include those objects that belong to the `axe_master` whose page you are on by doing something like this:

```ruby
expose(:guitars) { axe_master.guitars }
``` 

Exposing just the `guitars` method is enough, however. The rest is implicit. Pretty cool. 

#####Customize and conquer
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Hopefully, this simple guide is enough to help you understand decent_exposure and get it up and running yourself. As mentioned before, decent_exposure relies a lot on convention, but there are many more ways to customize its use that aren't mentioned here. Head over to the [documentation](https://github.com/hashrocket/decent_exposure) to read up on these.