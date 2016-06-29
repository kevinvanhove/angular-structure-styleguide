# Angular Allegro
A style guide for structuring angular applications

Working Draft - Version 0.1

## Quick intro
In most angular applications the complexity of the code grows fairly rapidly. Having no strategy leads to bulky controllers and fragmented components. One way to battle this is by splitting up code over multiple files, but it shouldn't end there. This paper answers the question '**where to put your code**' and introduces an architectual schema that is designed to eliminate code complexity.

To create a schema that works we first need to understand all the different angular development strategies one can take. We can clearly define 3 development strategies:

* View based 
* Controller based 
* Component based 

## Angular development strategies

### View based
> 1 view, 1 controller

<img src="https://raw.githubusercontent.com/kevinvanhove/angular-structure-styleguide/master/documentation/angular-view-based-strategy.jpg" width="488">

In a view based development strategy, on navigating to a certain route, a html view loads with a controller that contains the code for that entire view. This is an angular development strategy that is not a recommendation though i see it in many applications, even very big applications. The problem is that as the view grows in complexity, the controller follows suite, making it difficult to maintain and expand this section of the application. 

##### Code example
The router loads the templateUrl AND the controller.

```javascript
(function() {
	'use strict';

	angular.module('app').config(route);
	route.$inject = ['$routeProvider'];

	function route($routeProvider) {

		$routeProvider
		.when('/', {
			templateUrl: '/views/homepage.html',
			controller: 'homepageCtrl'
		})
		.when('/login/', {
			templateUrl: '/views/login.html',
			controller: 'loginCtrl'
		})
		.when('/about/contact', {
			templateUrl: '/views/about/contact.html',
			controller: 'contactCtrl'
		})

	}

})();
```

### Controller based
> 1 view, many controllers

<img src="https://raw.githubusercontent.com/kevinvanhove/angular-structure-styleguide/master/documentation/angular-controller-based-strategy.jpg" width="517">

A controller based development strategy allows for better management of the JS code. You keep the 1 view but you split the code into many controllers and many files. Alltough still not a recommendation it is a big improvement over the view based development strategy. The route is loading just the HTML view and the controllers are added using declarative syntax in the HTML. This allows for many controllers to be associated with 1 view.

##### Code examples
The router loads ONLY the view.

```javascript
(function() {
	'use strict';

	angular.module('app').config(route);
	route.$inject = ['$routeProvider'];

	function route($routeProvider) {

		$routeProvider
			.when('/', {
				templateUrl: '/views/homepage.html',
			})
			.when('/login/', {
				templateUrl: '/views/login.html',
			})
			.when('/about/contact', {
				templateUrl: '/views/about/contact.html',
			})

	}

})();
```

The view 'homepage.html' loads the controllers using ng-controller.

```html
<div ng-controller="homepageCtr as homeCtrl">

	<div ng-controller="whatIsNewCtrl as newCtrl">

		<!--some html code-->

	</div>

	<div ng-controller="TakeTheTourCtrl as tourCtrl">

		<!--some html code-->

	</div>

</div>
```

The controllers are added to your index.html file

```html
<body ng-app="app">

	<!--some html code-->

	<script src="app/app.js"></script>
	<script src="app/router.js"></script>
	<script src="app/homepage/homepageCtr.js"></script>
	<script src="app/homepage/whatIsNewCtrl.js"></script>
	<script src="app/homepage/TakeTheTourCtrl.js"></script>

</body>
```

### Component based
> many views, many controllers

<img src="https://raw.githubusercontent.com/kevinvanhove/angular-structure-styleguide/master/documentation/angular-component-based-strategy.jpg" width="515">

On top of improved management of the JS code, the component based development strategy will also allow for better management of the HTML code. This is the recommended development strategy in both angular 1 and angular 2. In angular 1 this can easily be achieved using angular directives/components. If you already applied the Controller based strategy, moving to the component based strategy is easy. You keep your controllers but move them over to an angular directive/component, creating a html tag. Each html tag will have only the HTML code associated with the controller. You now have many views and many controllers that are managed by angular directives/components. 

##### Code examples
The router loads the directive/component.

```javascript
(function() {
	'use strict';

	angular.module('app').config(route);
	route.$inject = ['$routeProvider'];

	function route($routeProvider) {

		$routeProvider
			.when('/', {
				template: '<homepage></homepage>',
			})
			.when('/login/', {
				template: '<login></login>',
			})
			.when('/about/contact', {
				template: '<contact></contact>',
			})

	}

})();
```

A template contains normal html code but also other directives/components

```html
<homepage>

	<div class="row">
		<div class="left">
			
			<what-is-new></what-is-new>
			
		</div>
		<div class="right">
			
			<take-the-tour></take-the-tour>
			
		</div>
	</div>

</homepage>
```

The directive/component definition attaches the controller and template to the html tag

```javascript
(function() {

	'use strict';

	angular.module('app').directive('homepage', homepage);
	homepage.$inject = ['constantSettings'];

	function homepage(settings) {

		return {
			restrict: 'E',
			controllerAs: 'ctrl',
			scope: {},
			bindToController: true,
			templateUrl: settings.path.components + 'homepage/homepage.tpl',
			controller: controller
		};

		function controller() {

			//directive code

		}

	}
})();
```


## The component tree
It's clear that we have to take the component based development strategy as our main development objective. In this strategy you create html tags. Just like `<html>` is the main tag, your application will have a main tag, for instance an `<app>` tag. 

This tag will contain your application modules, maybe a `<branding>` tag which could hold some pages, like a `<homepage>` or `<contact>` tag. Within those page tags you might find some other tags like `<what-is-new>` and  a `<take-the-tour>` tag.


### Component tree design
Yes you have to think in html tags now. Your app will progress fast and you will need a solid component tree design in order to manage your growing library of html tags. This section will discuss the design of such a component tree by identifying:

* modules
* pages
* parts

### Identifying modules
* An application should have at least 1 module
* a module contains pages

### Identifying pages
* an application should have at least 1 page
* a page contains parts

### Identifying parts
* an application should have at least 1 part

Yes, for a simple application you will have at least 4 components. So it's possible you end up with this simple structure:

```html
<app>
	<module>
		<page>
			<part></part>
		</page>
	</module>
</app>
```

or

```html
<app>
	<book>
		<homepage>
			<video-of-book></video-of-book>
		</homepage>
	</book>
</app>
```
or

```html
<sierra>
	<hulpvraag>
		<hulpvraag-detail>
			<hv-status-overzicht></hv-status-overzicht>
		</hulpvraag-detail>
	</hulpvraag>
</sierra>
```

## Architecture of a component
Readability and maintainability of a component are 2 of the drivers that lead to the propositions in this section. Closely following John Papa's excellent [angular style guide](https://github.com/johnpapa/angular-styleguide) we now dive deeper and look at how we architect the different code layers that make up a component.

![Overview](https://raw.githubusercontent.com/kevinvanhove/angular-structure-styleguide/master/documentation/angular-component-structure.jpg)

A component consists of 2 main parts, the **documentation** and the **implementation**. Within the implementation we can further distinguish 3 different code sections. **Initialization** code, component **event** registrations and finally all other component business **logic**:

##### Code example

```javascript
/**
 * @ngdoc component
 * @name moduleName.component:videoOfBook
 *
 * @description <video-of-book> component, description of this component.
 */


(function () {
  'use strict';

  angular.module('app').component('videoOfBook', {
    bindings: {},
    templateUrl: 'template.html',
    controller: controller
  });

  controller.$inject = [''];
  function controller(){

    var $ctrl = this;
    var localStateVariable;

    setupVideo();
    getPreferences();

    /****************************************************************/

    $ctrl.ui.pauze = pauzeVideo;
    $ctrl.ui.next = nextVideo;

    /****************************************************************/

    function setupVideo(){}
    function getPreferences(){}

    function loadListOfVideos(){}
    function prepareList(){}
    function checkAudioQuality(){}

    function pauzeVideo(){}
    function nextVideo(){}

  }

}());

```

##### Visual example
![Overview](https://raw.githubusercontent.com/kevinvanhove/angular-structure-styleguide/master/documentation/angular-component-structure-example.jpg)

### Documenting a component
Since a component should have a single responsibility within your application, it should also be easy to document one. The Angular Project uses [ngDoc](https://github.com/angular/angular.js/wiki/Writing-AngularJS-Documentation), a form of [jsdoc](http://usejsdoc.org/) for all of its documentation. We recommend using that as it exposes some angular specific [block and inline tags](https://github.com/angular/angular.js/wiki/Writing-AngularJS-Documentation#angularjs-specific-ngdoc-directives).

First we write documentation at the very top of the component. This explains the global functionality of the component, what it is, how it works and to what module it belongs. 

```javascript
/**
 * @ngdoc component
 * @name moduleName.component:videoOfBook
 *
 * @description <video-of-book> component, description of this component.
 */


(function () {}());

```

Next, for each important function in your component, include documentation at the top of the function.

```javascript
function setupVideo(videoType){
   /**
   * @ngdoc function
   * @name setupVideo
   * @methodOf moduleName.component:videoOfBook
   *
   * @description We need to set up the video player first. Additional documentation about this function...
   *
   * @param {string} videoType You can set the type of video needed. If 'forLatest' is provided then it will show a different box design
   * @returns {object} If needed, this function returns the video settings. 
   *
   * @example setupVideo('forLatest');
   */     

   var variableForFunction = '';

}

```



### Implementing component details

The implementation of the component is of course where all the action happens. To make this readable and maintainable we need to differentiate sections or types of code and group them. Currently we can identify 3 different groups:

* Initialization code 
* Event handlers 
* Business logic 

#### Initialization code
![Overview](https://raw.githubusercontent.com/kevinvanhove/angular-structure-styleguide/master/documentation/angular-component-initialization.jpg)

When reviewing a component you need to be able to instantly identify how the component will behave. This is done by knowing how it maintains state and by knowing how it starts. 

**Modeling local state** of the component is done first. We assign the invocation context of the controller function to the view model `$ctrl` (we use `$ctrl` here because angular 1.5 also uses `$ctrl` as the `controllerAs` value) and we define the local state variables. 

Then we call the functions that initialize the component when it loads, these are called the **constructor callbacks**, and show you how the component starts and flows.


```javascript

  function controller(){

    //local state
    var $ctrl = this;
    var localStateVariable;

    //constructor callbacks
    setupVideo();
    getPreferences();

  }

```

```html

  <h3>{{$ctrl.videoTitle}}</h3>
  <div>...</div>

```

#### Event handlers
![Overview](https://raw.githubusercontent.com/kevinvanhove/angular-structure-styleguide/master/documentation/angular-component-events.jpg)

User actions result in a flow of data from the template to the component controller. It should be clear, when inspecting the controller, where those user actions are and what they do. Event handlers should therefore be placed as close to the top of the controller.

Assign business logic functions to the **public template pointers**, as seen below:


```javascript
  function controller(){

    $ctrl.ui.pauze = pauzeVideo;
    $ctrl.ui.next = nextVideo;

  }
```
```html

  <button ng-click="$ctrl.ui.pauze()">Pauze video</button>
  <div>...</div>

```

#### Business logic
![Overview](https://raw.githubusercontent.com/kevinvanhove/angular-structure-styleguide/master/documentation/angular-component-logic.jpg)

In this section of the component we encapsulate the internals of the component. This includes calling factories and services, performing $http requests, creating helper functions, manipulate DOM, setting up $watches, using angular's 1.5 component life cycle hooks, working with promises, etc...

Try to work with **factories and services** as much as possible to reduce the size of your component controllers and to improve the communications between components by sharing code and state.

Write functions defined as **function declarations** over function expressions. Function declarations are hoisted to the top so there are no concerns over using a function before it is defined. Order is critical with function expressions.

Adopt simple **functional programming styles**. Always be on the lookout for creating small, repeatable actions that can be abstracted out into a function.

```javascript

  function controller(){

    function setupVideo(){}
    function getPreferences(){}

    function loadListOfVideos(){}
    function prepareList(){}
    function checkAudioQuality(){}

    function pauzeVideo(){}
    function nextVideo(){}

  }

```

*The below image is a quick look at how the code will be structured using this schema.*
![Overview](https://raw.githubusercontent.com/kevinvanhove/angular-structure-styleguide/master/documentation/overview.png)


*test*


## applicationPath

Combining the modulePath, pagePath and scopePath gives you the applicationPath. This allows you to document certain areas or locations in your app. It makes it also easier to send other developer to a certain location in your app... work in progress...

![Overview](https://raw.githubusercontent.com/kevinvanhove/angular-structure-styleguide/master/documentation/overview2.png)




you can't just add code to a module, you need at least 1 page and 1 scope
the goal of this is to get an applicationPath, down to the very lowest level
app > management > dashboard > home > status > users > data.users.logedInUsers




## things to write about
* datamodel solutions
* component communications
* 


http://blog.mgechev.com/2015/03/02/immutability-in-angularjs-immutablejs/
