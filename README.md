# Ember Flash Message

A simple plugin that allows you to display a message on the next route
transition, similiar to Rails' ``flash[:notice]``. This is useful for
displaying one time notices on later pages.

## Usage

You should include
[ember-flash-message.js](https://raw.github.com/ryanto/ember-flash-message/master/flash-message.js)
on your page. Since the plugin depends on Ember.js you will have to
include Ember first. Now any of your route's will have access to
``flashMessage('message')``, which will set the message.

### Template

Any template that you want to display the message should contain the
following. It makes the most sense to put this in your ``application``
template to have the message be available on all pages.

```handlebars
{{#flashMessage}}
  <div {{bind-attr class="message.type"}}>
    {{message.text}}
  </div>
{{/flashMessage}}
```

### Route

Any route can call ``this.flashMessage(message)`` to set the message.
The message will be displayed after the next transition. After the
router transitions for a second time the message will be destroyed.

```javascript
Ember.LoginRoute = Ember.Route.extend({
  actions: {
    login: function() {
      // login user ...
      this.flashMessage('Welcome back!', 'success');
      this.transitionTo('index');
    }
  }
});
```

### Instant Message

There may be some instances where you want to display the message right
away and not wait for the route to transition. You can use the ``now()``
function to update the message.

```javascript
Ember.ProfileRoute = Ember.Route.extend({
  actions: {
    save: function(profile) {
      var router = this;

      profile.save().then(function() {
        router.flashMessage('Your profile has been updated!').now();
      });
    }
  }
});
```   

### Dismissing Messages

While you could easily remove flash messages yourself, the main FlashMessageController contains a convinence [action](http://emberjs.com/guides/templates/actions/) named `dismissFlashMessage` that will remove the current message. To use this action, you just need to add a link _(or any other element)_ that triggers it.

```handlebars
{{#flashMessage}}
  <div {{bind-attr class=':flash-message message.type'}}>
    {{message.text}}
    <a href='#' class='dismiss' {{action 'dismissFlashMessage'}}>x</a>
  </div>
{{/flashMessage}}
```

You can also specify a value so that the flash message will be automatically dismissed after the configured time (in milliseconds).
```javascript
flashMessage('Your profile has been updated!', 5000).now();
```

If user mouses onto the flash message, the timer will be stopped, and only restarted again (at half the configure value) if the user mouses away.

### Controller

The flash message can be set from the controller by adding a ``needs``
dependency for the ``flashMessage`` controller. See the example below.


```javascript
App.PostController = Ember.ObjectController.extend({
  needs: ['flashMessage'],

  actions: {
    save: function() {
      var flashMessage = this.get('controllers.flashMessage');

      this.get('model').save()
        .then(function() {
          flashMessage.set('message', 'Blog post saved!');
        });
    }
  }
});
```

Please note that whenever you set the flash message from a control it
will be displayed instantly.

## Development

This plugin is built with rake pipeline, which requires Ruby. To get
started:

```
bundle install
bundle exec rackup
```

Edit code and visit [http://localhost:9292](http://localhost:9292) to
run tests.
