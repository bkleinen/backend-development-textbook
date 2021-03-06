Some Gems for Rails 
===========================

This guide lists a few valuable gems for
your first Rails Project.

After reading this guide you will

* understand how gems are installed
* understand how bundler handles dependencies
* be able to configure the gems you use
* be able to deliberately chose version numbers for your gems

<dd class="work-in-progress"><p>This chapter is still a work in progress. </p>
<p>You can help by reviewing the documents and posting your comments and corrections.</p></dd>

-----------------------------------------------------------------

Gems and Bundler
----------------

### gem

* rubygems is the package manager for ruby
* a gem has a name  (e.g. rake) and a version (e.g. 0.9.2.2)
* [what is a gem](http://docs.rubygems.org/read/chapter/24)
* find 100.000 gems at [rubygems.org](http://rubygems.org/)


### the problem

![bundler](images/bundler-small.png)

* you write an app
* using 100 gems
* then deploy it to a server
* where all theses gems are present in slightly different versions

### the solution

* `Gemfile` : define which gems + versions you want
* run `bundle install`
* picks out compatible versions of gems, includes dependencies, writes `Gemfile.lock`
* versions are now locked!
* deploy, run `bundle install` on the production server
* exact same versions are now installed


### defining versions

``` ruby
gem "devise"
gem "rails", "4.0.0.beta"
gem "rack",  ">=1.0"
gem "thin",  "~>1.1"
gem "nokogiri", :git => "git://github.com/tenderlove/nokogiri.git"
```

### Gemfile.lock

```
devise (2.1.0)
  bcrypt-ruby (~> 3.0)
  orm_adapter (~> 0.0.7)
  railties (~> 3.1)
  warden (~> 1.1.1)
```

### gems and rails

* configuration for a gem: `config/initializers/devise.rb`
* gem may install generators: `rails generate`
* gem may install rake tasks: `rake -T`



Installing
----------

Gems are ruby packages, they are the prefered unit of code reuse in ruby.
You can install them using the command line tool `gem`:


``` shell
gem install devise
```

In a Rails Project you should let bundler do the work: you just add the
gem to the `Gemfile`, and then run

``` shell
bundle install
```

Bundler will resolve version conflicts between the gems for you and 
install and use a set of gems that will work well with each other.


Installing gems with C code
--------

A word of warning: some gems are not written exclusively in ruby,
they may contain C code and use C libraries. From the [RubyGems Guides](http://guides.rubygems.org/gems-with-extensions/):

> Many gems use extensions to wrap libraries that are written in C with a ruby wrapper. 
> Examples include nokogiri which wraps libxml2 and libxslt, pg which is an interface 
> to the PostgreSQL database and the mysql and mysql2 gems which provide an interface to the MySQL database.

To install such a gem you first need to research which libraries are used, how they are
named on your linux distribution, and then install the libraries. The last step might look like this:

``` shell
$dev> ssh my.production.machine
$production> apt-get install libmagick++-dev
$production> gem install rmagick
$production> gem install paperclip
```

Now that you have installed the gem once by hand
you can be sure that it can also be installed by bundler.

You might not have permission to install system wide packages and gems 
on the production machine. If you can't sudo,
you need to ask the system administrator to do it for you.

Some Gems
----------

This list is based on [Dwellables statistics on the Rails Rumble 2013](http://www.dwellable.com/blog/Rails-Rumble-Gem-Teardown) and
[coodbeerstartups "Must Have Gems for Development Machine in Ruby on Rails "](http://www.codebeerstartups.com/2013/04/must-have-gems-for-development-machine-in-ruby-on-rails).

### Bootstrap for rails

*[Bootstrap SASS](https://github.com/twbs/bootstrap-sass) - don't just use bootstrap, configure it with SASS
*[bootstrap_forms](https://github.com/bootstrap-ruby/rails-bootstrap-forms) - form helper to generate form tags and input fields that work well with bootstrap

### Mixins for CSS

You don't want to waste your time writing vendor-prefixes for css3 features.
Use a mixin library instead:

![bourbon screenshot](images/gem-bourbon.png)
[http://bourbon.io/](http://bourbon.io/)

### CSS Grid 

Bootstrap is used by a lot of projects. See [Kehoe(2013)](http://railsapps.github.io/twitter-bootstrap-rails.html) for help
with choosing the right bootstrap gem.

But there is a more sophisticated alternative to bootstrap: **Neat** uses a *semantic* grid system:

![neat screenshot](images/gem-neat.png)
[http://neat.bourbon.io/](http://neat.bourbon.io/)


### File Upload

Uploading Pictures and displaying them is a common task with two good solutions:

* gem [carrierwave](https://github.com/carrierwaveuploader/carrierwave) or
* gem [paperclip](https://github.com/thoughtbot/paperclip)

Both use the library [ImageMagick](http://www.imagemagick.org/):

* gem [rmagick](https://github.com/rmagick/rmagick) image processing with image magick

You might also want to know how to use ImageMagick on the UNIX commandline: 

* command line tool [convert](http://www.imagemagick.org/script/convert.php) to convert to different formats and
* command line tool [mogrify](http://www.imagemagick.org/script/mogrify.php) to  resize, blur, crop, draw on, flip, join, ... an image and overwrite it.

### Authentication

* gem [devise](https://github.com/plataformatec/devise) for login,
* gem [omniauth](https://github.com/intridea/omniauth) to integrate authentication methods.
* gem [pundit](https://github.com/elabs/pundit) if you need roles and permissions 

### Permalinks

You don't want the database keys to be visible in your URLs?  Use Friendly IDs instead:

* gem [friendlyid](https://github.com/norman/friendly_id)

### Parsing xml and html

* gem [nokogiri](http://nokogiri.org/)

(this is also used by capybara)

### Parsing Markdown

Letting users enter HTML is a dangerous idea - it's really hard to
avoid the security problems.  An alternative is to let them enter
a simpler markup language, like [Markdown](http://en.wikipedia.org/wiki/Markdown).
You store the markdown in your database, and convert it to HTML when displayed using a gem:

* gem [redcarpet](https://github.com/vmg/redcarpet) - see [Railscast #272](http://railscasts.com/episodes/272-markdown-with-redcarpet?view=asciicast)

### Activity Stream

If you want to give users an overview of what happened recently
you can use an activity stream, similar to what you see in facebook.

![activity stream example](images/gem-activity.png)

* gem [public_activity](https://github.com/pokonski/public_activity)

### Admin Backend

Create a Backend for manipulating data with a few lines of code:

![ActiveAdmin Screenshot](images/gem-active-admin.png)

* gem [activeadmin](http://activeadmin.info/)


### Sending Mail

To send mail from Rails use [ActionMailer](http://guides.rubyonrails.org/action_mailer_basics.html).

To see the generated mails in your web browser *instead* of sending them, use the gem [letter_opener](https://github.com/ryanb/letter_opener)

### HTTP Requests

This might be handy for downloading from Webpages or APIs

* gem [curb](https://github.com/taf2/curb)  - the http library curl for ruby

### Using APIs

* gem [octokit](https://github.com/octokit/octokit.rb) - for github
* gem [twitter](https://github.com/sferik/twitter)
* gem [gravatar](https://github.com/sinisterchipmunk/gravatar)
* gem [koala](https://github.com/arsduo/koala) - for facebook
* gem [barometer](https://github.com/attack/barometer) - A multi API consuming weather forecasting superstar
* gem [gmaps4rails](https://github.com/apneadiving/Google-Maps-for-Rails)

### Testing

* gem [factory_girl](https://github.com/thoughtbot/factory_girl) for creating test data.
* gem [capybara](https://github.com/jnicklas/capybara) as the "browser" for acceptance tests, with
* gem [poltergeist](https://github.com/jonleighton/poltergeist) for testing client side javascript.


### Understanding your Code better

![better_errors screenshot](images/gem-better_errors.png)

* gem [better_errors](https://github.com/charliesome/better_errors)

* gem [meta_request](https://github.com/dejan/rails_panel/tree/master/meta_request) - supporting gem for [Rails Panel, a Google Chrome extension for Rails development](https://github.com/dejan/rails_panel)
* gem [annotate](https://github.com/ctran/annotate_models) - inserts the db schema as comments in your model.rb file
* gem [bullet](https://github.com/flyerhzm/bullet) - helps you improve your usage of activerecord queries
* gem [flay](https://github.com/seattlerb/flay) - finds structural similarities in your code, so you can refactor
* gem [rails_best_practices](https://github.com/railsbp/rails_best_practices)


Ressources
----------

* [Ruby Gems](https://rubygems.org/)
* [Bundler](http://bundler.io/)
* the [Ruby Toolbox](https://www.ruby-toolbox.com/) is organized in categories that help you find alternative solutions

