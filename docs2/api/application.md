# Marionette.Application

The `Backbone.Marionette.Application` object is the hub of your composite
application. It organizes, initializes and coordinates the various pieces of your
app. It also provides a starting point for you to call into, from your HTML
script block or from your JavaScript files directly if you prefer to go that
route.

The `Application` is meant to be instantiated directly, although you can extend
it to add your own functionality.

```js
MyApp = new Backbone.Marionette.Application();
```

## Docs
* [Methods]()
  * [addInitializer]()
  * [start]()
  * [module]()
  * [addRegions]()
  * [getRegion]()
  * [removeRegion]()
  * [vent]()
  * [commands]()
  * [execute]()
  * [reqres]
  * [request]
* [Events]()
  * [initialize:before]()
  * [initialize:after]()
  * [start]()
  * [stop]()
