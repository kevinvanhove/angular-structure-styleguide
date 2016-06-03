# Angular Structure Guide
A style guide for structuring modular angular applications

## Quick intro
In most angular applications the complexity of the code grows fairly rapidly. Having no strategy leads to bulky controllers and fragmented components. One way to battle this is by splitting up code over multiple files, but it shouldn't end there. This paper answers the question '**where to put your code**' and introduces an architectual schema that is designed to eliminate code complexity.

*The below image is a quick look at how the code will be structured using this schema.*
![Overview](https://raw.githubusercontent.com/kevinvanhove/angular-structure-styleguide/master/documentation/overview.png)

## applicationPath

Combining the modulePath, pagePath and scopePath gives you the applicationPath. This allows you to document certain areas or locations in your app. It makes it also easier to send other developer to a certain location in your app... work in progress...

![Overview](https://raw.githubusercontent.com/kevinvanhove/angular-structure-styleguide/master/documentation/overview2.png)

## View based development: 1 view, 1 controller
In a view based development strategy, on navigating to a certain route, a html view loads with a controller that contains the code for that entire view. This is an angular development strategy that is not a recommendation though i see it in many applications, even very big applications. The problem is that as the view grows in complexity, the controller follows suite, making it difficult to maintain and expand this section of the application. 

## Controller based development: 1 view, many controllers
A controller based development strategy allows for better management of the JS code. You keep the 1 view but you split the code into many controllers and many files. Alltough still not a recommendation it is a big improvement over the view based development strategy. The route is loading just the HTML view and the controllers are added using declarative syntax in the HTML. This allows for many controllers to be associated with 1 view.

## Component based development: many views, many controllers
On top of improved management of the JS code, the component based development strategy will also allow for better management of the HTML code. This is the recommended development strategy in both angular 1 and angular 2. In angular 1 this can easily be achieved using angular directives/components. If you already applied the Controller based strategy, moving to the component based strategy is easy. You keep your controllers but move them over to an angular directive/component, creating a html tag. Each html tag will have only the HTML code associated with the controller. You now have many views and many controllers that are managed by an angular directive/component. 

## The component tree
In a component based development strategy you create html tags. Just like <html> is the main tag, your application will have a main tag, for instance <app></app>. This <app> tag will contain your application modules, again as html tags, for instance <dashboard></dashboard> and in turn contain all other components. As such it's important to have a solid component tree design for your application. 

## Component tree design
This section will discuss a component tree design method by identifying modules, pages and scopes within an application.

### Identifying modules

### Identifying pages

### Identifying scopes



you can't just add code to a module, you need at least 1 page and 1 scope
the goal of this is to get an applicationPath, down to the very lowest level
app > management > dashboard > home > status > users > data.users.logedInUsers






