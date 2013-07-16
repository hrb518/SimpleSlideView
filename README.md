# SimpleSlideView

A nifty little jQuery or Zepto plugin for the simplest of sliding views.

* [Explanatory blog post]()
* [Check out a demo]()

## Dependencies

SimpleSlideView requires either [jQuery](http://jquery.com/) or [Zepto](http://zeptojs.com/) (the default build should be fine).

### Optional: Scrolling

This plugin was designed to work well with non-fixed layouts, which means it can be helpful to scroll to the top of the window or container prior to a view changing. If a `$.scrollTo` plugin is available, SimpleSlideView will attempt to use it by default. It has been tested with [jquery.scrollTo](https://github.com/flesler/jquery.scrollTo) and [ZeptoScroll](https://github.com/suprMax/ZeptoScroll/).

This functionality can be mimicked using SimpleSlideView's events if plugins aren't your bag. You can also just set the `scrollOnStart` option to `true` if you don't care about animation... no plugin required. See options for more info.

## Getting started

SimpleSlideView requires two types of elements to be present in your markup:

* A container for the views that will be sliding.
* An element for each of the views themselves. These don't have to be _immediate_ children of the container, but they should be nested somewhere inside of it.

A very simple example:

```html
<div class="container"> <!-- the container -->
  <div class="view">...</div> <!-- first view (shown by default) -->
  <div class="view">...</div> <!-- second view -->
  <div class="view">...</div> <!-- and so on... -->
</div>
```

You then call `simpleSlideView` on the containing element:

```javascript
$('.container').simpleSlideView();
```

If you'd like to use a different selector than `.view` for the views, you can specify an argument:

```javascript
$('.container').simpleSlideView('.my-view-selector');
```

[More options]() are available and can be passed as an object.

## Navigating between views

Once the plugin is active, only the active view will be visible, so we probably want some way to navigate to the other views. There are two types of navigation that can occur... a "push" (target view animates in from right) or a "pull" (target view animates in from left).

### With HTML

If you want to specify view navigation in your markup, the `data-popview` and `data-pushview` attributes are the way to go.

If you have a link with an `href` that already resolves to a view, just add one or the other attribute:

```html
<a href="#view-1" data-popview>Backward!</a>
<a href="#view-3" data-pushview>Forward!</a>
```

You can also specify an attribute value. This is useful if you want to use a more complicated selector that would be invalid as an `href`, you want to link to a different destination while the SimpleSlideView is active, or you want to use an element that isn't a link at all:

```html
<div data-pushview=".view.weird:first">To the first weird view!</div>
```

### With JavaScript

The `simpleSlideView` method returns an object with methods you can use to navigate with JavaScript.

```javascript
// Instantiates the SimpleSlideView
var slideView = $('.container').simpleSlideView();
// Pushes the view with an ID of 'my-view-selector'
slideView.pushView('#my-view-selector');
```

See methods for more info.

## Getting responsive

A sliding view isn't always appropriate when the viewport can show more content. Luckily, you can turn SimpleSlideView off and on whenever it makes sense for your project using the `on` and `off` methods.

By default, SimpleSlideView will activate itself when initialized, but you can disable this by setting the `deferOn` option to `true`. An example:

```javascript
var slideView = $('.container').simpleSlideView({
  // Do not activate unless the viewport is larger than 768pt
  deferOn: ($(window).width() > 768)
});

// Turn on/off if window size changes
$(window).on('resize', function(){
  if ($(window).width() > 768) {
    slideView.off();
  } else {
    slideView.on();
  }
});
```

## Options

You can pass an object to the `simpleSlideView` method as the first or second argument:

```javascript
// This...
$('.container').simpleSlideView('.my-view-selector', { duration: 250 });
// ...is the same as this:
$('.container').simpleSlideView({ views: '.my-view-selector', duration: 250 });
```

Here are all the options and their defaults:

```coffeescript
# The default view selector. An object will be a
# jQuery or Zepto object, a string will be used
# as a selector within the container.
views: '.view'

# The view that should be initially active. Can
# also be an object or string. If 'null', the first
# view in the container will be used.
activeView: null

# If 'true', SimpleSlideView will not activate
# until the on() method is called.
deferOn: false

# The speed of animations. Defaults to the current
# jQuery or Zepto default.
duration: $.fx.speeds._default

# The easing method to use for animations. Defaults
# to 'ease-out' for Zepto and 'swing' for jQuery.
easing: if Zepto? then 'ease-out' else 'swing'

# If true, animations will act on the 'transform'
# properties rather than 'right' or 'left'. Defaults
# to 'true' for Zepto.
useTransformProps: Zepto?

# When 'true', 3D transforms will be used. Can sometimes
# improve performance. 'true' by default if Modernizr and
# the 'csstransforms3d' test are included.
use3D: Modernizr? and Modernizr.csstransforms3d

# The CSS prefix to use for the 'transform' property.
# Defaults to the one the framework is using (if any).
cssPrefix: if $.fx.cssPrefix? then $.fx.cssPrefix else ''

# If 'true', the height of the container will be resized
# to match the height of the active view (both initially
# and as the views change. You should set to 'false' if
# your container's height is fixed.
resizeHeight: true

# Duration of the height animations when 'resizeHeight'
# is true. If 'null', the value of the duration option
# will be used.
heightDuration: null

# If 'true', the resizeHeight animation will occur after
# the rest of the view change has finished. (Having too
# many CSS animations happening at once sometimes affects
# performance.) 'true' for Zepto, 'false' otherwise.
deferHeightChange: Zepto?

# Scroll to the top of the window or container (see
# scrollToContainer) when a view change begins. If this is
# a string, the plugin will try to scroll to the position
# using a plugin with that name. If 'true', the scroll
# will snap to that position without animation.
scrollOnStart: if $.scrollTo? then 'scrollTo' else false

# If 'true', the scroll will move to the top of the
# container. If 'false', the top of the window will
# be used.
scrollToContainerTop: true

# If 'true', the height of the viewport will never
# lower. If 'null', the value will be based on whether
# or not resizeHeight and scrollOnStart are both truthy.
# This is useful for avoiding odd animation in browsers
# that resize the viewport as they scroll, such as
# iOS Safari.
maintainViewportHeight: null

# The event that the magic data-attribute events will
# be bound to. If 'false', no events will be bound.
dataAttrEvent: 'click'

# The names of the data attributes to use for the magic
# event bindings.
dataAttr:
  push: 'pushview'
  pop: 'popview'

# Class names that get added to the different elements as
# the plugin does its thing. These have no utility to the
# plugin, they are merely for styling and convenience.
classNames:
  container: 'SimpleSlideView-container'
  view: 'SimpleSlideView-view'
  activeView: 'SimpleSlideView-view-active'

# The names of the events that will trigger on the
# container when a view change begins or ends.
eventNames:
  viewChangeStart: 'viewChangeStart'
  viewChangeEnd: 'viewChangeEnd'
```

## Methods

Save the output of the plugin to a variable if you intend to use methods:

```javascript
var slideView = $('.container').simpleSlideView();
slideView.pushView('#view-2');
```

### on

```javascript
on()
```

Activates the plugin. You don't have to fire this unless you've set `deferOn` to `true` or if you've called `off()`.

### off

```javascript
off()
```

Deactivates the plugin. We've done everything in our power to remove all traces of it from the DOM, but obviously you can activate it again using `on()`.

### pushView and popView

```javascript
pushView(targetView)
popView(targetView)
```

Will push or pop to `targetView`. This will get passed to `$`, so it can be whatever kind of selector jQuery or Zepto would accept.

These are shortcut methods for...

### changeView

```javascript
changeView(targetView, action)
```

This works the same as `pushView` or `popView`, accept it needs a value for `action` of either `'push'` or `'pop'`.

## Events

Two events will trigger on the view container at the beginning and end of a view change. By default the event names are `viewChangeStart` and `viewChangeEnd`.

In addition to the usual `event` object, your callback can also receive the `targetView` and `action` properties the `changeView` function did:

```javascript
$('.container').on('viewChangeStart', function (event, targetView, action) {
  console.log(targetView); // Whatever you or a magic method passed
  console.log(action); // 'push' or 'pop'
});
```

## Known issues and limitations

### Hashes and maintaining history

There are entire libraries devoted to maintaining history with JavaScript. Frankly, the task seemed too large for this library to handle. If you have ideas for integrating history, please consider contributing to the project.

### Multi-view transitions

Some interfaces will animate _multiple_ views in a row if you pop a view far into its history. This isn't part of SimpleSlideView right now because we don't maintain history and we don't require views exist in any particular hierarchy we could infer history from.

### Animation performance

We've noticed that some pages perform better with CSS animations and some with JavaScript. Speaking broadly, pages with a _lot_ of complex or animating content tend to perform better with JS, while most other pages seem to perform better with CSS. We hope that by relying on the `$.animate` method and providing options for `useTransformProps` and `use3D` we've given you the ability to experiment with what works best for your project.

## Building from source

SimpleSlideView is written in [CoffeeScript](http://coffeescript.org/). We've included a [Grunt](http://gruntjs.com/) config file to make it easy to contribute. With [node.js](http://gruntjs.com/) installed, `cd` to the project directory and follow these steps:

1. Install the Grunt CLI (if you haven't already): `npm install -g grunt-cli`
1. Install other dependencies: `npm install`
1. Watch the source file for changes: `grunt`

As you save `simpleslideview.coffee`, the Grunt task will compile and minify automatically. You can stop this by hitting `Ctrl+C`.