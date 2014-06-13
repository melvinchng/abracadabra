[![Gem Version](https://badge.fury.io/rb/abracadabra.svg)](http://badge.fury.io/rb/abracadabra)

# Abracadabra

The gem that swaps out text with a fully-compliant Rails form in one click.

Much of the concepts and html mark-up were taken from the awesome [x-editable](http://vitalets.github.io/x-editable/) plugin and the Rails version of this, [x-editable-rails](https://github.com/werein/x-editable-rails). However, this was written from the ground up and uses fully Rails-compliant forms without hacking into x-editable's core files or overriding them.

## Installation

Add this line to your application's Gemfile:

    gem 'abracadabra'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install abracadabra

## Usage

* Requires jQuery and jQuery-UJS (rails.js).
* Bootstrap and Font-Awesome are the default, but are not required. You can override the CSS and/or customize the icon classes (see [Configuration](#configuration)).

In your `application.css`, AFTER Bootstrap, include the css file:

```css
 *= require abracadabra
```

OR if you're using SASS/SCSS:

```sass
 @import "abracadabra-scss";
```

In your `application.js`, AFTER jQuery (required), include the javascript file:

```js
 //= require abracadabra
```

## Helpers

The bread and butter of abracadabra is its helper, `click_to_edit`. It's pretty much as readable as it gets:

```ruby
<%= click_to_edit @user, path: user_path(@user), attribute: :name %>
```

When a user clicks the link generated by this helper, a form with a text field input will replace the link. It's fully Rails compliant, and the form markup that is generated is identical to a `form_for` with `remote: true`. Here's what it looks like:

![Abracadabra Demo](http://recordit.co/CbgPTahYix.gif "Abracadabra Demo")

The first parameter of `click_to_edit` is the object to be edited, and the only other required parameters are `path` and `attribute`. `path` specifies where the form will be submitted, and `attribute` specifies the column being updated.

It accepts the following parameters:

#### REQUIRED
- `path: user_path(@user)` - Specifies where the form will be submitted.

- `attribute: :name` - Specifies what attribute your text field will be updating.

#### OPTIONAL
- `class: "my-class"` - Class(es) to be added to the abracadabra link. The class "abracadabra" is added either way. [*Default:* `"abracadabra"`]

- `id: "my-id"` - ID to be added to the abracadabra link. [*Default:* `nil`]

- `value: "blah"` - An alternate value, other than what object.attribute would return. [*Default:* `object.attribute`]

- `method: "patch"` - HTTP REST method to use. Use anything but "get". [*Default:* `"patch"`]

- `buttonless: true` - Removes submit and cancel buttons. Submission and cancellation is then done through the Enter/Tab and Escape keys, respectively. [*Default:* `false`]

- `remote: true` - Same as link_to's `remote: true`, form submits via AJAX. [*Default:* `true`]

- `type: :js` - Content type -- responds to any content type (`:js` and `:script` are interchangeable). [*Default:* `:script`] **&#42;IMPORTANT: `type` will be ignored if `remote = false` is used. HTML is the default in Rails for standard form submissions.&#42;**

- `deletable: true` OR `deletable: "Are you sure?"` - Puts a link to DELETE the object (obviously, it always uses DELETE as the HTTP verb). If a boolean is used, it is submitted upon clicking. If a string is used, a confirmation dialog will prompt them using the string before submitting. [*Default:* `false`] **&#42;IMPORTANT: On `ajax:success`, this will remove the specific abracadabra instance from the DOM entirely. ALSO, this will be remote if the main form is remote.&#42;**

- `deletable_path: user_path(@user)` - Specifies where the form will be submitted. [*Default:* `path` (uses the same path as the main form)]

- `deletable_type: :js` - Deletable content type -- responds to any content type (:js and :script can both be used to respond with Javascript). [*Default:* `:script`]

- `tab_to_next: true` OR `tab_to_next: ".my-class"` - Opens the next abracadabra instance after successful form submission (main form, not the DELETE link's submission). If a boolean is used, `.abracadabra` is the selector used to find the next instance to open. If a string is used, that will be the selector, so be sure to use standard jQuery selector syntax (i.e. `.class` and `#id`). [*Default:* `false`] **&#42;IMPORTANT: If you use a string, and it's a class, this abracadabra instance MUST have the same class as well.&#42;**

- `submit_on_blur: true` - Submit form when focus leaves the input, rather than simply closing it out. [*Default:* `false`]

#### EXAMPLES
##### *SIMPLE*
```ruby
click_to_edit @friend, path: friend_path(@friend), attribute: :name, deletable: true
```

##### *COMPLEX*
```ruby
click_to_edit @friend, 
    path: friend_path(@friend),
    attribute: :name,
    class: "my-abracadabra",
    id: "my-abracadabra-#{index}",
    value: @friend.name.titleize,
    method: :put,
    buttonless: true,
    type: :json,
    deletable: "Are you sure?",
    deletable_path: user_friend_path(@friend),
    deletable_type: :json,
    tab_to_next: "#my-abracadabra-#{index+1}",
    submit_on_blur: true
```

#### REBINDING

Simply call abracadabra's jQuery function:

```javascript
$.abracadabra();
```

## Configuration

Abracadabra allows some customization. If you would like to change what icon classes are used for the `submit`, `cancel`, and `delete` icons, you can change them globally. 

In any Javascript file that loads **BEFORE** abracadabra's Javascript file that you required above, change any/all of the following variables to suit your project's needs:

```javascript
abracadabraSubmitIcon = "fa fa-check"; // default

abracadabraCancelIcon = "fa fa-times"; // default

abracadabraDeleteIcon = "fa fa-times-circle-o"; // default
```

## Future & Contributing

1. I would love anyone to add date pickers and other alternate field types to this.

2. I would love the different Bootstrap classes to be overridable with an initializer (config/abracadabra.rb), rather than Javascript (not sure if this is even possible), so that any framework could be used. Same with the Font-Awesome button classes.

Any other ideas, feel free to contribute!

1. Fork it ( http://github.com/TrevorHinesley/abracadabra/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
