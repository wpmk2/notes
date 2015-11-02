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
- Call the state method of the $stateProvider service to add states the the application.
- Each states is made up of a name and a configuration object.
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

	<a ui-sref="home">Home</a> <!-- using the name of the state replace the href with ui-sref -->
	<a href="/about">About</a> <!-- using the url, remember to add the corresponding forward slash (/)-->

####Useful $state Service Methods

	$state.go('about'); // go to the about state
	$state.reload(); // force the current state to be reloaded
	$state.get(); // retrieve an array of all configuration objects for the applicarion states
	$state.get('about') // pass a state name to get the configuration object for that state

####Useful $state properties

	$state.current // returns a reference to the currently active state 
	$state.params // returns an object containin the parameters for the current state

####Useful $state Events

	$stateChangeStart 	// fired when starting to transition to a new state 
	$stateChangeSuccess	// fired if the transition to the new state succeeded
	$stateChangeError	// fired if the transition to the new state failed
	$stateNotFound		// fired if the state was not found

