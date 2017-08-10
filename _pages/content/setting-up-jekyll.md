---
layout: page
title: Setting up Jekyll
use_math: true
tags:
- coding
- jekyll
- under-construction
---

# Setting it up under Windows

To start using Jekyll under Windows 10, I first upgraded to the most recent available version (Windows 10 Pro, version 1703, build 15063.483), enabled developer mode and installed Windows Subsystem for Linux (WSL). I then enjoyed a brief period of using bash until my laptop spontaneously restarted while I was away, after which bash refused to start. Restarting the computer did not help and neither did reinstalling WSL. But before that happened, I installed ruby, ruby-dev, gcc, make and the Jekyll bundle and was able to start the local server and build a simple site.

After some initial confusion, I realized the default behavior of Jekyll is to put a link to every single page on the site on the top navigation bar. This is definitely not what I want since I plan to have a lot of pages with content and only a few index pages ("Science & Electronics", "Photography", etc.) on the navbar.

It was a bit hard to find instructions for this online but in the end it turned out to be quite easy. I looked at the source of the rendered site in my browser and copied the header part. I saved it into `_includes/header.html` and opened it in vim. Then I replaced the explicitly rendered hyperlinks to all the sites with

{% highlight liquid %}
{% raw %}
{% for page in site.pages %}
{% if page.tags contains "navbar" %}
<a class="page-link" href="{{page.url}}">{{page.title}}</a>
{% endif %}
{% endfor %}
{% endraw %}
{% endhighlight %}

This means Jekyll will go through all the pages (it does not care about the directory structure, simply searches through the whole tree) and insert links to those that have a `navbar` tag. Tags are assigned to the pages in their front matter (the header of the file where the layout and the title is specified), for example as
{% highlight text %}
---
layout: page
title: Setting up Jekyll
tags:
- coding
- jekyll
- under-construction
---
{% endhighlight %}


# Setting it up under Ubuntu

When bash under Windows stopped working, I went to my Ubuntu installation and did the following:

{% highlight shell %}
apt-get install ruby
apt-get install rubygems
apt-get upgrade ruby
{% endhighlight %}

However, even after this, the installed version was 1.9 but Jekyll installation was asking for 2.0. A bit of googling told me to add a new repo:

{% highlight shell %}
apt-add-repository ppa:brightbox/ruby-ng
apt-get update
apt-get install ruby2.4
{% endhighlight %}

After this,
{% highlight shell %}
apt-get install ruby2.4-dev
gem install jekyll
{% endhighlight %}

When I tried to run Jekyll after this, I got an error 'Could not find jekyll-feed-0.9.2'. This went away after I gem-installed jekyll-feed, only to be replaced by the same error but with 'jekyll-minima'. Fortunately, this was the last missing dependency and after installing it, things started to work.

# Setting up MathJax

It is rather easy to include TeX formulas rendered by MathJax (online only). Kramdown, the default markdown processor, supports it. One just needs to call the MathJax scripts. I followed the instructions given on this [blog](http://haixing-hu.github.io/programming/2013/09/20/how-to-use-mathjax-in-jekyll-generated-github-pages/).

If I am not mistaken, the script can be in principle called anywhere in the code of the page before the formulas. But I think it is good practice to do it in the html head. That being said, I already modified the `header.html` file at this point (to change the navbar behavior) and I would prefer not to override more than necessary, so I just included the script call in there as well. It goes as follows:

{% highlight html %}
{% raw %}
{% if page.use_math %}
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    TeX: {
      equationNumbers: {autoNumber: "AMS"}
    },
    tex2jax: {
      inlineMath: [['$','$']],
      displayMath: [['$$','$$']],
      processEscapes: true,
    }
  });
</script>
<script type="text/javascript"
  src="http://cdn/mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
{% endif %}
{% endraw %}
{% endhighlight %}

One then needs to set the `use_math` variable in the front matter of the page (simply `use_math: true`) and start typing math:
\[
  R_{\mu\nu}-R g_{\mu\nu}/2+\Lambda g_{\mu\nu} = 8\pi G T_{\mu\nu}/c^4.
\]

# The journey continues - Jekyll under Cygwin

I would still like to avoid switching between my Windows and Ubuntu installation, so I tried getting Jekyll to work under Cygwin. Needed to install `ruby-devel` (in order not to have to use the annoying `setup.exe` for adding packages, I got the nice `apt-cyg` tool which works just like `apt-get` under Ubuntu). When `gem install jekyll` still didn't work without errors, I found a forum which suggested installing `ruby-pkg-config` and `libffi-devel`. This solved it for me. But I still couldn't run jekyll because it wasn't added to the `PATH` variable. I found the executable in `~/.gem/ruby/2.3.0/gems/jekyll-3.5.1/exe/`, so I added this path to `.bash_profile` (adding the line `export PATH="~/.../exe:$PATH"`). Then there was just a bunch of missing dependencies of Jekyll such as `bigdecimal` or `jekyll-feed`. I gem-installed them all and now it's finally working.

As a little update: When I was trying to repeat the installation under Cygwin on my work computer, I got stuck at the `gem install jekyll` step again. It turned out I also had to install `gcc-core`, `gcc-g++` and `automake1.15` (I do not know if they are all strictly necessary but the combination works).
