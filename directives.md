##Directives

###Types
There are three types:
1. Component: alson known as a widget. It is used to encapsulate a piece of user interface and it's behaviour.
2. Decorator: used to add functionality to an existing html tag, ng-click and ng-show are examples of decorator directives.
3. Structural / Templating: manipulates de DOM, examples of this type are ng-repeat and ng-if.

###Basic Syntax
In Javascript:

	angular.module('app').directive('dmStudentDetails', function () {
		return { 
			restrict: 'E', // type of directive (E: Element, A: Attribute, C: Class, M: Comment)
			templateUrl: 'students/studentDetails.html', // otional html content 
		}
	});

To use this directive in html:

	<dm-student-details></dm-student-details>

Notice the use of **camel case in JavaScript** and **snake case in html**. 
Not using snake case (snake-casing) in html is a common cause of errors.

It is good practice to prefix your directives with a two or three letter acronym related to your company or the project.
In the example above we used dm (for DevMountain). This helps us quickly identify component directives in our code
and avoid possible naming collisions with directives provided by any external libraries used in our application.

