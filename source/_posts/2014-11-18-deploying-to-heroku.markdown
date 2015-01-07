---
layout: post
title: "Deploying to Heroku"
date: 2014-11-18 18:20:41 -0500
comments: true
categories: 
---
###Six Steps

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; So, you've built your app and now want to get it live for all the world to see. Heroku is a great option for this. While Heroku has some very in-depth documentation, I constantly find myself looking up the steps again and again. With this post, I will detail the six steps you need to follow to quickly deploy a basic app to Heroku, while hiding any sensitive information such as personal keys used to interact with an API.   

**1. Create a free account**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; The first thing you are going to want to do is head over to https://signup.heroku.com/dc and set up a free account.

**2. brew install heroku**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Next, install heroku. My favorite way to do this is on the command line via using Homebrew.

**3. heroku create**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Now, you're all set up to create your app. CD into the your app's directory and run `heroku create`. Heroku will create a url to your app with a ridiculous default name and url, usually about nature. This command will also set up a heroku remote to use with git. 

**4. heroku config:set KEY=value**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; If you have any API keys or other sensitive data, Heroku provides you with convenient config variables to store and access this information. Let's say I have a super secret key. I can set the value by running `heroku config: set SUPER_SECRET_KEY=124kjh537b`. Then you can access this variable in your application as so, `ENV['SUPER_SECRET_KEY']`. Each time you set a new secret key for your application, Heroku will update and restart your app, in case you have already deployed at this point. 

**5. PostgreSQL**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; In order to successfully push your Heroku app up to the web, you will need to use PostgreSQL as your production database, at the very least. You can do this by adding the following line to your gemfile: `gem 'pg', group: :production` and changing the `adapter` in your `config/database.yml` to `adapter: postgresql`. While using PostgreSQL as your production database, and SQLite as your development database is okay in the short term, it is recommended to use the same database in both the development and production environments in order to avoid headaches in the future. 

**6. git push heroku master**

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; You are now ready to push your newly created app up to Heroku. Commit your changes if you repo is not already up-to-date and run `git push heroku master` which will push to your heroku remote. 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Your app is now up! Go check it out using the url Heroku provided to you in step 3. As I said before, these steps are for basic app deployment. Once you have a full-fledged Rails application, you will likely need t troubleshoot a little bit to get all of your styling to appear correctly. For this reason, I like to deploy early and often to make the debugging process and painless as possible.  


