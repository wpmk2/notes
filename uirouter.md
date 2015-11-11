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
- States are configure on the config method of the application.
- Inject $stateProvider into the config function.
- Call the state method of the $stateProvider service to add states to the application.
- Each state is made up of a name and a configuration object.
- Use the configuration object to specify the url, controller and template for the state.

Example:

	var app = angular.module('app', ['ui.router']); // add ui.router as dependency to module
	app.config(function ($stateProvider) { // inject $stateProvider into the module's config function
		$stateProvider
			.state('home', { // add states by calling the .state function on $stateProvider
				url: '/', // specify an optional url associated with the state (in this case the root of site)
				controller: 'AppController', // specify a controller (could be 'AppController as app')
				controllerAs: 'app', // specify an alias here if not using "controller as syntax" shown in parenthesis above.
				templateUrl: 'home/home.html' // specify a view associated with the state
			})
			.state('about', {
				url: '/about',
				controller: 'AboutController as about', // alternate syntax
				templateUrl: 'about/about.html' 
			}) 
	});

###Activate a State

####From JavaScript
Inject the $state service into the controller and use $state.go('state name') to navigate to a state.

	$state.go('about');

####From html
Link to a state from any anchor tag using the url or the name of the state:

	<a ui-sref="home">Home</a> <!-- using the name of the state replace the href attribute with ui-sref -->
	<a href="#/about">About</a> <!-- using the url, remember to add the # and corresponding forward slash (/)-->

####Useful $state Service Methods

	$state.go('about'); // go to the about state
	$state.reload(); // force the current state to be reloaded
	$state.get(); // retrieve an array of all configuration objects for the application states
	$state.get('about') // pass a state name to get the configuration object for that state

####Useful $state properties

	$state.current // returns a reference to the currently active state 
	$state.params // returns an object containin the parameters for the current state

####Useful $state Events

	$stateChangeStart 	// fired when starting to transition to a new state 
	$stateChangeSuccess	// fired if the transition to the new state succeeded
	$stateChangeError	// fired if the transition to the new state failed
	$stateNotFound		// fired if the state was not found

###Passing Parameters
States can set and recieve paramenters. 

Differente ways of declaring a parameter.

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
	});

To pass the paramenter on the url, just add the value for the paramenter after the route:

	http://localhost:8080/#/users/abcd // $stateParams.id will have the value abcd
	http://mysite.com/#/users/1234	 // $stateParams.id will have the value 1234

To pass the parameter using ui-sref we pass an object in parenthesis containing all the parameters:

	<!-- assuming we have a user object on the scope with an id property we can write -->
	<a ui-sref="users({ id: user.id })">View User Details</a> <!-- ui-router builds the correct url  -->	
	<a ui-sref="users({ id: 1234 })">View User Details</a> <!-- or we can just pass the value  -->	

To pass the parameter using href, just concatenate the base url with the params:


	<a href="#/users/{{ user.id }}">View User Details</a> <!-- from an object or value on the scope -->	
	<a href="#/users/1234">View User Details</a> <!-- passing the value directly -->	

	
The parameter is added to the $stateParams object into a property with the parameter name. 
In the examples above, the parameter will be stored on **$stateParams.id** and can be retrieved 
by the controller associated with the state.

	function AppController ($scope, $stateParams) { // inject $stateParams
		// the value passed into the url is on a property on $stateParams named after the parameter
		$scope.userId = $stateParams.id; // in this case the property name is id (from :id on the state configuration object)
	}