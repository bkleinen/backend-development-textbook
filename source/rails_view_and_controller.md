Rails Views and Controller
==========================

The Rails View is concerened with displaying (HTML and JSON) output. The
controller is concerned with handling incoming requests,
and using the model and views to generate a result.

After reading this guide you should

* understand the role of view, controller and routing in rails
* know which routes and actions are implied by rails resources

and be able to 

* adapt the views and controllers generated by the scaffold generator
* build a form for editing a model
* use nested resources

REPO: Fork the [example app 'kanban-board'](https://github.com/backend-development/rails-example-kanban-board) and try out what you learn here.

-------------------------------------------------------

The View
--------

The View in the Model-View-Controller pattern
is responsible for generating output that will be
displayed the user in various ways.

In Ruby on Rails **Embedded Ruby** (ERB) is normally used
as the templating languages to define the views.  If you have
used templates in other languages or have used PHP embedded in HTML
than you will recognize the similarities.

Both Web Designers and Web Developers might want to edit
the files concerning the view. (This is one of the points where
using git to resolve merge conflicts comes in handy!).

### Layouts and Views

The view file (e.g. `app/views/thing/show.html.erb` will only
contain the HTML that is specific for this view.  There is
another file `app/views/layouts/application.html.erb` that contains
the surrounding stuff that stays the same for every webpage: head, main
navigation, footer.

![Layouts and Views](images/layout_view.svg)


If you find other parts of your code that you want to reuse
in severl views you can extract it into a "partial".  An example
is the `_form.html.erb` partial created by the scaffold: it is
used both by the `new.html.erb` and the `edit.html.erb` view.

![Layouts, Views and Partials](images/layout_view_partial.svg)

### Views with ERB

When it comes to templating systems there are two competing
schools of thought: on the one side there are minimal
templating systems that only offer the inclusion of variable values
and maybe iteration.  On the other hand are full programming
languages embedded in HTML.

ERB is an example of the latter: the full power of ruby is
available inside the template:

* Instance Variables of the controller are available in the view
* `<% ruby code here %>` just evaluates the code
* `<%= ruby code here %>` evaluates the code and includes the result

You can use all the usual ruby contructs for iteration, conditions, blocks.
Here's an example of a loop:

``` ruby
<ul>
<% @groups.each do |group| %>
  <li><%= link_to group.name, group %></li>
<% end %>
</ul>
```


### Links

Never write links to your own app "by hand"! Use helper methods to get the right URLs.
Use `rake routes` on the command line do find out which URLs exist:

``` shell
$ rake routes
          Prefix Verb     URI Pattern
  add_user_group PUT      /groups/:id/add_user(.:format)
  del_user_group PUT      /groups/:id/del_user(.:format)
          groups GET      /groups(.:format)
                 POST     /groups(.:format)
       new_group GET      /groups/new(.:format)
      edit_group GET      /groups/:id/edit(.:format)
           group GET      /groups/:id(.:format)
                 PATCH    /groups/:id(.:format)
                 PUT      /groups/:id(.:format)
                 DELETE   /groups/:id(.:format)
```

Use the "prefix" and `_path` or `_url` to get the path or full URL of the
action.

* `link_to "Add a User", add_user_group_path` links to the groups#add_user action
* `link_to "Show the Object", object` links to show action of the object


### Now do 'Rails for Zombies' Episode no3

![Rails for Zombies Episode 3](images/rails-for-zombies-3.jpg)

The Controller
--------------

The controller is the central part of MVC. An incoming HTTP request 
is routet to exactly one Controller Action that will respond to the Request.
The controller than uses the model(s) to load and manipulate the right data, 
and finally displays the resulting page by rendering a view.

### Restful Resources

Rails uses [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) as
a convention about which actions should be available.  For example
if you specify in `config/routes.rb`  

``` ruby
resources :zombies
```

This will generate the following mappings (visibile through `rake routes`):

``` 
HTTP
Method URI Pattern       Controller          Action
GET    /zombies          zombies_controller  def index
POST   /zombies          zombies_controller  def create
GET    /zombies/new      zombies_controller  def new
GET    /zombies/:id/edit zombies_controller  def edit
GET    /zombies/:id      zombies_controller  def show
PATCH  /zombies/:id      zombies_controller  def update
DELETE /zombies/:id      zombies_controller  def destroy
```

You have already used the scaffold generator which also
adds the necessary views.  This way we end up with full CRUD (create, read, update, delete)
capability for zombies:

![scaffold](images/rest.png)

Do adapt the views to better fit your users needs, but at the same
time try to keep the underlying routes the same!

### Now do 'Rails for Zombies' Episode no 4

![Rails for Zombies Episode 4](images/rails-for-zombies-4.jpg)

Form Helpers
-------

When you write a Rails App, you never write `form`- or `input`-Tags by hand.
You always use form helpers to contruct the HTML for you.  You gain a lot of
functionality by using the helpers, but you also need to understand how they work.


### Creating the Form Tag

Let's look at a very simple edit-form for a Resource called user:

``` ruby
<%= form_for(@user) do |f| %>
    Uid:      <%= f.text_field :uid %>  <br>
    Name:     <%= f.text_field :name %> <br>
    E-Mail:   <%= f.email_field :email %> <br>
    Homepage: <%= f.url_field :homepage %> <br>
    <%= f.submit %>
<% end %>
```

The form helber `form_for`  will create the form-tag
and set the action and method according to the REST conventions.
For example if the `@user` variable contains a user object with id 16, 
the resulting form tag will look like this:

``` html
<form class="edit_user" id="edit_user_16" action="/users/16" accept-charset="UTF-8" method="post">
  <input type="hidden" name="_method" value="patch" />
...
</form>
```

The conventions say we should use the PATCH method for updating an existing
resource, but this is not available in current html forms. Rails get's around
this by using a hidden field and some javascript to actually send the HTTP
request with the correct method.


### Creating Input Elements


``` ruby
<%= form_for(@user) do |f| %>
    Uid:      <%= f.text_field :uid %>  <br>
    Name:     <%= f.text_field :name %> <br>
    E-Mail:   <%= f.email_field :email %> <br>
    Homepage: <%= f.url_field :homepage %> <br>
    <%= f.submit %>
<% end %>
```


The `text_field`, `email_field`, `url_field` helpers create input
fields that are related to the attributes of the user-object.
The fields are set up correctly for both displaying the current value
of the attribute and for editing and overwriting it when the form is 
sent in.  For example `<%= f.text_field :name %>` might  be displayed as

``` html
<input type="text" value="Brigitte Jellinek" name="user[name]" id="user_name" />
```

### Sending the Data

When you press the submit-Data, the date from the form is sent via a HTTP request.
In the development-log file on the server you can see the request coming in and being
routed to the right action:

```
Started PATCH "/users/16" for ::1 at 2015-11-11 12:47:15 +0100
Processing by UsersController#update as HTML
  Parameters: { "user"=>{"uid"=>"4206851", "name"=>"Brigitte Jellinek", "email"=>"", ... }, "id"=>"16"}
```

Actually the Parameter came in through two separate channels: the data for the
user came through the body of the HTTP request, the `id` came in as part of the URL.
Observe the URI Pattern in the output of `rake routes`:

```
          Prefix Verb     URI Pattern                        Controller#Action
           users GET      /users(.:format)                   users#index
                 POST     /users(.:format)                   users#create
        new_user GET      /users/new(.:format)               users#new
       edit_user GET      /users/:id/edit(.:format)          users#edit
            user GET      /users/:id(.:format)               users#show
                 PATCH    /users/:id(.:format)               users#update
```

Because the pattern is `/users/:id` a request to `/users/16` will also
set the id to 16.

### Processing the Data

The parameters from the HTTP request can be used
directly in the controller via the `params` Hash:

```
# PATCH /users/1
# PATCH /users/1.json
def update
  @user = User.find(params[:id])
  ...
end
```

For mass-assignments (update, create) rails offers an easy
way to filter out only the parameters you really want to be changed / created,
and ignore all others. The following code will look for a key `user` in the
params Hash and only pick out it's sub-entries `uid`, `name`, `email` and `homepage`:

```
# PATCH /users/1
# PATCH /users/1.json
def update
  @user = User.find(params[:id])
  good_params = params.require(:user).permit(:uid, :name, :email, :homepage)
  @user.update(good_params)
  redirect_to @user, notice: 'User updated.'
end
```

We should also handle errors.  If the update does not succeed, `update` returns `false`
and the `errors` attribute is set on the user-object.  In this case we just re-display
the edit-view from before:

```
# PATCH /users/1
# PATCH /users/1.json
def update
  @user = User.find(params[:id])
  good_params = params.require(:user).permit(:uid, :name, :email, :homepage)
  if @user.update(good_params)
    redirect_to @user, notice: 'User was successfully updated.'
  else
    render :edit 
  end
end
```


### Handling and Displaying Errors

When displaying the form we always display errors that are
available throught the `errors` attribute of the user object:

``` ruby
<%= form_for(@user) do |f| %>
  <% if @user.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@user.errors.count, "error") %> prohibited this user from being saved:</h2>

      <ul>
      <% @user.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
      </ul>
    </div>
  <% end %>
  ...
<% end %>
```

<!--

Nested Resources
-------

* one model belongs to another, and is dependent
* `has_many :cards, :dependent => :destroy`
* makes sense to nest urls:
* /boards/3/cards  - all the cards in board 3

### model:

* `has_many :cards, :dependent => :destroy`

### routes file:

``` ruby
resources :boards do
  resources :cards
end
```

### Routes constructed by nested resources

``` sh
GET    /boards                          
POST   /boards                          
GET    /boards/new                      
GET    /boards/:id/edit                 
GET    /boards/:id                      
PUT    /boards/:id                      
DELETE /boards/:id                      
GET    /boards/:board_id/cards          
POST   /boards/:board_id/cards          
GET    /boards/:board_id/cards/new      
GET    /boards/:board_id/cards/:id/edit 
GET    /boards/:board_id/cards/:id      
PUT    /boards/:board_id/cards/:id      
DELETE /boards/:board_id/cards/:id      
```

### Changes

If you switch to nested resources you need to change
a lot of links:  Instead of `cards_path` you will need `boards_cards_path(board)`.

Another change is needed in the form for editing or creating a card:

``` ruby
<%= form_for  [ @board, @card ]  do |f| %>
```

-->


Further reading
----------------

* Rails Guide: [Layouts and Rendering in Rails](http://guides.rubyonrails.org/layouts_and_rendering.html)
* Rails Guide: [Action Controller Overview](http://guides.rubyonrails.org/action_controller_overview.html)
* Rails Guide: [Rails Routing from the Outside In](http://guides.rubyonrails.org/routing.html)
* [link_to](http://apidock.com/rails/v3.2.8/ActionView/Helpers/UrlHelper/link_to)
* [before_action](http://guides.rubyonrails.org/form_helpers.html#select-boxes-for-dealing-with-models)
* [resources](http://apidock.com/rails/ActionDispatch/Routing/Mapper/Resources/resources)
* Alternative to ERB: [HAML](http://haml.info/tutorial.html)  similar to SASS
