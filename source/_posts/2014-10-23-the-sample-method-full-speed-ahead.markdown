---
layout: post
title: "The Sample Method: Full Speed Ahead"
date: 2014-10-23 17:45:37 -0400
comments: true
categories: 
---

###Sample: My new favorite method

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Just yesterday, I found out about the `sample` method. Using the `sample` method will randomly select an element from the array of your choice. 

```ruby 
numbers = [0,1,2,3,4,5,6,7,8,9]  
numbers.sample
 => 5
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; You can also pass `sample` an argument to return however many unique elements you would like.

```ruby
numbers = [0,1,2,3,4,5,6,7,8,9]  
numbers.sample(3)
 => [7, 0, 3]
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Prior to discovering the `sample` method, I accessed random elements using other methods.

```ruby
numbers = [0,1,2,3,4,5,6,7,8,9]
numbers.shuffle[0]
 => 7
numbers[rand(numbers.size)]  
 => 9
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Since these other methods also get the job done, I was curious as to what the upside of `sample` is. Aside from being much more elegant, IMHO, the `sample` method is also a lot faster. One can use Ruby's `Benchmark` module to test the speed of different lines of code. 

```ruby 
require 'benchmark'

numbers = (1..5000000).map { rand }
Benchmark.bmbm do |x|
  x.report("shuffle[0]") { numbers.shuffle[0] }
  x.report("[rand(numbers.size)]") { numbers[rand(numbers.size)]  }
  x.report("sample") {numbers.sample}
end 
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; I know the excitement is hard to contain, but before we test the speed of our random selection methods, let's walk through the above speed test. First, I create an array of 5 milllion different floats using the `map` method. Calling `rand` in the block with no argument will give you floats. If I had supplied `rand` with an argument, let's say `2`, the map method would return an array of 5 million integers with the possible values of `0` or `1`. If we pass in `3` to `rand` instead, we would get an array with the possible values of `0`, `1`, or `2`. You get the picture.       

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Now on the the actual test. By using the `bmbm` method on Benchmark, you can test the speed of several lines of code at once. This is preferable to the similar `bm` method, because the order of tests may affect their speed, based on memory use and garbage collection. Another cool feature of the `Benchmark` module is that you can pass in a string argument to the `report` method, which will label each test for clarity. Now the moment we've all been waiting for; let's see how fast this `sample` method actually is. 

`
                         user     system      total        real
shuffle[0]           0.290000   0.000000   0.290000 (  0.296134)
rand(numbers.size)   0.000000   0.000000   0.000000 (  0.000008)
sample               0.000000   0.000000   0.000000 (  0.000005)
`
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Looking at the "real" column above, which shows the elapsed time in seconds, we can see that the `sample` method is almost twice as fast as using [rand(numbers.size)]



