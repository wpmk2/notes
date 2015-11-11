##Quick Reference to UI-Router
UI Router works with application states instead of URLs.

###Install UI-Router
It can be installed using bower or npm:

	bower install angular-ui-router
	npm install angular-ui-router

###Use UI-Router
1. reference angular-ui-router.js in your html page.
2. add ui.router as a dependency on the angular module.
3. add ui-view directive to html as a placehoder for the views.

###Configure States
- States are configured on the config method of the application.
- Inject $stateProvider into the config function.
- Call the state method of the $stateProvider service to add states to the application.
- Each state is made up of a name and a configuration object.
- Use the configuration object to specify the url, controller and template for the state.

Example:

	var app = angular.module('app', ['ui.router']); // add ui.router as dependency to module
	app.config(function ($stateProvider) { // inject $stateProvider into the module's config function
		$stateProvider
			.state('home', { // add states by calling the .state function on $stateProvider
				url: '/', // optional url associated with the state (in this case the root of site)
				controller: 'AppController', // named controller or anonymous (inline) function
				controllerAs: 'app', // when using controller as syntax
				templateUrl: 'home/home.html' // specify a view associated with the state
			})
			.state('about', {
				url: '/about',
				controller: 'AboutController as about', // alternative controller as syntax
				templateUrl: 'about/about.html' // could be a function that returns the path to the view
			}) 
	});

###Activate a State

####From JavaScript
Inject the $state service into the controller and use $state.go('state name') to navigate to a state.

	$state.go('about');

####From html
Link to a state from any anchor tag using the url or the name of the state:

	<!-- using the name of the state, replace the href attribute with ui-sref -->
	<a ui-sref="home">Home</a> 
	
	<!-- using the url, remember to add the # and corresponding forward slash (/)-->
	<a href="#/about">About</a> 

####Useful $state Service Methods

	$state.go('about'); // go to the about state
	$state.reload(); // force the current state to be reloaded
	$state.get(); // retrieve an array of all configuration objects for the application states
	$state.get('about') // pass a state name to get the configuration object for that state

####Useful $state properties

	$state.current // returns a reference to the currently active state 
	$state.params // returns an object containing the parameters for the current state

####Useful $state Events

	$stateChangeStart 	// fired when starting to transition to a new state 
	$stateChangeSuccess	// fired if the transition to the new state succeeded
	$stateChangeError	// fired if the transition to the new state failed
	$stateNotFound		// fired if the state was not found

###Working with Parameters
States can set and recieve paramenters. 

####Methods for Declaring Parameters.

Add the name of the parameter to the url prefixing it with a colon:

	.state('users', {
		url: '/users/:id', // in this case the name of the parameter is id
		controller: 'AppController',
		
		// ... other properties of the state configuration object
	});

Use curly braces and the name of the parameter or a regular expression. If a regular expression is used, 
the parameter is only set if the value provided matches the regular expression (without the slashes):

	.state('users', {
		url: '/users/{id}', // in this case the name of the parameter is id
		url: '/users/{id:[0-9]{1,8}}, // in this case will only match for id of 1 to 8 numbers
		controller: 'AppController',
		
		// ... other properties of the state configuration object
	});

Use the params property on the state configuration object:

	.state('users', {
		url: '/users',
		params: {
			id: { value: 1234 } // declares a parameter id and gives it a default value
		},
		controller: 'AppController',
		
		// ... other properties of the state configuration object		
	})
	.state('expenses', {
		url: '/expenses/:id/:month',
		controller: 'AppController',
		// ... other properties of the state configuration object		
	});

####Passing Parameters
To pass the parameters on the url, just add the value for the paramenters after the route:

	http://localhost:8080/#/users/abcd // $stateParams.id will have the value abcd
	
	// below, $stateParams.id has the value 1234, $stateParams.month has the value october
	http://mysite.com/#/expenses/1234/october	 

To pass parameters using ui-sref, provide an object in parenthesis containing all the parameters:

	<!-- assuming we have a user object on the scope with an id property we can write -->
	<a ui-sref="users({ id: user.id })">View User Details</a> <!-- ui-router builds the correct url  -->	
	<a ui-sref="expenses({ id: 1234, month: 'march' })">View Expenses</a> <!-- or we can just pass the values  -->	

To pass parameters using href, just concatenate the base url with the parameters:

	<a href="#/users/{{ user.id }}">View User Details</a> <!-- from an object or value on the scope -->	
	<a href="#/expenses/1234/march">View Expenses</a> <!-- passing the value directly -->	

####Accessing Parameters on the Controller	
The parameters are added to the $stateParams object into a properties named after the parameters. 
In the examples above, the parameter will be stored on **$stateParams.id** and can be retrieved 
by the controller associated with the state.

	function AppController ($scope, $stateParams) { // inject $stateParams
	
		// the parameters passed into the state become properties on the $stateParams service
		$scope.userId = $stateParams.id; // in this case the property name is id (from :id on the state configuration object)
		$scope.month = $stateParams.month; // we can also access the month parameter
	}

###Resolve Property
We can add a resolve object as a property on the state configuration object. The resolve property is used to specify a set of
dependencies we can inject into the controller. Each dependency is defined as a property on the resolve object and is often 
implemented by calling a function that returns a promise. The promise will be resolved before transitioning to the state.
This ensures that the data is available to the controller before the state changes.

	.state('users', {
		// ... other properties
		
		resolve: {
			accounts: function (data$) { //  data$ is an angular service
				return data$.getAccounts(); // this function returns a promise
			} // ui-router will wait until the promise is resolved before changing state
		}
	});

We use the dependencies declared on the resolve object by injecting them into the controller.

	function UserController ($scope, accounts) { // inject the accounts property from the resolve object. Names must match
		$scope.accounts = accounts; // the accounts promise was resolved and the value can be assigned to a scope variable
	}

###Passing Arbitrary Data to States
It is possible to pass any data to a state by adding properties to the state configuration object. 
The properties added will be passed to the controller as properties in the $state.current object:

	.state('users', {
		// ... other properties
		
		data: { // we called it data, but it can be called anything other than the properties used by ui-router (resolve, url, etc)
			city: 'Provo',
			state: 'Utah'
		},
		secrets: { // another arbitrary piece of data passed to the controller
			favoriteColor: 'blue',
			catName: 'rafles'
		}
	});

To retrieve the data in the controller:

	function UserController ($scope) { 
		$scope.city = $state.current.data.city; // 'Provo'
		$scope.secrets = $state.current.secrets; // { favoriteColor: 'blue', catName: 'rafles' }
	}


**The properties added to any state are inherited by (added to the prototype of) child states.**

###onEnter and onExit Properties
Use the onEnter and onExit properties of the state configuration to specify callback functions that will execute when 
entering or exiting the state.

	.state('users', {
		// ... other properties
		
		onEnter: function ($http) { // we can inject services here
			console.log('Entered users state'); // add functionality
		},
		onExit: function ($log) { // we can inject any service here
			$log('Exiting users state'); // perform any action
		}
	});

