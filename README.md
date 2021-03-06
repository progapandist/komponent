# <img alt="Komponent" src="https://raw.github.com/ouvrages/komponent/master/logo.svg?sanitize=true" width="200" height="40" />

**Komponent** implements an opinionated way of organizing front-end code in Ruby on Rails, based on _components_.

Each component has its own folder, containing a Ruby module, a slim partial, a PostCSS stylesheet and a JavaScript file.

Komponent relies heavily on webpacker to manage dependencies and generate the production JS and CSS files.

This gem has been inspired by our Rails development practices at [Ouvrages](https://ouvrages-web.fr) and [Etamin Studio](https://etaminstudio.com), and the (excellent) [_Modern Front-end in Rails_](https://evilmartians.com/chronicles/evil-front-part-1) article from Evil Martians.


## Getting started

```ruby
# Gemfile

gem "komponent"
```

Run the following command to set up your project instantly:

```sh
rails generate komponent:install
```

This command will:

* check that the dependencies (currently, webpacker) are installed
* rename the `app/javascript` folder to `frontend` and modify webpacker config accordingly
* create the `frontend/components` folder where you will put your component
* create the `frontend/components/index.js` file that will list your components and `import` it in `frontend/packs/application.js`

## Usage

Generate a new component with the `component` generator:

```sh
rails generate component button
```

Then, render it in your views with the `component` helper (or its alias `c`).

```slim
/ app/views/pages/home.html.slim

= component "button"
= c "button"
```

You can pass `locals` to the helper. They are accessible within the component partial, as instance variables.

```slim
/ app/views/pages/home.html.slim

= component "button", text: "My button"
```

```slim
/ frontend/components/button/_button.html.slim

.button
  = @text
```

The component also accepts a `block`. To render the block, just use the standard `yield`.

```slim
/ app/views/pages/home.html.slim

= component "button"
  span= "My button"
```

```slim
/ frontend/components/button/_button.html.slim

.button
  = yield
```

Each component comes with a Ruby `module`. You can use it to set properties:

```ruby
# frontend/components/button/button_component.rb

module ButtonComponent
  property :href, required: true
  property :text, default: "My button"
end
```

```slim
/ frontend/components/button/_button.html.slim

a.button(href=@href)
  = @text
```

If your partial becomes a too complex and you want to remove logic from it, you may want to define custom helpers in the `ButtonComponent` module:

```ruby
# frontend/components/button/button_component.rb

module ButtonComponent
  property :href, required: true
  property :text, default: "My button"

  def external_link?
    @href.starts_with? "http"
  end
end
```

```slim
/ frontend/components/button/_button.html.slim

a.button(href=@href)
  = @text
  = " (external link)" if external_link?
```

```slim
/ app/views/pages/home.html.slim

= component "button", text: "My button", href: "http://github.com"
```

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/ouvrages/komponent.

## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).
