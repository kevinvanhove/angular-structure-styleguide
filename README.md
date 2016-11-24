# Angular Structure Guide
A style guide for structuring angular applications

Working Draft - Version 0.1

1. [Quick intro](#quick_intro)
2. [Angular development strategies](#angular_development_strategies)
3. [The component tree](#the_component_tree)
4. [Architecture of a component](#architecture_of_a_component)
5. [Component communications](#component_communications)

<a name="quick_intro"></a>
## Quick intro
In most angular applications the complexity of the code grows fairly rapidly. Having no strategy leads to bulky controllers and fragmented components. One way to battle this is by splitting up code over multiple files, but it shouldn't end there. This paper answers the question '**where to put your code**' and introduces an architectual schema that is designed to eliminate code complexity.

To create a schema that works we first need to understand all the different angular development strategies one can take. We can clearly define 3 development strategies:

* View based 
* Controller based 
* Component based 

<a name="angular_development_strategies"></a>
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

<a name="the_component_tree"></a>
## The component tree
It's clear that we have to take the component based development strategy as our main development objective. In this strategy you create html tags. Just like `<html>` is the main tag, your application will have a main tag, for instance an `<app>` tag. 

This tag will contain other tags, maybe a `<branding>` tag which could hold some pages, like a `<homepage>` or `<contact>` tag. Within those pages you might find some other tags like `<what-is-new>` and  a `<take-the-tour>` tag. 

All these tags within tags are angular 1 or 2 defined components and they make up the component tree of your application. Planning a component tree design is a first step in creating your application.


### Component tree design
Yes you have to think in html tags now. Your app will progress fast and you will need a solid component tree design in order to manage your growing library of html tags. This section will discuss the design of such a component tree by identifying:

* Global components
* Local components

### Global components
Global components are the reusable bits and pieces of your application. You might have a global `<video-of-book>` component that just displays a video of your book along with a caption and a titel. Or you could have a global `<buy-form>` component that contains a form where people can buy your book. 

### Local components
A local component, for instance `<homepage>`, is part of your main application component tree and encapsulates individual **pages, sections or parts** of your application. A local component is not meant to be reusable like a global component. 

The image below shows an example of a local component tree (left side) and its reusable global components (to the right).

<img src="https://raw.githubusercontent.com/kevinvanhove/angular-structure-styleguide/master/documentation/angular-local-vs-global-components.jpg" width="607">

Your application should have only 1 local component tree starting with the root component, for instance your `<app>` tag. This single component tree will hold your complete application structure and is put together using a combination of local components and global components.

##### Code example

```html
<app> //local

    <homepage> //local

        <book> //local
            <homepage-book-try></homepage-book-try> //local
            <homepage-book-buy> //local
                <buy-form></buy-form> //global
            </homepage-book-buy>
        </book>

        <videos> //local
            <homepage-videos-trailer> //local
                <video-of-book></video-of-book> //global
            </homepage-videos-trailer> //local
            <homepage-videos-list></homepage-videos-list> //local
        </videos>

    </homepage>
    
    <contact> //local
        <buy-form></buy-form> //global
    </contact>

    
</app>
```










<a name="architecture_of_a_component"></a>
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

In this layer of the component we encapsulate the **internals** of the component. This includes calling factories and services, performing $http requests, creating helper functions, manipulate DOM, setting up $watches, using angular's 1.5 component life cycle hooks, working with promises, etc...

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
<a name="component_communications"></a>
## Component communications 
There are 2 recommend approches for a tree of components to interact with each other, in most cases you should combine these methods.

* Using the **component require property** 
* Through **property binding** (input/output)

Of course there are more methods, like $broadcast, $emit and a standard angular service, but we will focus on the recommend approche for component based development.

Continuing our example of an app where you can buy a book. Let's say that, when arriving on the buy form page, you want to present the user a list of books they showed interest in. We know this information because they visited that books product page.

### The require property
A directive, and consequently a component, has a require property available on the definition object. Using this property and requiring another component (of the same component tree) will give you a reference to that components **controller**. 

<img src="https://raw.githubusercontent.com/kevinvanhove/angular-structure-styleguide/master/documentation/angular-component-communications-require.png" width="360">

This allows you to **expose an api** on a parent component and make available it's methods to any child component. The 'require' approach can only be used if you are 100% sure that all components are or will be in the **same component tree**. If this is not the case than you are left with the 'angular service' approach.

#### Code examples
Exposing the api methods, `addBook()` and `getBooks()` on the controller of the `<app>` component. These methods will be called to get the list of visited books and to add a new book to the list.

```javascript

/**
 * @ngdoc component
 * @name app.component:app
 *
 * @description The main application component.
 */


(function () {
  'use strict';

  angular.module('app').component('app', {
    template: 'root component template',
    controller: controller
  });

  controller.$inject = [];
  function controller(){

    var $ctrl = this;
    var booksWatched = ['LatestBook'];

    /****************************************************************/

    $ctrl.addBook = addBook;
    $ctrl.getBooks = getBooks;

    /****************************************************************/

    function addBook(bookName){

      booksWatched.push(bookName);

    }
    function getBooks(){

      return booksWatched;

    }

  }

}());

```

Require the `<app>` component using `require:{api: '^app'}`, this binds the components controller to `$ctrl.api`. Call the method `$ctrl.api.addBook('ES6 - The Essentials');` to add a book to the `booksWatched` array in the `<app>` component.

```javascript

/**
 * @ngdoc component
 * @name app.component:book
 *
 * @description The <book> component.
 */


(function () {
  'use strict';

  angular.module('app').component('book', {
    bindings: {},
    require: {api: '^app'},
    template: 'Product page of the book: ES6 - The Essentials',
    controller: controller
  });

  controller.$inject = [];
  function controller(){

    var $ctrl = this;

    setupAngularHooks();

    /****************************************************************/

    function setupAngularHooks(){
      $ctrl.$onInit = function(){
        addThisBook();
      }
    }

    function addThisBook(){

      $ctrl.api.addBook('ES6 - The Essentials');

    }

  }

}());

```

The `<buy-form>`component also requires the `<app>` component and gets the list of watched books using the line `$ctrl.booksWatched = $ctrl.api.getBooks();`. The result is rendered to the user in the template `<ul><li ng-repeat="book in $ctrl.booksWatched">{{book}}</li></ul>`.

```javascript

/**
 * @ngdoc component
 * @name app.component:book
 *
 * @description The <buy-form> component.
 */


(function () {
  'use strict';

  angular.module('app').component('buyForm', {
    require: {api: '^app'},
    template: '<ul><li ng-repeat="book in $ctrl.booksWatched">{{book}}</li></ul>',
    controller: controller
  });

  controller.$inject = [];
  function controller(){

    var $ctrl = this;

    setupAngularHooks();

    /****************************************************************/

    function setupAngularHooks(){
    	$ctrl.$onInit = function(){
    		getWatchedBooks();
    	}
    }

    function getWatchedBooks(){
      $ctrl.booksWatched = $ctrl.api.getBooks();
    }


  }

}());

```


### An angular service
...

## things to write about in next update
* datamodel solutions
* naming conventions
* directory structure
