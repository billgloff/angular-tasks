# AngularTasks

A set of `rake` tasks to compile a CoffeeScript and SASS in an Angular project that follows the Angular-Seed structure.

## Why?

I find myself follwoing the layout pattern found in the Angular-Seed example, but I don't like having to put all the controllers in one
file. There should be some help there with building several controller files into one.

## Requirements

Depending on what parts you use, you will need a CoffeeScript compiler and Compass to compile the Scss.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'angular-tasks', :git => 'git://github.com/markborcherding/angular-tasks.git
```

And then execute:

```
$ bundle
```

Add the following to your `Rakefile`:

```ruby
AngularTasks::TaskLib.new
```

## Configuration

Eventually you will be able to add a bit of config to the tasks. The following is an example of what it _will_ be like:

```ruby
AngularTasks::TaskLib.new do |config|
  config.coffeescripts_dir = 'app/js'
  config.coffeescripts_dir = 'src/js'
  config.compile_sass? = false
end
```

## Usage

Check out the tasks with `rake -T`.

Right now I'm just using my Compass `config.rb` to configure all the sassy stuff. If you don't want the sass stuff be sure to set the following in the config:

```ruby
  config.compile_sass? = false
```

## CoffeeScripts

My biggest hurdle was getting over having all the `js`/`coffee` in one giant file. It felt like there was a good opportunity to create a convention on the file concatenation.

### Config

There are some config options for handling the CoffeeScript files. Below are all the options and their default values.
```ruby
AngularTasks::TaskLib.new do |config|
  config.verbose = true                # Verbose logging. Eventually will default to false.
  config.compile_coffeescript? = true  # If we should even compile the CoffeeScript.
  config.coffeescripts_dir = 'src/js'  # The directory of the CoffeeScript files to compile.
  config.javascripts_dir = 'src/js'    # The output directory for the JavaScript files.
  config.boot_filename = 'app'         # The coffe file without any dependencies...will probably delete
  config.components = {                # A list of the components and the directory to find their supporting files
    :directives  => "directives/**/*.coffee",
    :filters  => "filters/**/*.coffee",
    :services  => "services/**/*.coffee",
    :controllers => "controllers/**/*.coffee"
  }
end
```


### File Concatenation

Each component will let you concactenate the files in another directory into a top level file For example, the following files will be compiled:

Input CoffeScript Files:

```
src/js/controllers.coffee
      /controllers/first.coffee
      /controllers/second.coffee
```

Output CoffeeScript File:

```
src/js/controllers.js
```

The output of the `controller.js` file will look like the following:

```javascript
// Generated by CoffeeScript 1.4.0
(function() {
  var all, the, variables, from, all, files;

  // contents of the controller.coffee

  // contents of the first.coffee

  // contents of the second.coffee

}).call(this);
```

Typically, I've been configuring each module like the following:

`controllers.coffee`

```coffeescript
# Create the top level controller module that the following files will chain onto
controllers = angular.module('myapp.controllers', ['myapp.services' ])

# Create a helper function to avoid controllers.controller 'SomeController'
controller = (name, func) ->
  controllers.controller name, func
```

The files in the `controllers/` each look like the following:
```coffeescript
controller 'HomeController', ($scope) ->
  $scope.foo = 'bar'
```

The get concatenated before they get compiled so they all exist inside the same closure.

```javascript
// Generated by CoffeeScript 1.4.0
(function() {
  var controller, controllers;

  controllers = angular.module('myapp.controllers', ['myapp.services']);

  controller = function(name, func) {
    return controllers.controller(name, func);
  };

  controller('HomeController', function($scope) {
    return $scope.foo = 'bar';
  });

}).call(this);
```


## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
