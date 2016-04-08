---
layout: post
author: Ira Levin
title:  "Creating Modal Dialogs with AngularJS and Custom Directives"
date:   2016-03-17 14:58:09 -0700
categories: jekyll update
---
# Introduction
Adding modal dialog boxes to your web pages is a very common task in our line of work.  The idea is pretty simple: a box (e.g., div) is positioned somewhere on the page, usually centered.  The box has a high z-index so it appears to be floating on top of the underlying page.  I like to get fancy and include styling that gives the box a border radius and maybe a shadow.  In addition, I sometimes like to gray out the underlying page as a visual queue and to let the user know that he/she needs to address the dialog first, before attempting to interact with the main page's UI.  Inside the box, you can put anything you want from a simple message with an "OK" (or "Close" button) to more sophisticated form that includes any number of visual elements.

With a bit of CSS, it's fairly easily roll out your your own custom modal using a div that's shown when the box is opened and hidden when it's closed.  But rather than reinvent the wheel, most developers reuse existing templates, plenty of which are out there on the Web and are open source.  For example, I've used the standard jQuery and Bootstrap modals quite a bit on previous projects.  However; they can be a bit limited.  In other words, there may not be a lot of config options you can choose from to make the modal look and behave exactly the way you want.  This is not typically a big problem as the standard modals do the trick most of the time.  However; if you do need special features for your modals, then you're back to rolling out your own.

Another thing to consider when using a standard modal is whether or not it's designed to play nice with AngularJS.  If your page is Angularized, then you may want to try the UI Bootstrap modal, designed by the Angular UI team.  Not ony doees the UI Bootstrap modal work seamlessly  *with* Angular, it's actually written *in* Angular.  It has a lot of config options too, giving it a good deal of flexibility.  Most *normal* developers (not us) would likely shy away from looking under the hood to see how the UI Bootstrap modal is implemented, mostly because there's no need to do so when developing a typical web application.  However; if you're of a curious sort (as I know you are), I'll give you the short run down.  The UI Bootstrap modal is nothing more than a custom Angular `directive`.  The code is open source, so it's no big secret.  Feel free to check it out at [UI Bootstrap][ui-bootstrap].

If you're planning to roll out your own modal, then why not implement it as a custom Angular directive?  The benifits would include being able to make the modal look and behave exactly as you like, while also taking advantage of full compatibility with Angular.  It's really not that hard.  This blog post will show you how, step by step!

# What Are Angular Directives?
So what exactly are angular directives?  Angular directives are markers on DOM elements.  In fact, Angular is *built* using directives, so if you've been using Angular, then you've no doubt been using directives.  For example, `ng-app`, `ng-controller`, `ng-repeat`, `ng-click` and `ng-show` are all implemented as directives.  Note that all of the above examples are of directives that are marked as *attributes* on their respective DOM elements.  As you'll see, you can also create directives that are marked as *element names*.

Directives contain *behavior* that is attached to the DOM element containing the marker.  The general rule of thumb is, if the marker changes the behavior of the element then it should be marked as an attribute.  On the other hand, if the directive creates a new element with all new behavior, then it should be marked as an element name.

Directives can insert HTML, manipulate the DOM and execute code.  For a comprehensive set of information about creating directives, you can go to [Creating Custom Directives][creating-custom-directives].  However; it's not necessary to read through that now, as I will explain things as we go through our simple example.  Just keep in mind that directives can do a whole lot more than create modals, so you'll want to keep the above link handy as a reference.

# Creating a Simple Modal
Let's start with a simple modal, implemented as a directive, that displays a message and includes a close button.  I've created a plunk here: [Simple Modal Plunk][simple-modal-plunk].

The example is split into the following files:

<table border="1">
	<thead style="background-color: lightgray">
		<th>File</th>
		<th>Description</th>
	</thead>
	<tbody>
		<tr>
			<td>index.html</td>
			<td>Main HTML page</td>
		</tr>
		<tr>
			<td>modalDialog.html</td>
			<td>Modal dialog template</td>
		</tr>
		<tr>
			<td>script.js</td>
			<td>Angular code behind</td>
		</tr>
		<tr>
			<td>style.css</td>
			<td>Custom CSS styling</td>
		</tr>
	</tbody>
</table><br />
First, let's take a look at the head section of the index.html file:

{% highlight html %}
<head>
  <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.2/css/bootstrap.min.css" rel="stylesheet" data-semver="3.3.2" data-require="bootstrap@*" />
  <script src="http://code.jquery.com/jquery-2.1.4.min.js" data-semver="2.1.4" data-require="jquery@*"></script>
  <script src="//maxcdn.bootstrapcdn.com/bootstrap/3.3.2/js/bootstrap.min.js" data-semver="3.3.2" data-require="bootstrap@*"></script>
  <script data-require="angular.js@1.3.15" data-semver="1.3.15" src="https://code.angularjs.org/1.3.15/angular.js"></script>
  <link rel="stylesheet" href="style.css" />
  <script src="script.js"></script>
 </head>
{% endhighlight %}

As you can see, we've included bootstrap and jQuery.  We use bootstrap to style some of the elements on our page, but we don't use it to create the modal.  jQuery is needed for Angular.  If you omit jQuery, Angular will include a stripped down version of jQuery, but I always like to have the full jQuery on hand in case I need it.  Finally, we include Angular as well as our own custom CSS and JavaScript files.

Now let's take a look at the body of our index.html file:

{% highlight html %}
<body ng-controller="indexCtrl">
  <h4>Display a modal dialog with AngularJS</h4>
  <hr />
  <button ng-click="toggleModal()" class='btn btn-warning btn-sm' 
          style="margin: 5px 0px 10px 10px;width: 150px;">Open Modal</button><br />
	<!-- Angular JS Directive Modal -->
  <modal-dialog height="150px" width="750px" show="modalShown">
    <div class="row">
      <div class="col-md-12" style="padding: 0px 10px 0px 10px;border-bottom: 1px solid grey;">
        <h3>Header</h3>
      </div>
    </div>
    <div class="row">
      <div class="col-md-12">
        <p>This is an important message</p>
      </div>
    </div>
   </modal-dialog>
</body>
{% endhighlight %}

First we include a simple header for our page followed by a horizontal rule and a button that, when clicked, will open our modal dialog.  Next, and this is the most important part, we introduce a new element called `modal-dialog`.  The HTML inside this new element is the content of the modal, which as you can see relies on the bootstrap grid system for its layout.

Also note that the new modal-dialog element contains height and width attributes.  This sets the size of the modal.  The new element also contains a funny looking attribute named show, which is set to "modalShown".  I'll explain the meaning of the show attribute in a moment.

Finally, note that the body tag in our index.html file contains an Angular ng-controller directive.  We'll implement this angular controller (called indexCtrl) in our script.js file in addition to our custom directive.

## The JavaScript

Now let's take a look at the contents of the script.js file:

{% highlight js %}
angular.module('app', []);

angular.module('app').controller('indexCtrl', function($scope) {
  $scope.modalShown = false;
  $scope.toggleModal = function() {
    $scope.modalShown = !$scope.modalShown;
  };
});

angular.module('app').directive('modalDialog', function() {
  return {
    restrict: 'E',
    scope: {
      show: '='
    },
    replace: true, // Replace with template
    transclude: true, // Insert custom content inside the directive
    link: function(scope, element, attrs) {
      scope.dialogStyle = {};
      if (attrs.width) {
        scope.dialogStyle.height = attrs.height;
      }
      scope.hideModal = function() {
        scope.show = false;
      };
    },
    templateUrl: 'modalDialog.html'
  };
});
{% endhighlight %}

At the top of the file, we create an Anglular module for our app, appropriately named "app"

{% highlight js %}
angular.module('app', []);
{% endhighlight %}

Next we have the implementation of our Angular controller:

{% highlight js %}
angular.module('app').controller('indexCtrl', function($scope) {
  $scope.modalShown = false;
  $scope.toggleModal = function() {
    $scope.modalShown = !$scope.modalShown;
  };
});
{% endhighlight %}



[ui-bootstrap]: https://angular-ui.github.io/bootstrap/
[creating-custom-directives]: https://docs.angularjs.org/guide/directive
[simple-modal-plunk]: http://plnkr.co/edit/3DvMp4OoZLNaMvJQlO4C
