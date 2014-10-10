---
layout: post
title: "Getters and Setters"
date: 2014-10-09 08:51:53 -0400
comments: true
categories: 
---
###It all makes sense now...

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;When creating a class in Ruby, one must use getters and setters in order to read and write to various instance variables of a class instance. For the longest time I didn't fully understand these getters and setters. I understood that `attr_reader` was used to read instance variables, `attr_writer` was used to write to instance variables and `attr_accessor` was used to both get and set instance variables. Beyond knowing the differences between the three, I didn't comprehend what was going on under the hood. I figured Ruby auto-magically allowed for reading and writing and I just had to accept it. 


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Let's say we have a class called `Guitar` and we wanted to read and write to the instance variable of `@brand`. 


```ruby
class Guitar 
end  

sweet_axe = Guitar.new
 => #<Guitar:0x007f9aaa250ac0>
sweet_axe.brand 
 =>NoMethodError: undefined method 'brand' #<Guitar:0x007f9aaa250ac0>
sweet_axe.brand = "Fender"
 =>NoMethodError: undefined method 'name=' for #<Guitar:0x007f9aaa250ac0>
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;As you can see above, we get a NoMethodError without a getter and setter present. When I dug a little deeper, trying to understand various Stack Overflow posts, I realized that `attr_reader` and `attr_writer` were basically just aliases for the following Ruby methods. 

####attr_reader :brand

```ruby
def brand
  @brand
end  
```
####attr_writer :brand

```ruby
def brand=(str)
  @brand = str
end
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Simple enough, right? Wasn't for me. As soon as I saw the `def method=(str)` syntax, I panicked. "What the h3ll is that?" I thought. Is that an argument with a default value? Almost looks like it, but not quite. I assumed it was some crazy Ruby syntax I had never seen before. I simply didn't understand. It all finally clicked when I learned <strong>the `=` sign is just part of the method name.</strong> It's that easy. The `=` sign is nothing special. All you are doing is passing in an argument to the method and setting the instance variable to it.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;With this understanding I can now make as many instances of the guitar class as I want, give them a brand and build some other cool methods using that brand method. Let's see how this works, using an `attr_accessor` instead of an `attr_reader` and `attr_writer` or the `brand` and `brand=` methods.

```ruby 
class Guitar 
attr_accessor :brand

  def strum_that_guitar
    puts "Now strumming my new #{brand}!"
  end
end  

sweet_axe = Guitar.new
 => #<Guitar:0x007f9aaa250ac0>
sweet_axe.brand = "Fender"
 => "Fender"
sweet_axe.brand
 => "Fender"
sweet_axe.strum_that_guitar
Now strumming my new Fender!
```



