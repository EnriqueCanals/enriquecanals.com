---
layout: blog
title: Getting Spree on Heroku in a Few Minutes
summary: Getting Spree on Heroku in a Few Minutes
date: 2012-02-24
postNum: 02242012
comments: true
---

#__February 24, 2012__

Below is a simple step by step guide on how to launch a Spree site on Heroku.

This guide assumes you're using:

  1. OS X

  2. Homebrew for managing packages

  3. RVM for managing local versions of Ruby

  4. Ruby 1.9.3 and Rails 3.2

##Ruby

Ruby 1.9.3 is what Heroku's Cedar Stack requires so get that going:

{% highlight bash %}
rvm install 1.9.3
{% endhighlight %}

Afterwards reload RVM

{% highlight bash %}
rvm reload
{% endhighlight %}

##Spree

Below are a few of needed items for Spree to run correctly

Install the current version of Spree (1.0.7)

{% highlight bash %}
gem install spree -v=1.0.7
{% endhighlight %}

Spree defaults to PostgreSQL, so we'll be using it:

I recommend using the Postgres app from <http://postgresapp.com/>.

Alternatively you can get it using Homebrew.

{% highlight bash %}
brew install postgresql
{% endhighlight %}

If you're installing PostgreSQL for the first time then read the build notes.

Now the gem:

{% highlight bash %}
gem install pg
{% endhighlight %}

If you don't already have it, install ImageMagick

{% highlight bash %}
brew install imagemagick
{% endhighlight %}

##Heroku

Install it:

{% highlight bash %}
gem install heroku
{% endhighlight %}

Also, you'll need the spree_heroku gem, which enables the use of s3 to store images/assets.

{% highlight bash %}
gem install spree_heroku
{% endhighlight %}


##Here we begin

Create a new rails app using postgreSQL as the default DB:

{% highlight bash %}
rails new mySpreeApp -d postgresql
{% endhighlight %}

Then navigate into your app directory and configure your settings in _config/database.yml_

Create the database:

{% highlight bash %}
bundle exec rake db:create:all
{% endhighlight %}

Install Spree:

{% highlight bash %}
spree install
{% endhighlight %}

Follow the prompts for the default options to load in the sample data, products, and run the migrations.

Now check if everything installed correctly. Run the application and you should see a sample Spree store with some products.

{% highlight bash %}
rails s
{% endhighlight %}

At this point install the spree_heroku gem to link our AWS S3 bucket.

{% highlight bash %}
gem 'spree_heroku', :git => 'git://github.com/joneslee85/spree-heroku.git'
{% endhighlight %}

{% highlight bash %}
bundle install
{% endhighlight %}

Read the info on joneslee85's gitHub page to see the configuration options you have for the  spree_heroku gem: <https://github.com/joneslee85/spree-heroku>

Now initiate a Git repo for the application.

{% highlight bash %}
git init
{% endhighlight %}

{% highlight bash %}
git add .
{% endhighlight %}

{% highlight bash %}
git commit -m "First Commit"
{% endhighlight %}

For this guide we'll be connecting our app and s3 bucket to Heroku directly instead of manually creating an s3.yml in our config folder ([read the docs on spree-heroku](https://github.com/joneslee85/spree-heroku)). You'll need to create a new Bucket on S3.

Login to Heroku
{% highlight bash %}
heroku login
{% endhighlight %}

and enter your credentials...

Now create your app on Heroku:

{% highlight bash %}
heroku create
{% endhighlight %}

This will create a new app on Heroku using a randomly generated name.

Add in S3 credentials.

{% highlight bash %}
heroku config:add S3_KEY='YOUR_KEY'
heroku config:add S3_SECRET='YOUR_SECRET_KEY'
heroku config:add S3_BUCKET='YOUR_BUCKET_NAME'
{% endhighlight %}


Finally install Spree for the remote heroku instance:

{% highlight bash %}
heroku run rails g spree:install
{% endhighlight %}


and run

{% highlight bash %}
heroku apps:open
{% endhighlight %}

Your new Spree store with sample products should now be open in a browser window.
