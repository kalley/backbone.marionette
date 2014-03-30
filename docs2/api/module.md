# Marionette.Application.module

Marionette Modules allow you to create modular encapsulated logic.
They can be used to split apart large applications into multiple files,
and to build individual components of your app.

## Documentation Index
* [Properties]()
  * [startWithParent]()
* [Methods]()
  * [start]()
  * [stop]()
  * [initialize]()
  * [extend]()
  * [addInitializer]()
  * [addFinalizer]()
* [Events]()
  * [before:start]()
  * [start]()
  * [before:stop]()
  * [stop]()

* [Starting and Stopping Modules](#starting-and-stopping-modules)
* [Starting Modules](#starting-modules)
  * [Module Initializers](#module-initializers)
  * [Start Events](#start-events)
  * [Preventing Auto-Start Of Modules](#preventing-auto-start-of-modules)
  * [Starting Sub-Modules With Parent](#starting-sub-modules-with-parent)
* [Stopping Modules](#stopping-modules)
  * [Module Finalizers](#module-finalizers)
  * [Stop Events](#stop-events)


#### Initialize Function

Modules have an `initialize` function which is immediately called when the Module is invoked. You can think of the `initialize` function as an extension of the constructor.

The initialize function is only available through the object literal definition of a Module.

```js
MyApp.module("Foo", {
  startWithParent: false,
  initialize: function( options, moduleName, app ) {
    this.someProperty = 'someValue';
  },
  // You can still set a define function
  define: function( Foo ) {
    console.log( this.someProperty ); // Logs 'someValue'
  }
});
```

The `initialize` function is passed three arguments.
  * The object literal definition of the Module itself (which allows you to pass arbitrary values to your Module)
  * The moduleName
  * The app.

```js
MyApp.module("Foo", {
  initialize: function( options, moduleName, app ) {
    console.log( options.someVar ); // Logs 'someString'
  },
  someVar: 'someString'
});
```

The initialize function is distinct from the `define` function. The primary difference between the two is that `initialize` is on the prototype chain, whereas `define` is not. What this means is that `initialize` can be inherited.

```js
var CustomModule = Marionette.Module.extend({
  define: function() {},    // This is not inherited and will never be called
  initialize: function() {} // This, on the other hand, will be inherited
});
```

## Module Classes

Module classes can be used as an alternative to the define pattern.

The extend function of a Module is identical to the extend functions on other Backbone and Marionette classes. This allows module lifecyle events like `onStart` and `onStop` to be called directly.

```
var FooModule = Marionette.Module.extend({
  startWithParent: false,

  constructor: function(moduleName, app, options) {
  },

  initialize: function(options, app, moduleName) {
  },

  onStart: function(options) {
  },

  onStop: function(options) {
  },
});

MyApp.module("Foo", FooModule);
```

If all of the module's functionality is defined inside its class, then the class can be passed in directly. `MyApp.module("Foo", FooModule)`



### Module Initializers

Modules, like `Application` objects, can be configured to have initializers. And just like
an Application's initializers, module's initializers are run anytime that
the module is started. Further, there is no limit to the number of initializers it can have.

Initializers can be added in the module's definition function.

```js
MyApp.module("Foo", function(Foo){

  Foo.addInitializer(function(){
    // Do things once the module has started
  });

  Foo.addInitializer(function(){
    // You can have more than one initializer
  });

});
```

### Start Events

When starting a module, a "before:start" event will be triggered prior
to any of the initializers being run. A "start" event will then be
triggered after they have been run.

```js
var mod = MyApp.module("MyMod");

mod.on("before:start", function(){
  // do stuff before the module is started
});

mod.on("start", function(){
  // do stuff after the module has been started
});
```


### Preventing Auto-Start Of Modules

The default behavior of modules is that they start with the application.
If you wish to manually start a module instead, you can change this behavior
with the `startWithParent` property.

```js
var fooModule = MyApp.module("Foo", function(){

  // prevent starting with parent
  this.startWithParent = false;

  // ... module code goes here
});

// start the app without starting the module
MyApp.start();

// later, start the module
fooModule.start();
```

The same behavior can be accomplished with the object literal definition:

```js
var fooModule = MyApp.module("Foo", {
  startWithParent: false
});
```

When splitting a module across multiple files, it is recommended that you set
`startWithParent` to be false.



## Stopping Modules

A module can be stopped, or shut down, to clear memory and resources when
the module is no longer needed. Like the starting of modules, stopping is done
in a depth-first hierarchy traversal. That is, a hierarchy of modules like
`Foo.Bar.Baz` will stop `Baz` first, then `Bar`, and finally `Foo`. Also `stopListening`
will be called to unbind all events binded using `listenTo` method.

To stop a module and its children, call the `stop` method of a module.

```js
MyApp.module("Foo").stop();
```

Modules are not automatically stopped by the application. If you wish to
stop one you must call the `stop` method on it, or stop its parent module.
When you stop any parent module, all of its children will be stopped as well.

```js
MyApp.module("Foo.Bar.Baz");

MyApp.module("Foo").stop();
```

This call to `stop` causes the `Bar` and `Baz` modules to both be stopped
as they are sub-modules of `Foo`. For more information on defining
sub-modules, see the section "Defining Sub-Modules".

### Module Finalizers

Modules also have finalizers that work in an opposite manner to
initializers: they are called whenever a module is stopped via the `stop` method.
You can have as many finalizers as you'd like.

```js
MyApp.module("Foo", function(Foo){

  Foo.addFinalizer(function(){
    // Tear down, shut down and clean up the module in here
  });

  Foo.addFinalizer(function(){
    // Do more things
  });

});
```

### Stop Events

When stopping a module, a "before:stop" event will be triggered prior
to any of the finalizers being run. A "stop" event will then be triggered
after they have been run.

```js
var mod = MyApp.module("MyMod");

mod.on("before:stop", function(){
  // do stuff before the module is stopped
});

mod.on("stop", function(){
  // do stuff after the module has been stopped
});
```
