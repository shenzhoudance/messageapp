
# 构建一个可以交互的博客功能

## 一、新建一个专案

```
rails new messageapp
```
- cd messageapp
- git init
- git add .
- git commit -m "initial commit"

在git中完成版本管理；

```
rails g model Message title:string description:text
```
Running via Spring preloader in process 14276
     invoke  active_record
     create    db/migrate/20180203011508_create_messages.rb
     create    app/models/message.rb
     invoke    test_unit
     create      test/models/message_test.rb
     create      test/fixtures/messages.yml

 ```
 rake db:migrate
 ```
== 20180203011508 CreateMessages: migrating ===================================
-- create_table(:messages)
  -> 0.0015s
== 20180203011508 CreateMessages: migrated (0.0016s) ==========================

```
atom .
rails g controller Messages
```

Running via Spring preloader in process 14446
     create  app/controllers/messages_controller.rb
     invoke  erb
     create    app/views/messages
     invoke  test_unit
     create    test/controllers/messages_controller_test.rb
     invoke  helper
     create    app/helpers/messages_helper.rb
     invoke    test_unit
     invoke  assets
     invoke    coffee
     create      app/assets/javascripts/messages.coffee
     invoke    scss
     create      app/assets/stylesheets/messages.scss
```
rails s
```
=> Booting Puma
=> Rails 5.1.4 application starting in development
=> Run `rails server -h` for more startup options
Puma starting in single mode...
* Version 3.11.2 (ruby 2.3.1-p112), codename: Love Song
* Min threads: 5, max threads: 5
* Environment: development
* Listening on tcp://0.0.0.0:3000
Use Ctrl-C to stop



```
rake routes
```

     Prefix Verb   URI Pattern                  Controller#Action
   messages GET    /messages(.:format)          messages#index
            POST   /messages(.:format)          messages#create
new_message GET    /messages/new(.:format)      messages#new
edit_message GET    /messages/:id/edit(.:format) messages#edit
    message GET    /messages/:id(.:format)      messages#show
            PATCH  /messages/:id(.:format)      messages#update
            PUT    /messages/:id(.:format)      messages#update
            DELETE /messages/:id(.:format)      messages#destroy
```
rails s
```
=> Booting Puma
=> Rails 5.1.4 application starting in development
=> Run `rails server -h` for more startup options
Puma starting in single mode...
* Version 3.11.2 (ruby 2.3.1-p112), codename: Love Song
* Min threads: 5, max threads: 5
* Environment: development
* Listening on tcp://0.0.0.0:3000
Use Ctrl-C to stop


- 安装gem：
https://rubygems.org/

- （1）安装：simple_form 3.5.0
https://rubygems.org/gems/simple_form

- （2）安装：bootstrap-sass 3.3.7
https://rubygems.org/gems/bootstrap-sass

- （3）运行：bundle install

```
bundle install
rails generate simple_form:install --bootstrap
rails s
=> Booting Puma
=> Rails 5.1.4 application starting in development
=> Run `rails server -h` for more startup options
Puma starting in single mode...
* Version 3.11.2 (ruby 2.3.1-p112), codename: Love Song
* Min threads: 5, max threads: 5
* Environment: development
* Listening on tcp://0.0.0.0:3000
Use Ctrl-C to stop
```
rake routes
```
     Prefix Verb   URI Pattern                  Controller#Action
   messages GET    /messages(.:format)          messages#index
            POST   /messages(.:format)          messages#create
new_message GET    /messages/new(.:format)      messages#new
edit_message GET    /messages/:id/edit(.:format) messages#edit
    message GET    /messages/:id(.:format)      messages#show
            PATCH  /messages/:id(.:format)      messages#update
            PUT    /messages/:id(.:format)      messages#update
            DELETE /messages/:id(.:format)      messages#destroy
       root GET    /                            messages#index
```       
rails s
```
=> Booting Puma
=> Rails 5.1.4 application starting in development
=> Run `rails server -h` for more startup options
Puma starting in single mode...
* Version 3.11.2 (ruby 2.3.1-p112), codename: Love Song
* Min threads: 5, max threads: 5
* Environment: development
* Listening on tcp://0.0.0.0:3000
Use Ctrl-C to stop

```
# 二、填充代码

## （1）控制器代码：
```
class MessagesController < ApplicationController
  before_action :find_message,only: [:show,:edit,:update,:destroy]


  def index
    @message = Message.all.order("created_at DESC")
  end

   def show
  end

  def new
    @message = Message.new
  end

  def create
    @message = Message.new(message_params)
    if @message.save
      redirect_to root_path
    else
      render 'new'
    end
  end

    def edit
    end

    def update
      if @message.update(message_params)
        redirect_to messages_path
      else
        render 'edit'
      end
    end

   def destroy
     @message.destroy
     redirect_to messages_path
   end

  private
   def message_params
      params.require(:message).permit(:title,:description)
   end

   def find_message
     @message = Message.find(params[:id])
end

end
```

## （2）views 代码

- form.html.erb

```
<%= simple_form_for @message do |f| %>
<%= f.input :title, label: "message title" %>
<%= f.input :description %>
<%= f.button :submit %>
<% end %>
```

- new.html.erb

```
<h1>new message</h1>

<%= render 'form' %>
```

- edit.html.erb

```
<h1>edit message</h1>

<%= render 'form' %>
```

- show.html.erb

```
<h2><%= @message.title %></h2>
<p><%= @message.description %></p>
<!-- add a poster at timestamp -->
<%= link_to "back", messages_path(@message) %>
<%= link_to "edit", edit_message_path(@message) %>
<%= link_to "delete", message_path(@message), method: :delete, date: { confirm: "are you sure?" } %>
<%= link_to "Delete", message_path(@message), method: :delete, data: { confirm: "Are you sure?" }, class: "btn btn-default" %>
```
