---
layout: post
author: Ira Levin
title:  "Creating Modal Dialogs with Angular JS and Custom Directives"
date:   2016-03-17 14:58:09 -0700
categories: jekyll update
---
# Introduction
Adding modal dialog boxes to your web pages is a very common task in our line of work.  The idea is pretty simple: a box (e.g., div) is positioned somewhere on the page, usually centered.  The box has a high z-index so it appears to be floating on top of the underlying page.  I like to get fancy and include styling that gives the box a border radius and maybe a shadow.  In addition, I sometimes like to gray out the underlying page as a visual queue and to let the user know that he/she needs to address the dialog first, before attempting to interact with the main page's UI.  Inside the box, you can put anything you want from a simple message with an "OK" (or "Close" button) to more sophisticated form that includes any number of visual elements as well as "Cancel" and "Save Changes" buttons.

With a bit of CSS, it's fairly easily roll out your your own custom modal using a div that's shown when the box is opened and hidden when it's closed.  But rather than reinvent the wheel, most developers reuse existing templates, plenty of which are out there on the Web and are open source.  For example, I've used the standard jQuery and Bootstrap modals quite a bit on previous projects.  However; they can be a bit limited.  In other words, there may not be a lot of config options you can choose from to make the modal look and behave exactly the way you want.  This is not typically a big problem as the standard modals do the trick most of the time.  However; if you do need special features for your modals, then you're back to rolling out your own.

Another thing to consider when using a standard modal is whether or not it's designed to play nice with Angular JS.  If your page is Angularized, then you may want to try the UI Bootstrap modal, designed by the Angular JS team at Google.  Not ony doees the UI Bootstrap modal work seamlessly with Angular JS, it has a lot of config options too, giving it a lot of flexibility.  Most *normal* developers (not us) would likely shy away from looking under the hood to see how the UI Bootstrap modal is implemented.  Mostly because there's no need to do so.  However; if you're of a curious sort (as I know you are), I'll give you the short run down.  The UI Bootstrap modal is nothing more than a custom Angular JS `directive`.  The code is open source, so it's no big secret.  Feel free to check it out at [UI Bootstrap][ui-bootstrap].

How about rolling out your own modal as a custom Angular JS directive?  The benifits include being able to make the modal look and behave exactly as you like, while also taking advantage of full compatibility with Angular JS.  It's really not that hard.  This blog post will show you how, step by step!

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
[ui-bootstrap]: https://angular-ui.github.io/bootstrap/
