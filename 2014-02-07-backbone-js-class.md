# Backbone.js full-day class (7 Feb 2014)

[The class repo is on GitHub][backbone-ga-class], containing [a branch from the day][backbone-ga-class-branch].

[backbone-ga-class]: https://github.com/timruffles/backbone-ga-class
[backbone-ga-class-branch]: https://github.com/timruffles/backbone-ga-class/tree/hub-feb-14

Tim Ruffles has been using Backbone since version 0.4.2. He's made lots of different game clients, a
Twitter client and Skimlinks (like an internal Google Analytics). It's quite easy to hack Backbone
into anything you like.

Backbone is all about structure for your app. The boring MVC parts. Not for the DOM, AJAX, and it's
definitely not jQuery. Read the source once you understand the interface, it's quite simple and well
documented. It uses a few of jQuery's functions. It's not a framework. It's a library. You're
responsible for your app. It's a smaller tool with smaller aims. It's flexible.

Tim combines models and collections together when giving this talk because they can be thought of in a similar way.

## The big idea

It runs the core loop of MVC. We have some state in our model, the model is rendered by the view.
The view renders controls to allow the user to modify the model. The view is updated to display
what's changed. It's the division between data and presentation.

Things should be driven primarily by the model changing and triggering events.

## A comparison

Tim shows [a snippet of jQuery][jquery-slide]. It's got too many responsibilities. It's
unstructured. It's hard to test.

[jquery-slide]: https://github.com/timruffles/backbone-ga-class/blob/35121ae96f75e9be407d93577269262a8daa5b20/slides/index.html#L100

## Responsibilities

- The user need lives in the `Model`
- User interaction lives in the `View`
- Persistence is handled by `Backbone.sync`
- (Your app feels more like a website with `Router`, less important for GDS)

## Key terms

- Domain: the problem this app is solving for its users, all the rules and logic
- Model: where the domain ends up in our code; there shouldn't be anything the user can't understand in the model (the term Model is overloaded with Models and Collections)
- State: what was happened so far in the model/domain

Domain and state live in the Model. The View presents. Sync allows us to persist state.

## Model

Subclass Backbone.Model. Give it two objects: instance methods, and static properties.

Normally not a good idea to have deep inheritance, makes it fragile.

- `url`: by default used by Sync to figure out where to persist.
- `validate`: validation!

The key is to use setters and getters that are defined by Backbone.js.

The Model fires events, and the View is listening to those. It fires a `change` event for
every change, and a `change:foo` event for every property `foo`.

You can get the previous value in the callback using `model.previous`.

PROTIP: In the Chrome console and Node you can use C-style symbols (eg %s, %d) when printing!

Anything that's for human consumption is a View concern.

## Single point of truth

If you have logic around validation, do it in one place.

## Model

- Has an `id`
- Model has an attributes object: read from it, but **never** write to it directly (use Backbone methods so that you're not bypassing validation)
- Trigger our own custom events

On a Model, why not wrap up your own events inside Backbone `change` events? eg when you're
making a login system, listen for the `change:userId` event and then trigger your own, better named,
`loggedIn` event that you can use to make your view more clear.

## Collection

The Model represents an individual thing. We'll frequently need to deal with collections of things.

A collection is an ordered map, using the `id` property of the Model. By default it preserves
insertion order, but can use a comparator to keep itself in order.

The first item in a collection will be `collection.at(0)`.

Defining a Collection is exactly like defining a Model. You need to tell the Collection what kind
of Model it's a collection of, otherwise it'll just be full of JavaScript objects.

Just like JavaScript, it's super lazy about typing.

You can do anything you'd want to do to a JavaScript array, and more.
And you can listen for events on all that stuff too.

A Model gets a `collection` property when **first** added to a collection (it's not overwritten
if the model is then added to a second collection). Collections delegate all model events.

Define a comparator method to keep a collection in a specific order.

A Model has a client-side id which is the `cid` property. The get method of a collection will use
both `cid` and `id` and you probably can't tell which, so make sure they don't conflict. The `cid`
is always prefixed with a 'c', so probably start your own IDs with something else.

`idAttribute` can be used to specify a different a attribute instead of `id`.

## View

The events hash! Woah. This looks cool.

    events: {
      "$event $selector(s)": "methodName",
      "click #hello": "sayHello"
    }

Just like the DOM is hierarchical, your views should be hierarchical. You can give it an `el`,
or it will create its own. You need to insert that view into the DOM.

On the extend, you give it a recipe for an el: `id`, `className` and `tagName`.

Every single view has a `this.$el`. You can select inside that element using `this.$(selector)`.
You can avoid dealing with the entire DOM by making sure you use `this.$` rather than `$`.

When you instantiate a view, you can pass in a model, collection and el.

Calling your rendering methods `render` is a convention. Backbone doesn't really care how you
render your things.

You can create smaller render methods to render part of the view. But it's probably better to
make use of hierarchical views and just re-render the tiny subview for performance reasons.

The View should never assume anything on behalf of the Model. All of the listening should have
happened a long time before the View starts triggering events to change the Model.

Worry about things being garbage collected: if you keep changing the view, the model may still
have a pointer to all the old views, so they won't be garbage collected.

Keep libraries like D3.js inside Backbone views.

Mustache/Underscore templates are nice and simple. Handlebars more complex. Backbone doesn't care what you use.

Defining your own `transform` method to create a nicely-formatted JSON representation of the model
and then just using render for passing it to the template is quite nice.

## Backbone.sync

Like everything else in Backbone, it's up to you. It's not limited to REST. You can override
`Backbone.Model.sync`.

Rather than using the parse to do things like converting snake-case to camel-case, write
a custom sync method.

Don't put your persistence all over your models.

Views should be things that are created and destroyed really quickly.

## Event-driven architecture

Use events! They're a very loose coupling, so they're not tied to implementation and can
be unit-tested very easily.
