# Service Portal: Style Guide

A modern & opinionated style guide for teams using Service Portal.

## Table of Contents

1. [Single Responsibility](#single-responsibility)
1. [Pure Functions](#pure-functions)
1. [controllerAs Syntax](#controlleras-syntax)
1. [$onInit](#oninit)
1. [Bindable Members at Top](#bindable-members-at-top)
1. [Function Declarations](#function-declarations)
1. [GlideQuery](#glidequery)
1. [Modules](#modules)
1. [Components](#components)
1. [One-time Binding](#one-time-binding)
1. [Small Functions](#small-functions)
1. [Linting](#linting)
1. [Code Format](#code-format)

## Single Responsibility

The [Single Responsibility Principle (SRP)](https://en.wikipedia.org/wiki/Single-responsibility_principle) states:

Every class, module, or function in a program should be focused and have one responsibility.

> Each software module should have one and only one reason to change. - Robert C. Martin

In gist, any given unit of code should have only one job and do it well. If the responsibility is scattered across different areas, it creates more touchpoints and increases the probability of creating bugs with future changes.

Apply the single responsibility principle (SRP) to all functions, scripts, components, and classes. Why use SRP?

* Makes the application cleaner
* Creates more modular code
* Easier to read and maintain
* Reduces bug count
* Produces more testable code

**[Back to top](#table-of-contents)**

## Pure Functions

Write [pure functions](https://en.wikipedia.org/wiki/Pure_function) when possible. These functions, when given the same input, will always return the same output. They produce no side effects. Pure functions are completely independent of outside state. Why use these?

* Easy to understand
* Easy to maintain
* Reduces bugs
* More testable

```javascript
const double = x => x * 2;
```

**[Back to top](#table-of-contents)**

## controllerAs Syntax

Use `controllerAs` syntax instead of `$scope` inside your client controller. Why?

* It is a common best practice
* Removes scope inheritance issues
* Eliminates having to inject `$scope` as a dependency

```javascript
api.controller = function() {
  var c = this;
  c.isVisible = true;
};
```

Avoid using `$scope` in a client controller unless needed.

```javascript
api.controller = function($scope) {
  $scope.isVisible = true;
};
```

Using `$scope` is fine when publishing and subscribing to events such as: `$emit`, `$broadcast`, or `$on`.

**[Back to top](#table-of-contents)**

## $onInit

Use the `$onInit` method to organize initialization code for your client controller.

From the AngularJS documentation:

> Called on each controller after all the controllers on an element have been constructed and had their bindings initialized (and before the pre & post linking functions for the directives on this element).

```javascript
api.controller = function() {
  var c = this;

  c.$onInit = function() {
    getTopics();
    getWidgets();
  };
};
```

**[Back to top](#table-of-contents)**

## Bindable Members at Top

Place the bindable members at the top of the client controller in alphabetical order. Why?

* Easy to read
* Quickly identify bindings on the HTML template

```javascript
api.controller = function() {
  var c = this;
  c.closeSurvey = closeSurvey;
  c.setRating = setRating;

  function closeSurvey() {
    /* */
  }

  function setRating() {
    /* */
  }
};
```

**[Back to top](#table-of-contents)**

## Function Declarations

As a general rule, use function declarations over function expressions. Why?

* Creates more readable code
* Keeps bindable members at the top
* Hides implementation details
* No concerns using a function before it is defined

```javascript
api.controller = function(coreService) {
  var c = this;
  c.formatDate = formatDate;

  function formatDate(date) {
    return coreService.formatDate(date);
  }
};
```

Avoid using function expressions with `$scope` in your client controller.

```javascript
api.controller = function($scope, coreService) {
  var c = this;

  $scope.formatDate = function(date) {
    return coreService.formatDate(date);
  };
};
```

**[Back to top](#table-of-contents)**

## GlideQuery

Use [GlideQuery](https://docs.servicenow.com/bundle/tokyo-application-development/page/app-store/dev_portal/API_reference/GlideQuery/concept/GlideQueryGlobalAPI.html) as an interface to the Now platform. `GlideQuery` offers a modern, functional, and safer alternative to `GlideRecord`. Why?

* Creates readable & expressive code
* Simplifies queries
* Easy to catch bugs

```javascript
function userExists(userID) {
  return new GlideQuery('sys_user')
    .where('user_name', userID)
    .selectOne('user_name')
    .isPresent();
}
```

When used within a scoped app, it must be prefixed with the global scope.

```javascript
new global.GlideQuery('sys_user')
```

**[Back to top](#table-of-contents)**

## Modules

Set a module only once in its own file. Why?

* Separates configuration from module definition
* Provides an identifiable place to set module configuration

Do this to define a module in a UI script.

```javascript
angular.module('employee-portal', []);
```

Get instances of the module this way.

```javascript
angular.module('employee-portal');
```

**[Back to top](#table-of-contents)**

## Components

Use components to construct independent and reusable bits of code. Components can be registered with the `.component()` helper method. Why use components?

* Simple configuration
* Promotes modular code
* Optimized for component-based architecture

```javascript
(() => {
  const hamburgerMenu = {
    template: [
      '<button type="button" data-target="#nav-bar">',
      '<span class="sr-only">${Toggle nav}</span>',
      '<span class="icon-bar"></span>',
      '<span class="icon-bar"></span>',
      '<span class="icon-bar"></span>',
      '</button>'
    ].join('')
  };

  angular
    .module('employee-portal')
    .component('hamburgerMenu', hamburgerMenu);
})();
```

This code was crafted using a UI script.

**[Back to top](#table-of-contents)**

## One-time Binding

On the HTML template, use one-time bindings where needed to improve performance.

From the AngularJS documentation:

> One-time expressions will stop recalculating once they are stable, which happens after the first digest if the expression result is a non-undefined value.

```html
<div class="panel-heading">{{::options.title}}</div>
```

The syntax can easily be applied inside an `ng-repeat` as well:

```html
<ul>
  <li ng-repeat="user in ::c.users"></li>
</ul>
```

Note: be careful using one-time bindings in areas where the data could fluctuate in the future.

**[Back to top](#table-of-contents)**

## Small Functions

Write small functions. Do refactor large functions to smaller ones. Why?

* Creates more readable code
* Easy to maintain
* Promotes code reuse
* Easy to test

**[Back to top](#table-of-contents)**

## Linting

[ESLint](https://eslint.org/) helps you find and fix problems with your JavaScript code. Use it to lint your code. Why?

* Increases code quality
* Makes code more consistent
* Enforces best practices & patterns of development
* Helps catch bugs

The [curly](https://eslint.org/docs/latest/rules/curly) rule is one worth having. As a best practice, avoid omitting curly braces around blocks; it reduces code clarity and can lead to bugs.

```javascript
if (isDuplicate) nowGR.deleteRecord();
```

This statement can be rewritten as such:

```javascript
if (isDuplicate) {
  nowGR.deleteRecord();
}
```

## Code Format

[Prettier](https://prettier.io/) is an opinionated code formatter. Use it to automate code formatting. Why?

* Increases code readability
* Makes code more consistent
* Saves you time and energy
* Removes style discussion in code reviews

Here is a sample configuration for your codebase.

```yml
singleQuote: true
semi: true
tabWidth: 2
trailingComma: "es5"
```

**[Back to top](#table-of-contents)**
