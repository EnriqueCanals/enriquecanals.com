---
layout: blog
title: Using Rack Applications Inside GWT Hosted Mode
summary: Using Rack Applications Inside GWT Hosted Mode
date: 2009-09-21
postNum: 090212009
comments: true
---

#__September 21, 2009__

This guide will show you how you can use JRuby to run any Rack application inside Google Web Toolkit's (GWT) hosted mode server so your interface and your backend are of the Same Origin.

##Background

GWT has two ways of interacting with a server: GWT Remote Procedure Call (RPC) and plain HTTP (XHR). GWT-RPC is a high level library designed for interacting with server-side Java code. GWT-RPC implements the GWT Remote Service interface allowing you to call those methods from the user interface. Essentially, GWT handles the dirty work for you. However, it only works on Java backends that can implement that interface. Since most of my backends are Sinatra/Rack applications, I'll be using the plain HTTP library.

##The problem

Due to the restriction of the Same Origin policy, the interface served out of GWT's development, or Hosted Mode server can only make requests back to itself. If you were using real servlets or GWT's RemoteService this wouldn't be an issue; but since Rack applications listen on their own port, you cannot make requests from GWT to our application without resorting to something like JSONP or server-side proxying. This leaves you having to compile our interface to HTML/JS/CSS, which is lengthy process, and serve it from the origin of the Rack application to see our changes.

##The solution

Since I wanted to develop using GWT's development environment with a Rack backend, I devised a way to use jruby-rack to load arbitrary Rack applications alongside our interface.

  1. Download and unpack the latest GWT for your platform (mine being linux) and goto it:

{% highlight bash linenos %}
wget http://google-web-toolkit.googlecode.com/files/gwt-linux-1.7.0.tar.bz2tar -xvjpf gwt-linux-1.7.0.tar.bz2cd gwt-linux-1.7.02 .Download the latest jruby-complete.jar:
{% endhighlight %}

{% highlight bash linenos %}
wget http://repository.codehaus.org/org/jruby/jruby-complete/1.3.1/jruby-complete-1.3.1.jarmv jruby-complete-1.3.1.jar jruby-complete.jar
{% endhighlight %}

  2. Download the latest jruby-rack.jar

{% highlight bash linenos %}
wget http://repository.codehaus.org/org/jruby/rack/jruby-rack/0.9.4/jruby-rack-0.9.4.jarmv jruby-rack-0.9.4.jar jruby-rack.jar
{% endhighlight %}

  3. Create an app with webAppCreator:

{% highlight bash linenos %}
./webAppCreator -out MySinatra com.example.MySinatracd MySinatra
{% endhighlight %}

  4. In order for this to work you have to package any gem dependencies your backend needs (sinatra, in our case) as jars within your application. For Sinatra it looks like this:

{% highlight bash linenos %}
java -jar jruby-complete.jar -S gem install -i ./sinatra sinatra --no-rdoc --no-ri jar cf sinatra.jar -C sinatra .
{% endhighlight %}

  5. Add jruby-complete.jar, jruby-rack.jar, sinatra.jar (and any other jars you've created) to the libs target of your build.xml:

{% highlight xml linenos %}
<target name="libs" description="Copy libs to WEB-INF/lib">
  <mkdir dir="war/WEB-INF/lib" />
  <copy todir="war/WEB-INF/lib" file="${gwt.sdk}/gwt-servlet.jar" >
  <copy todir="war/WEB-INF/lib" file="${gwt.sdk}/jruby-complete.jar" />
  <copy todir="war/WEB-INF/lib" file="${gwt.sdk}/jruby-rack.jar" />
  <copy todir="war/WEB-INF/lib" file="${gwt.sdk}/sinatra.jar" />
</target>
{% endhighlight %}

  6. Add these lines right after `<web-app>` in war/WEB-INF/web.xml:

{% highlight xml linenos %}
<context-param>
  <param-name>rackup</param-name>
  <param-value>
    require 'rubygems'
	require './lib/sinatra_app'
	map '/api' do run MyApp  
	end
  </param-value>
</context-param>
<filter>
  <filter-name>RackFilter</filter-name>
  <filter-class>org.jruby.rack.RackFilter</filter-class>
</filter>
<filter-mapping>
  <filter-name>RackFilter</filter-name>
  <url-pattern>/api/*</url-pattern>
</filter-mapping>
<listener>
  <listener-class>org.jruby.rack.RackServletContextListener</listener-class>
</listener>
{% endhighlight %}

Note: All you're doing here is passing the contents of a config.ru file into the `<param-value>` element for the `<context-param>` (make sure this is HTML encoded!). This states that any request to /api is to be handled by your Sinatra application and not GWT's Hosted mode servlet.

  7. Create your Sinatra backend and place it in war/WEB-INF/lib/sinatra_app.rb

{% highlight ruby linenos %}
require 'sinatra'
require 'open-uri'

class MyApp < Sinatra::Base

  get '/showpage' do
    open('http://www.yahoo.com').read
  end

  get '/helloworld' do
    'hello world'
  end

end
{% endhighlight %}

  8. Run your new awesome setup:

{% highlight bash linenos %}
ant hosted
{% endhighlight %}

Now when you navigate to http://localhost:8888/api/helloworld orhttp://localhost:8888/api/showpage you should see the Sinatra application being served via GWT.
