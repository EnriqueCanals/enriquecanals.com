---
layout: blog
title: Issues Installing Capybara-Webkit on OS X 10.9 (Mavericks)
summary: Fix issue with Qt and qmake on OS X 10.9 Mavericks
date: 2013-11-05
postNum: 11052013
comments: true
---

#__November 05, 2013__

**_Fix issues with Qt and qmake on OS X 10.9 Mavericks_**


I just recently decided to upgrade to OS X 10.9 from 10.8 and ran into a pretty crappy issue while installing the capybara-webkit gem. When running bundle install, I received the following error

{% highlight bash %}
  Building native extensions.  This could take a while...
  ERROR:  Error installing capybara-webkit:
  ERROR: Failed to build gem native extension.

  /Users/$USER/.rvm/rubies/ruby-2.0.0-p247/bin/ruby extconf.rb
  Command 'qmake -spec macx-g++' not available
{% endhighlight %}

After some searching I was able to find several posts mentioning that the most recent stable version of Qt isn't fully supported on 10.9. As such, I decided to try the beta which worked!

**Step 1:**
Download Qt 5.2.0-beta-1-clang [HERE](http://download.qt-project.org/development_releases/qt/5.2/5.2.0-beta1/).

**Step 2:**
Install it and include the Src files.

**Step 3:**
Symlink qmake into your /bin directory from the location where you installed Qt. The default location is in your home directory. Open a shell and do something like:

{% highlight bash %}
  ln -s /Path/to/where/you/installed/Qt5.2/5.2.0-beta1/clang_64/bin/qmake /usr/local/bin/qmake
{% endhighlight %}

That should do it. Now when you gem install capybara-webkit it shouldn't bitch about qmake not being available.

Now go compute!
