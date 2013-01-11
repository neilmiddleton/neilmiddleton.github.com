---
layout: post
title: Internationalisation with Rails 3
wip: true
---
First of all though, what is Internationalisation (or I18n)?  Well, firstly it's part of two larger topics, that and Localisation (L11n).  So what's the difference?  Well to quote [wikipedia](http://en.wikipedia.org/wiki/Internationalization_and_localization):

### Localisation:

> A localized system has been adapted or converted for use in a particular locale (other than the one it was originally developed for), including the language of the user interface (UI), input, and display, and features such as time/date display and currency.

### Internationalisation:

> An internationalized system is equipped for use in a range of "locales" (or by users of multiple languages), by allowing the co-existence of several languages and character sets for input, display, and UI.

From these two definitions it's easy to see that we have a variety of topics that need worrying about other than the language of the words on the page.  These include:

* How dates are formatted
* Which direction the text is written in (whilst English is left to right, there are several right to left, and even top to bottom languages)
* How numbers are formatted, including currency and so on

…and the list goes on.

## Translations

So, how as a Rails developer do we tackle this?  Well, firstly we're treated by the fact that Rails 3 has a very capable and mature I18n API which is pleasent to use.

For instance, in simple terms we can introduce other languages into a page, but setting a locale specific version of a particular string:

_/config/locales/en.yml_
{% highlight ruby %}
en:
  hello: "Hello world"
{% endhighlight %}

_/config/locales/es.yml_
{% highlight ruby %}
es:
  hello: "Hola, mundo"
{% endhighlight %}

Now, depending on the value of `I18n.locale`, Rails will allow us to pull the appropriate string with the simple translate helper:

{% highlight ruby %}
I18n.locale = :en
I18n.t(:hello)
=> "Hello world"

I18n.locale = :es
I18n.t(:hello)
=> "Hola, mundo"
{% endhighlight %}

Awesome right?

## Dates and numbers

So, what about dates, and prices etc?  Well, luckily that's pretty simple too, as I18n is baked right down into Rails.  For instance, all active record errors, date displays and the like are checked for translations prior to render.  For instance, let's consider dates.  The way that we show dates over in the UK is different to, say, the US.  Where we like to do `23rd March 2012`, the US likes to do `March 23, 2012`…

So, by default, Rails will use the US version, as that's it's spiritual home, which means we need to provide details to Rails of how to render dates over here in the UK:

_/config/locales/en_GB.yml_
{% highlight ruby %}
en-GB:
  date:
    formats:
      default: ! '%d-%m-%Y'
      long: ! '%d %B, %Y'
      short: ! '%d %b'
{% endhighlight %}

Now, that we have this config, we'll now get the correct dates in the UK.  (Note the en_GB locale, you can have more than one country which uses a language ;) ).

So, we can now localise all of our dates and numbers, but that's a fair amount of effort to do - there's hundreds…  Well, luckily that's already been largely done for us via the [Rails-I18n](https://github.com/svenfuchs/rails-i18n) gem.  This gem provides pre-baked locale files for all of the popular locales, and loads more besides.  They contain translations and formats for dates, numbers, times, activerecord errors and more.  Very useful stuff.  Simply dropping this into your application will automatically provide you with a massive amount of localisation out of the box.

## Data

So, we've now got our pages localised and we can flip between them happily serving all sorts of nationalities, but unfortunately our English data is still showing through.  We've got a database full of products which are all in English being shown to a French audience - not so good to them.

Well, the solution I've found works well here is the [Globalize3](https://github.com/svenfuchs/globalize3) gem.  This gem allows you to add translations for a given ActiveRecord model in a nice simple matter.  What's more you can treat them as nested attributes for simple editing and updating:

_/app/controllers/products_controller.rb_
{% highlight ruby %}
def edit
	@product = Product.find_by_id(params[:id])
	Locale.all.each do |locale|
	  @product.translations.build locale => locale.code
	end
end
{% endhighlight %}

_/app/models/product.rb_
{% highlight ruby %}
has_many :translations
accepts_nested_attributes_for :translations
{% endhighlight %}

_/app/views/products/edit.erb_
{% highlight ruby %}
<%= form_for @product do |f| %>
  <%= f.text_field :name %>
  <%= f.fields_for :translations do |t| %>
	  <% if t.object.locale != :en %>
			<%= f.text_field :name %>
	  <% end -%>
  <% end -%>
<% end -%>
{% endhighlight %}

Now, there's a few things going on here.  Globalize3 is taking the translations for a model as optional, this is because it will fall back to the default locale when a translation is not available (hence the exclusion of `:en` in this particular example).  Secondly, notice that as these are nested attributes you can stick them in the same form as the main data itself making it nice and simple to manage.

When showing data Globalize3 will respect the value of `I18n.locale` and render the appropriate translation for a record if available.  This means that if you have translations, your front end will show them with no intervention from yourself, which is always a bonus.

So - now we have a full set of I18n complete.  We can localise the dates, numbers, errors and so on that emanate from Rails, whilst also being able to provide translated data on a needs basis, and all without too much pain and suffering.

For more detailed information on the Rails I18n API see the [Rails Guide](http://guides.rubyonrails.org/i18n.html).  For more information on Globalize3, see the [Github Page](https://github.com/svenfuchs/globalize3)
