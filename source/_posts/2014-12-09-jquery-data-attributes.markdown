---
layout: post
title: "jQuery Data Attributes"
date: 2014-12-09 09:29:25 -0500
comments: true
categories: 
---
###Ever need some Ruby in your JS?

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Yesterday, I was implementing a Google map in a Rails project. I needed to pass coordinates from a controller to a Google maps script. This is where jQuery data attributes come in. You can't just throw an ERB tag in a script, but you can assign embedded ruby to a data attribute. 

```html
 <h1 class='big-text' data-latitude='<%= @user.latitude %>' data-longitude='<%= @user.longitude %>'></h1>
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Now, I can assign my data attributes to a JS variable for use in the Google maps script. 

```javascript
var lat = $("h1.big-text").data('latitude')
var lng = $("h1.big-text").data('longitude') 
```
