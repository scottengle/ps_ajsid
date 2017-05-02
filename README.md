# Course Work for Pluralsight Course 'AngularJS In-Depth'

https://app.pluralsight.com/library/courses/angularjs-in-depth

# The History of AngularJS

Developed in 2009 by Misko Hevery. Publicly released as version 0.9.0 in October, 2010.

# Build a Strong AngularJS Foundation

* The Major AngularJS Players
  * Module
    * Container used to group your app together
  * Config
    * All configuration happens here
  * Routes
    * routes map the views and controllers
  * $Scope
    * the glue holding it all together
  * View
    * Directive
      * Components used in the Views
  * Controller
    * Service
      * shared resources used in the controller
* $compile
  * compiles DOM into a template function that can then be used to link scope and the View together
* $digest
  * processes all of the watchers on the current scope
* $apply()
  * is used to notify that something has happened outside of the AngularJS domain
  * forces a $digest cycle
* Model-View-Whatever
  * Really more of a Model-View-ViewModel
* Controller and $scope
  * $scope is the glue between the Controller and the View
  * The Controller is responsible for constructing the model on $scope and providing commands for the View to act upon
  * $scope provides context
* Controller Best Practices
  * should not know anything about the view they control
  * be small and focused
  * should not talk to other controllers
  * should not own the domain model
* View and Templates
  * The View is AngularJS compiled DOM
  * The View is the product of $compile merging the HTML template with $scope
  * The DOM is no longer the single source of truth
* Models and Services
  * Services carry out common tasks specific to the web application
  * Services are consumed via the AngularJS Dependency Injection system
  * Services are application Singletons
  * Services are instantiated lazily
* Routes
  * $route is used for deep linking URLs to Controllers and Views
  * Defined using $routeProvider
  * Typically used in conjunction with ngView directive and $routeParams service

## Coding an MVVM Application with routes

    angular.module('myApp', [])
      .config(function ($routeProvider) {
        $routeProvider
          .when('/', {
            templateUrl: 'boards.html',
            controller: 'BoardsCtrl'
          })
          .when('/:boardId', {
            templateUrl: 'board.html',
            controller: 'BoardCtrl'
          })
          .otherwise({
            redirectTo: '/'
          });
      })
      .controller('MainCtrl', ['$scope', 'BoardService', function($scope, BoardService) {
          $scope.boards = BoardService.getBoards();

          $scope.deleteBoard() = function (id) {
            console.log('ID', id);
          };
        }])
      .controller('BoardsCtrl', ['$scope', 'BoardService', function($scope, BoardService) {
        $scope.boards = BoardService.getBoards();
      }])
      .controller('BoardCtrl', ['$scope', '$routeParams', 'BoardService', 
        function($scope, $routeParams, BoardService) {
          var boardId = $routeParams.boardId;
          var board = BoardService.getBoard(boardId);
          $scope.boardTitle = board.title;
          $scope.notes = board.notes;
      }])
      .factory('BoardService', function() {
        var boards = [
          {id: 0, title: 'Board 00', content: 'Content Pending...'},
          {id: 1, title: 'Board 01', content: 'Content Pending...'},
          {id: 2, title: 'Board 02', content: 'Content Pending...'},
          {id: 3, title: 'Board 03', content: 'Content Pending...'},
          {id: 4, title: 'Board 04', content: 'Content Pending...'},
        ];

        var getBoards = function() {
          return boards;
        };

        return {
          getBoards: getBoards
        };
      });

# AngularJS Directives Basics

*Why A DSL?*

People find DSLs valuable because a well-designed DSL can be much easier to program with than a traditional library. This improves programmer productivity, which is always valuable. In particular, it may also improve communication with domain experts, which is an important tool for tackling one of the hardest problems in software development.

-- Martin Fowler

*Fixed Languages Are Broken*

[Without the ability to reprogram the language], programmers resort to repetitive, error-prone workarounds. Literally millions of lines of code have been written to work around missing features in programming languages.

-- Stuart Halloway

## Directive Definition Object

*The Simplified World View*

Most simple directives consist of three things (or less). Having a firm grasp on these three things will make advanced directives much more approachable.

*Link
*Controller
*DDO

The Directive Definition Object (DDO) tells the compiler how a directive is supposed to be assembled. Common properties are:

* link function
* controller function
* restrict
* template
* templateUrl

Example (from the AngularJS docs):

    angular.module('docsTimeDirective', [])
    .controller('Controller', ['$scope', function($scope) {
      $scope.format = 'M/d/yy h:mm:ss a';
    }])
    .directive('myCurrentTime', ['$interval', 'dateFilter', function($interval, dateFilter) {

      function link(scope, element, attrs) {
        var format,
            timeoutId;

        function updateTime() {
          element.text(dateFilter(new Date(), format));
        }

        scope.$watch(attrs.myCurrentTime, function(value) {
          format = value;
          updateTime();
        });

        element.on('$destroy', function() {
          $interval.cancel(timeoutId);
        });

        // start the UI update process; save the timeoutId for canceling
        timeoutId = $interval(function() {
          updateTime(); // update DOM
        }, 1000);
      }

      return {
        link: link
      };
    }]);

## Controller and Link Functions

*The Controller*

The controller is constructed furing the pre-linking phase. It receives $scope which is the current scope for the element.

*The Link Function*

The link function is where DOM manipulation happens 

It comes with `scope`, `element` and `attrs`

* `scope` is the same $scope in the controller function
* `element` is a jQuery or JQLite instance of the DOM element the directive is declared on
* `attrs` is a list of attributes declared on the element

# Server Side Integration with AngularJS

* REST and $http
* Promises
* Real Time Communication

*$http*

The `$http` service is a core Angular service that facilitates ommunication with the remote HTTP servers via the browser's XMLHttpRequest object or via JSONP.

The `$http` API is based on the deferred/promise APIs exposed by the $q service.

*$http Shortcut Methods*

* $http.get
* $http.head
* $http.post
* $http.put
* $http.delete
* $http.jsonp

*Promises*

The returned value of calling `$http` is a `promise`. You can use the `then` method to register callbacks. These callbacks receive a single argument - an object representing the response.

Example:

    angular.module('myApp')
      .factory('BoardsService', ['$http', '$q', function ($http, $q) {
        var baseUrl = 'https://www.myapp.com/';
        var userId = 1;

        var find = function() {
          var deferred = $q.defer();
          var url = baseUrl + 'users/' + userId + '/boards.json';

          $http.get(url)
            .then(function success(response) {
                ...
              },
              function error(reason) {
                ...
              });

          return deferred.promise;
        };

        return {
          find: find
        };
      }]);

# Testing With AngularJS

*The Environment*

* Karma is the scenario runner
* Karma is testing framework agnostic
* Easiest way to get up and running with Karma is with Yeoman

*Unit Testing*

* Test isolated pieces or units of logic
* We accomplish this with matchers and spies

*Matchers*

* Expectations are built with the function `expect` which takes a value, called with the `actual`
* It is chained with a `Matcher` function, which takes the expected value

    expect(a).toBe(b);
    expect(a).not.toBe(null);

*Spies*

* Jasmine's test doubles are called `spies`
* A spy can stub any function and tracks calls to it and all arguments
* There are special matchers for interacting with spies

    spyOn(mockUserService, 'userExists').andCallThrough();
    scope.userExists();
    expect(mockUserService.userExists).toHaveBeenCalled();

# Advanced AngularJS Directives

* Isolate Scope
* Compile
* Transclusion
* 3rd Party Integration

## Isolate Scope

* Does not prototypically inherit from its parent
* Prevents your directive from accidentally modifying data in the parent scope
* Basically defines the "API", or communication contract, for your directive

*Attribute Isolate Scope*

* Defined with an `@` symbol
* Binds local scope property to the value of a DOM element
* Unidirectional binding from parent to directive
* Result is always a string, since DOM attributes are strings

*Binding Isolate Scope*

* Defined with an `=` symbol
* Two-way binding between parent and directive
* Can define the binding as optional via `=?`
* Optimal for dealing with objects and collections

*Expression Isolate Scope*

* Defined with an `&` symbol
* Allows you to execute an expression on the parent scope
* To pass variables from child to parent expressions you must use an object map

*Transclusion*

* Compiles the contents of the element and makes it available to the directive
* Transcluded contents are bound to the parent scope
* Directive contents are bound to an isolate scope
* The two scopes are siblings
* Good for decorating existing elements on the DOM without destroying them
* Take the contents of this DOM element and copy it over to another DOM element

*Compile*

* Responsible for transforming the template DOM
* Very rarely used

# AngularJS Animations

The pattern is [class][event][destination] and stack on each other.

    .repeat-item.ng-enter.ng-enter-active
    .my-elm.ng-hide-add.ng-hide-add-active
    .my-animation.CLASS-add.CLASS-add-active

## CSS Transitions

Easiest and fastest way to attach animations. As long as the matching CSS class is present, then AngularJS will pick up the animation.

*CSS Animations*

* More extensive than transitions
* Does not require the destination class styling (a.k.a. `-active` class)

## JavaScript Animations

* Allows for more control over your animations
* Define your animations with the `animation` service
* Naming convention is still class based
* Make sure to call the `done` function when the animation is over

