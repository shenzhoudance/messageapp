
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

# 三、添加用户体系
- 安装gem：
https://rubygems.org/

- gem 'devise', '~> 4.4', '>= 4.4.1'
https://rubygems.org/gems/devise


```
 rails generate devise:install
```
Running via Spring preloader in process 1901
     create  config/initializers/devise.rb
     create  config/locales/devise.en.yml
===============================================================================

Some setup you must do manually if you haven't yet:

 1. Ensure you have defined default url options in your environments files. Here
    is an example of default_url_options appropriate for a development environment
    in config/environments/development.rb:

      config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }

    In production, :host should be set to the actual host of your application.

 2. Ensure you have defined root_url to *something* in your config/routes.rb.
    For example:

      root to: "home#index"

 3. Ensure you have flash messages in app/views/layouts/application.html.erb.
    For example:

      <p class="notice"><%= notice %></p>
      <p class="alert"><%= alert %></p>

 4. You can copy Devise views (for customization) to your app by running:

      rails g devise:views

===============================================================================
```
rails g devise:views
```
Running via Spring preloader in process 1955
     invoke  Devise::Generators::SharedViewsGenerator
     create    app/views/devise/shared
     create    app/views/devise/shared/links.html.erb
     invoke  simple_form_for
     create    app/views/devise/confirmations
     create    app/views/devise/confirmations/new.html.erb
     create    app/views/devise/passwords
     create    app/views/devise/passwords/edit.html.erb
     create    app/views/devise/passwords/new.html.erb
     create    app/views/devise/registrations
     create    app/views/devise/registrations/edit.html.erb
     create    app/views/devise/registrations/new.html.erb
     create    app/views/devise/sessions
     create    app/views/devise/sessions/new.html.erb
     create    app/views/devise/unlocks
     create    app/views/devise/unlocks/new.html.erb
     invoke  erb
     create    app/views/devise/mailer
     create    app/views/devise/mailer/confirmation_instructions.html.erb
     create    app/views/devise/mailer/email_changed.html.erb
     create    app/views/devise/mailer/password_change.html.erb
     create    app/views/devise/mailer/reset_password_instructions.html.erb
     create    app/views/devise/mailer/unlock_instructions.html.erb

```
rails g devise User
```

Running via Spring preloader in process 1988
     invoke  active_record
     create    db/migrate/20180203063544_devise_create_users.rb
     create    app/models/user.rb
     invoke    test_unit
     create      test/models/user_test.rb
     create      test/fixtures/users.yml
     insert    app/models/user.rb
      route  devise_for :users

```
rake db:migrate
```

== 20180203063544 DeviseCreateUsers: migrating ================================
-- create_table(:users)
  -> 0.0037s
-- add_index(:users, :email, {:unique=>true})
  -> 0.0008s
-- add_index(:users, :reset_password_token, {:unique=>true})
  -> 0.0008s
== 20180203063544 DeviseCreateUsers: migrated (0.0057s) =======================

- 安装导航栏：
https://getbootstrap.com/docs/3.3/components/#navbar

## 在app/views/layouts/application.html.erb文件里面添加用户导航代码

```
<!DOCTYPE html>
<html>
  <head>
    <title>Messageapp</title>
    <%= csrf_meta_tags %>

    <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>

  <body>


     <div class="navbar navbar-default">
       <div class="container">
         <div class="navbar-header">
           <%= link_to "message board", root_path, class: "navbar-brand" %>
         </div>
         <ul class="nav navbar-nav">
           <li><%= link_to "sign up", new_user_registration_path %></li>
        <% if user_signed_in? %>
        <li><%= link_to "sign out",destroy_user_session_path, method: :delete %></li>
         <% else %>
         <li><%= link_to "log in", new_user_session_path %></li>
          <% end %>
         </ul>
         <% if user_signed_in? %>
         <p><%= link_to "new_message", new_message_path, class: "navbar-right navbar-text navbar-link" %></p>

         <% end %>
       </div>
     </div>


    <p class="notice"><%= notice %></p>
    <p class="alert"><%= alert %></p>

    <%= yield %>
  </body>
</html>
```

# 四、构建用户对应关系
```
git chekcout -b xw-020302
rails generate migration add_user_id_to_messages user_id:integer
```
Running via Spring preloader in process 6332
     invoke  active_record
     create    db/migrate/20180203084514_add_user_id_to_messages.rb

```
 rake db:migrate
 ```
== 20180203084514 AddUserIdToMessages: migrating ==============================
-- add_column(:messages, :user_id, :integer)
  -> 0.0017s
== 20180203084514 AddUserIdToMessages: migrated (0.0018s) =====================
```
atom .
rails c
```
Running via Spring preloader in process 6439
Loading development environment (Rails 5.1.4)
```
2.3.1 :001 > Message.connection
```
=> #<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fa4be2a3770 @transaction_manager=#<ActiveRecord::ConnectionAdapters::TransactionManager:0x007fa4bad220b0 @stack=[], @connection=#<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fa4be2a3770 ...>>, @query_cache={}, @query_cache_enabled=false, @connection=#<SQLite3::Database:0x007fa4be2a3a90 @tracefunc=nil, @authorizer=nil, @encoding=#<Encoding:UTF-8>, @busy_handler=nil, @collations={}, @functions={}, @results_as_hash=true, @type_translation=nil, @readonly=false>, @owner=#<Thread:0x007fa4ba883428 run>, @instrumenter=#<ActiveSupport::Notifications::Instrumenter:0x007fa4be398bf8 @id="0aadc5a44019b1e52824", @notifier=#<ActiveSupport::Notifications::Fanout:0x007fa4bb22f648 @subscribers=[#<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4bacca220 @pattern="sql.active_record", @delegate=#<ActiveRecord::LogSubscriber:0x007fa4bacca798 @queue_key="ActiveRecord::LogSubscriber-70172742669260", @patterns=["sql.active_record"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4be06a5a8 @pattern="logger.action_view", @delegate=#<ActionView::LogSubscriber:0x007fa4be06ac88 @root=nil, @queue_key="ActionView::LogSubscriber-70172769736260", @patterns=["logger.action_view", "render_partial.action_view", "render_template.action_view", "render_collection.action_view"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4be06a2d8 @pattern="render_partial.action_view", @delegate=#<ActionView::LogSubscriber:0x007fa4be06ac88 @root=nil, @queue_key="ActionView::LogSubscriber-70172769736260", @patterns=["logger.action_view", "render_partial.action_view", "render_template.action_view", "render_collection.action_view"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4be069f90 @pattern="render_template.action_view", @delegate=#<ActionView::LogSubscriber:0x007fa4be06ac88 @root=nil, @queue_key="ActionView::LogSubscriber-70172769736260", @patterns=["logger.action_view", "render_partial.action_view", "render_template.action_view", "render_collection.action_view"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4be069c20 @pattern="render_collection.action_view", @delegate=#<ActionView::LogSubscriber:0x007fa4be06ac88 @root=nil, @queue_key="ActionView::LogSubscriber-70172769736260", @patterns=["logger.action_view", "render_partial.action_view", "render_template.action_view", "render_collection.action_view"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4bb4fe608 @pattern="sql.active_record", @delegate=#<ActiveRecord::ExplainSubscriber:0x007fa4bb4fe680>, @can_publish=false>], @listeners_for=#<Concurrent::Map:0x007fa4bb22f5d0 entries=2 default_proc=nil>, @mutex=#<Thread::Mutex:0x007fa4bb22f440>>>, @logger=#<ActiveSupport::Logger:0x007fa4be49b028 @progname=nil, @level=0, @default_formatter=#<Logger::Formatter:0x007fa4be49af88 @datetime_format=nil>, @formatter=#<ActiveSupport::Logger::SimpleFormatter:0x007fa4badb1468 @datetime_format=nil>, @logdev=#<Logger::LogDevice:0x007fa4be49af10 @shift_size=nil, @shift_age=nil, @filename=nil, @dev=#<File:/Users/xiaowei/messageapp/log/development.log>, @mon_owner=nil, @mon_count=0, @mon_mutex=#<Thread::Mutex:0x007fa4be49aec0>>, @local_levels=#<Concurrent::Map:0x007fa4be49ada8 entries=0 default_proc=nil>>, @config={:adapter=>"sqlite3", :pool=>5, :timeout=>5000, :database=>"/Users/xiaowei/messageapp/db/development.sqlite3"}, @pool=#<ActiveRecord::ConnectionAdapters::ConnectionPool:0x007fa4bb97d5f8 @mon_owner=nil, @mon_count=0, @mon_mutex=#<Thread::Mutex:0x007fa4bb97d530>, @query_cache_enabled=#<Concurrent::Map:0x007fa4bb97d508 entries=0 default_proc=#<Proc:0x007fa4bb97d4b8@/Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/activerecord-5.1.4/lib/active_record/connection_adapters/abstract/query_cache.rb:27>>, @spec=#<ActiveRecord::ConnectionAdapters::ConnectionSpecification:0x007fa4bb97d738 @name="primary", @config={:adapter=>"sqlite3", :pool=>5, :timeout=>5000, :database=>"/Users/xiaowei/messageapp/db/development.sqlite3"}, @adapter_method="sqlite3_connection">, @checkout_timeout=5, @reaper=#<ActiveRecord::ConnectionAdapters::ConnectionPool::Reaper:0x007fa4bb97d418 @pool=#<ActiveRecord::ConnectionAdapters::ConnectionPool:0x007fa4bb97d5f8 ...>, @frequency=nil>, @size=5, @thread_cached_conns=#<Concurrent::Map:0x007fa4bb97d260 entries=1 default_proc=nil>, @connections=[#<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fa4be2a3770 ...>], @automatic_reconnect=true, @now_connecting=0, @threads_blocking_new_connections=0, @available=#<ActiveRecord::ConnectionAdapters::ConnectionPool::ConnectionLeasingQueue:0x007fa4bb97d170 @lock=#<ActiveRecord::ConnectionAdapters::ConnectionPool:0x007fa4bb97d5f8 ...>, @cond=#<MonitorMixin::ConditionVariable:0x007fa4bb97d0f8 @monitor=#<ActiveRecord::ConnectionAdapters::ConnectionPool:0x007fa4bb97d5f8 ...>, @cond=#<Thread::ConditionVariable:0x007fa4bb97d0d0>>, @num_waiting=0, @queue=[]>, @lock_thread=false>, @schema_cache=#<ActiveRecord::ConnectionAdapters::SchemaCache:0x007fa4bad21f98 @connection=#<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fa4be2a3770 ...>, @columns={}, @columns_hash={}, @primary_keys={}, @data_sources={}>, @quoted_table_names={}, @quoted_column_names={}, @visitor=#<Arel::Visitors::SQLite:0x007fa4bad21db8 @dispatch={}, @connection=#<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fa4be2a3770 ...>>, @lock=#<Monitor:0x007fa4bad21d18 @mon_owner=nil, @mon_count=0, @mon_mutex=#<Thread::Mutex:0x007fa4bad21ca0>>, @prepared_statements=true, @active=nil, @statements=#<ActiveRecord::ConnectionAdapters::SQLite3Adapter::StatementPool:0x007fa4bad21bd8 @cache={}, @statement_limit=1000>>
2.3.1 :002 > Message
=> Message(id: integer, title: string, description: text, created_at: datetime, updated_at: datetime, user_id: integer)
```
2.3.1 :003 > @message = Message.last
```
 Message Load (0.3ms)  SELECT  "messages".* FROM "messages" ORDER BY "messages"."id" DESC LIMIT ?  [["LIMIT", 1]]
=> #<Message id: 7, title: "rerew", description: "rwerwer", created_at: "2018-02-03 08:25:20", updated_at: "2018-02-03 08:25:20", user_id: nil>
```
2.3.1 :004 > exit
```

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

Started GET "/" for 127.0.0.1 at 2018-02-03 16:51:02 +0800
  (0.1ms)  SELECT "schema_migrations"."version" FROM "schema_migrations" ORDER BY "schema_migrations"."version" ASC
Processing by MessagesController#index as HTML
 Rendering messages/index.html.erb within layouts/application
 Message Load (0.2ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
 Rendered messages/index.html.erb within layouts/application (10.5ms)
 User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 430ms (Views: 411.6ms | ActiveRecord: 2.0ms)


Started GET "/users/sign_up" for 127.0.0.1 at 2018-02-03 16:51:08 +0800
Processing by Devise::RegistrationsController#new as HTML
 Rendering devise/registrations/new.html.erb within layouts/application
 Rendered devise/shared/links.html.erb (1.6ms)
 Rendered devise/registrations/new.html.erb within layouts/application (97.7ms)
Completed 200 OK in 192ms (Views: 179.4ms | ActiveRecord: 0.0ms)


Started POST "/users" for 127.0.0.1 at 2018-02-03 16:51:17 +0800
Processing by Devise::RegistrationsController#create as HTML
 Parameters: {"utf8"=>"✓", "authenticity_token"=>"N1is53YlhxccCsm/A0uBK7653e/7O7TbFpsGzC4J+OtAHyy//sKTlz6laWejMmoLU3GmROfgcilbsPB+rIVq8w==", "user"=>{"email"=>"494410617@qq.com", "password"=>"[FILTERED]", "password_confirmation"=>"[FILTERED]"}, "commit"=>"Sign up"}
  (0.1ms)  begin transaction
 User Exists (0.2ms)  SELECT  1 AS one FROM "users" WHERE "users"."email" = ? LIMIT ?  [["email", "494410617@qq.com"], ["LIMIT", 1]]
  (0.1ms)  rollback transaction
 Rendering devise/registrations/new.html.erb within layouts/application
 Rendered devise/shared/links.html.erb (1.5ms)
 Rendered devise/registrations/new.html.erb within layouts/application (12.7ms)
Completed 200 OK in 268ms (Views: 74.5ms | ActiveRecord: 0.3ms)


Started GET "/users/sign_in" for 127.0.0.1 at 2018-02-03 16:51:23 +0800
Processing by Devise::SessionsController#new as HTML
 Rendering devise/sessions/new.html.erb within layouts/application
 Rendered devise/shared/links.html.erb (1.1ms)
 Rendered devise/sessions/new.html.erb within layouts/application (18.7ms)
Completed 200 OK in 87ms (Views: 84.6ms | ActiveRecord: 0.0ms)


Started POST "/users/sign_in" for 127.0.0.1 at 2018-02-03 16:51:30 +0800
Processing by Devise::SessionsController#create as HTML
 Parameters: {"utf8"=>"✓", "authenticity_token"=>"GG4rL6hMNa9OwF/X35umgklwIpW6hVwmg+wrswP54MDBarlslr2EwFSVuAguVFPjdcbJWW418sJuiweHFnBN8A==", "user"=>{"email"=>"494410617@qq.com", "password"=>"[FILTERED]", "remember_me"=>"1"}, "commit"=>"登录"}
 User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."email" = ? ORDER BY "users"."id" ASC LIMIT ?  [["email", "494410617@qq.com"], ["LIMIT", 1]]
  (0.1ms)  begin transaction
 SQL (0.5ms)  UPDATE "users" SET "remember_created_at" = ?, "updated_at" = ? WHERE "users"."id" = ?  [["remember_created_at", "2018-02-03 08:51:31.049613"], ["updated_at", "2018-02-03 08:51:31.050701"], ["id", 1]]
  (1.0ms)  commit transaction
  (0.1ms)  begin transaction
 SQL (0.6ms)  UPDATE "users" SET "sign_in_count" = ?, "current_sign_in_at" = ?, "last_sign_in_at" = ?, "updated_at" = ? WHERE "users"."id" = ?  [["sign_in_count", 3], ["current_sign_in_at", "2018-02-03 08:51:31.056463"], ["last_sign_in_at", "2018-02-03 08:25:16.563364"], ["updated_at", "2018-02-03 08:51:31.057359"], ["id", 1]]
  (1.1ms)  commit transaction
Redirected to http://localhost:3000/
Completed 302 Found in 180ms (ActiveRecord: 3.5ms)

Started GET "/" for 127.0.0.1 at 2018-02-03 16:51:31 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.4ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (3.6ms)
  User Load (0.4ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 1], ["LIMIT", 1]]
Completed 200 OK in 66ms (Views: 60.0ms | ActiveRecord: 0.8ms)


Started GET "/messages/new" for 127.0.0.1 at 2018-02-03 16:51:33 +0800
Processing by MessagesController#new as HTML
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 1], ["LIMIT", 1]]
  Rendering messages/new.html.erb within layouts/application
  Rendered messages/form.html.erb (7.6ms)
  Rendered messages/new.html.erb within layouts/application (11.7ms)
Completed 200 OK in 104ms (Views: 70.6ms | ActiveRecord: 0.3ms)


Started POST "/messages" for 127.0.0.1 at 2018-02-03 16:51:37 +0800
Processing by MessagesController#create as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"h+LZ5R7qdsI52K5ceBR4x81EkAb/KgVvDWqKGRQRmYGgT0NM3AWO/A2b+5re4n8cCzNqd2dvcTTmLzUvIg9eIA==", "message"=>{"title"=>"dd", "description"=>"fdfdf"}, "commit"=>"Create Message"}
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 1], ["LIMIT", 1]]
   (0.1ms)  begin transaction
  SQL (0.4ms)  INSERT INTO "messages" ("title", "description", "created_at", "updated_at", "user_id") VALUES (?, ?, ?, ?, ?)  [["title", "dd"], ["description", "fdfdf"], ["created_at", "2018-02-03 08:51:37.117241"], ["updated_at", "2018-02-03 08:51:37.117241"], ["user_id", 1]]
   (1.1ms)  commit transaction
Redirected to http://localhost:3000/
Completed 302 Found in 9ms (ActiveRecord: 1.9ms)


Started GET "/" for 127.0.0.1 at 2018-02-03 16:51:37 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.7ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (3.8ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 1], ["LIMIT", 1]]
Completed 200 OK in 67ms (Views: 63.9ms | ActiveRecord: 0.9ms)


^C- Gracefully stopping, waiting for requests to finish
=== puma shutdown: 2018-02-03 16:52:23 +0800 ===
- Goodbye!
Exiting
```
rails c
```
Running via Spring preloader in process 6574
Loading development environment (Rails 5.1.4)
```
2.3.1 :001 > Message.connection
```
 => #<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fa4bb454a68 @transaction_manager=#<ActiveRecord::ConnectionAdapters::TransactionManager:0x007fa4bab813f0 @stack=[], @connection=#<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fa4bb454a68 ...>>, @query_cache={}, @query_cache_enabled=false, @connection=#<SQLite3::Database:0x007fa4bb4551c0 @tracefunc=nil, @authorizer=nil, @encoding=#<Encoding:UTF-8>, @busy_handler=nil, @collations={}, @functions={}, @results_as_hash=true, @type_translation=nil, @readonly=false>, @owner=#<Thread:0x007fa4ba883428 run>, @instrumenter=#<ActiveSupport::Notifications::Instrumenter:0x007fa4be398bf8 @id="0aadc5a44019b1e52824", @notifier=#<ActiveSupport::Notifications::Fanout:0x007fa4bb22f648 @subscribers=[#<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4bacca220 @pattern="sql.active_record", @delegate=#<ActiveRecord::LogSubscriber:0x007fa4bacca798 @queue_key="ActiveRecord::LogSubscriber-70172742669260", @patterns=["sql.active_record"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4be06a5a8 @pattern="logger.action_view", @delegate=#<ActionView::LogSubscriber:0x007fa4be06ac88 @root=nil, @queue_key="ActionView::LogSubscriber-70172769736260", @patterns=["logger.action_view", "render_partial.action_view", "render_template.action_view", "render_collection.action_view"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4be06a2d8 @pattern="render_partial.action_view", @delegate=#<ActionView::LogSubscriber:0x007fa4be06ac88 @root=nil, @queue_key="ActionView::LogSubscriber-70172769736260", @patterns=["logger.action_view", "render_partial.action_view", "render_template.action_view", "render_collection.action_view"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4be069f90 @pattern="render_template.action_view", @delegate=#<ActionView::LogSubscriber:0x007fa4be06ac88 @root=nil, @queue_key="ActionView::LogSubscriber-70172769736260", @patterns=["logger.action_view", "render_partial.action_view", "render_template.action_view", "render_collection.action_view"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4be069c20 @pattern="render_collection.action_view", @delegate=#<ActionView::LogSubscriber:0x007fa4be06ac88 @root=nil, @queue_key="ActionView::LogSubscriber-70172769736260", @patterns=["logger.action_view", "render_partial.action_view", "render_template.action_view", "render_collection.action_view"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4bb4fe608 @pattern="sql.active_record", @delegate=#<ActiveRecord::ExplainSubscriber:0x007fa4bb4fe680>, @can_publish=false>], @listeners_for=#<Concurrent::Map:0x007fa4bb22f5d0 entries=2 default_proc=nil>, @mutex=#<Thread::Mutex:0x007fa4bb22f440>>>, @logger=#<ActiveSupport::Logger:0x007fa4be49b028 @progname=nil, @level=0, @default_formatter=#<Logger::Formatter:0x007fa4be49af88 @datetime_format=nil>, @formatter=#<ActiveSupport::Logger::SimpleFormatter:0x007fa4badb1468 @datetime_format=nil>, @logdev=#<Logger::LogDevice:0x007fa4be49af10 @shift_size=nil, @shift_age=nil, @filename=nil, @dev=#<File:/Users/xiaowei/messageapp/log/development.log>, @mon_owner=nil, @mon_count=0, @mon_mutex=#<Thread::Mutex:0x007fa4be49aec0>>, @local_levels=#<Concurrent::Map:0x007fa4be49ada8 entries=0 default_proc=nil>>, @config={:adapter=>"sqlite3", :pool=>5, :timeout=>5000, :database=>"/Users/xiaowei/messageapp/db/development.sqlite3"}, @pool=#<ActiveRecord::ConnectionAdapters::ConnectionPool:0x007fa4bb7bb440 @mon_owner=nil, @mon_count=0, @mon_mutex=#<Thread::Mutex:0x007fa4bb7bb350>, @query_cache_enabled=#<Concurrent::Map:0x007fa4bb7bb328 entries=0 default_proc=#<Proc:0x007fa4bb7bb2d8@/Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/activerecord-5.1.4/lib/active_record/connection_adapters/abstract/query_cache.rb:27>>, @spec=#<ActiveRecord::ConnectionAdapters::ConnectionSpecification:0x007fa4bb7bb5a8 @name="primary", @config={:adapter=>"sqlite3", :pool=>5, :timeout=>5000, :database=>"/Users/xiaowei/messageapp/db/development.sqlite3"}, @adapter_method="sqlite3_connection">, @checkout_timeout=5, @reaper=#<ActiveRecord::ConnectionAdapters::ConnectionPool::Reaper:0x007fa4bb7bb260 @pool=#<ActiveRecord::ConnectionAdapters::ConnectionPool:0x007fa4bb7bb440 ...>, @frequency=nil>, @size=5, @thread_cached_conns=#<Concurrent::Map:0x007fa4bb7bb210 entries=1 default_proc=nil>, @connections=[#<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fa4bb454a68 ...>], @automatic_reconnect=true, @now_connecting=0, @threads_blocking_new_connections=0, @available=#<ActiveRecord::ConnectionAdapters::ConnectionPool::ConnectionLeasingQueue:0x007fa4bb7bb170 @lock=#<ActiveRecord::ConnectionAdapters::ConnectionPool:0x007fa4bb7bb440 ...>, @cond=#<MonitorMixin::ConditionVariable:0x007fa4bb7bb0f8 @monitor=#<ActiveRecord::ConnectionAdapters::ConnectionPool:0x007fa4bb7bb440 ...>, @cond=#<Thread::ConditionVariable:0x007fa4bb7bb0d0>>, @num_waiting=0, @queue=[]>, @lock_thread=false>, @schema_cache=#<ActiveRecord::ConnectionAdapters::SchemaCache:0x007fa4bab811c0 @connection=#<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fa4bb454a68 ...>, @columns={}, @columns_hash={}, @primary_keys={}, @data_sources={}>, @quoted_table_names={}, @quoted_column_names={}, @visitor=#<Arel::Visitors::SQLite:0x007fa4bab80ec8 @dispatch={}, @connection=#<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fa4bb454a68 ...>>, @lock=#<Monitor:0x007fa4bab80d60 @mon_owner=nil, @mon_count=0, @mon_mutex=#<Thread::Mutex:0x007fa4bab80d10>>, @prepared_statements=true, @active=nil, @statements=#<ActiveRecord::ConnectionAdapters::SQLite3Adapter::StatementPool:0x007fa4bab80ba8 @cache={}, @statement_limit=1000>>
```
2.3.1 :002 > @message = Message.last
```
  Message Load (0.4ms)  SELECT  "messages".* FROM "messages" ORDER BY "messages"."id" DESC LIMIT ?  [["LIMIT", 1]]
 => #<Message id: 8, title: "dd", description: "fdfdf", created_at: "2018-02-03 08:51:37", updated_at: "2018-02-03 08:51:37", user_id: 1>
 ```
2.3.1 :003 > @message = Message.first
```
  Message Load (0.3ms)  SELECT  "messages".* FROM "messages" ORDER BY "messages"."id" ASC LIMIT ?  [["LIMIT", 1]]
 => #<Message id: 1, title: "rew", description: "rwer", created_at: "2018-02-03 08:21:40", updated_at: "2018-02-03 08:21:40", user_id: nil>

 ```
2.3.1 :004 > @message.user_id = 1
 => 1
2.3.1 :005 > @message.save
```
   (0.2ms)  begin transaction
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  SQL (0.6ms)  UPDATE "messages" SET "updated_at" = ?, "user_id" = ? WHERE "messages"."id" = ?  [["updated_at", "2018-02-03 08:54:49.223632"], ["user_id", 1], ["id", 1]]
   (1.1ms)  commit transaction
 => true
 ```
2.3.1 :006 > @message = Message.find(3)
```
  Message Load (0.5ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 3], ["LIMIT", 1]]
 => #<Message id: 3, title: "werwerw", description: "rwrwerwr", created_at: "2018-02-03 08:21:48", updated_at: "2018-02-03 08:21:48", user_id: nil>
 ```
2.3.1 :007 > @message.user_id = 1
 => 1
2.3.1 :008 > @message.save
```
   (0.1ms)  begin transaction
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  SQL (0.5ms)  UPDATE "messages" SET "updated_at" = ?, "user_id" = ? WHERE "messages"."id" = ?  [["updated_at", "2018-02-03 08:56:41.568721"], ["user_id", 1], ["id", 3]]
   (9.0ms)  commit transaction
 => true
 ```
2.3.1 :009 > @message = Message.find(4)
```
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 4], ["LIMIT", 1]]
 => #<Message id: 4, title: "rr", description: "rwrwe", created_at: "2018-02-03 08:22:33", updated_at: "2018-02-03 08:22:33", user_id: nil>
 ```
2.3.1 :010 > @message.user_id = 1
 => 1
2.3.1 :011 > @message.save
```
   (0.1ms)  begin transaction
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  SQL (0.4ms)  UPDATE "messages" SET "updated_at" = ?, "user_id" = ? WHERE "messages"."id" = ?  [["updated_at", "2018-02-03 08:57:34.826474"], ["user_id", 1], ["id", 4]]
   (9.0ms)  commit transaction
 => true
 ```
2.3.1 :012 > exit
```

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

Started GET "/users/sign_out" for 127.0.0.1 at 2018-02-03 16:58:07 +0800
   (0.3ms)  SELECT "schema_migrations"."version" FROM "schema_migrations" ORDER BY "schema_migrations"."version" ASC

ActionController::RoutingError (No route matches [GET] "/users/sign_out"):

actionpack (5.1.4) lib/action_dispatch/middleware/debug_exceptions.rb:63:in ```
call'
web-console (3.5.1) lib/web_console/middleware.rb:135:in `call_app'
web-console (3.5.1) lib/web_console/middleware.rb:28:in `block in call'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `catch'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/show_exceptions.rb:31:in `call'
railties (5.1.4) lib/rails/rack/logger.rb:36:in `call_app'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `block in call'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `block in tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:26:in `tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `tagged'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `call'
sprockets-rails (3.2.1) lib/sprockets/rails/quiet_assets.rb:13:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/remote_ip.rb:79:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/request_id.rb:25:in `call'
rack (2.0.4) lib/rack/method_override.rb:22:in `call'
rack (2.0.4) lib/rack/runtime.rb:22:in `call'
activesupport (5.1.4) lib/active_support/cache/strategy/local_cache_middleware.rb:27:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/static.rb:125:in `call'
rack (2.0.4) lib/rack/sendfile.rb:111:in `call'
railties (5.1.4) lib/rails/engine.rb:522:in `call'
puma (3.11.2) lib/puma/configuration.rb:225:in `call'
puma (3.11.2) lib/puma/server.rb:624:in `handle_request'
puma (3.11.2) lib/puma/server.rb:438:in `process_client'
puma (3.11.2) lib/puma/server.rb:302:in `block in run'
puma (3.11.2) lib/puma/thread_pool.rb:120:in `block in spawn_thread'
Started GET "/" for 127.0.0.1 at 2018-02-03 16:58:13 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.2ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (14.3ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 1], ["LIMIT", 1]]
Completed 200 OK in 336ms (Views: 325.3ms | ActiveRecord: 1.9ms)


Started DELETE "/users/sign_out" for 127.0.0.1 at 2018-02-03 16:58:15 +0800
Processing by Devise::SessionsController#destroy as HTML
  Parameters: {"authenticity_token"=>"xCGyZuFXC5nMPCoT4jJOxBcshsha1X4vk+1MlN9S5m0BuX6XB/JIPn8g0o9pdZrht7iaGwPyLxB7m9QY7qs5lg=="}
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 1], ["LIMIT", 1]]
   (0.1ms)  begin transaction
  SQL (0.5ms)  UPDATE "users" SET "remember_created_at" = ?, "updated_at" = ? WHERE "users"."id" = ?  [["remember_created_at", nil], ["updated_at", "2018-02-03 08:58:16.009853"], ["id", 1]]
   (9.4ms)  commit transaction
Redirected to http://localhost:3000/
Completed 302 Found in 52ms (ActiveRecord: 10.1ms)


Started GET "/" for 127.0.0.1 at 2018-02-03 16:58:16 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.4ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (3.0ms)
Completed 200 OK in 74ms (Views: 71.9ms | ActiveRecord: 0.4ms)


Started GET "/users/sign_up" for 127.0.0.1 at 2018-02-03 16:58:17 +0800
Processing by Devise::RegistrationsController#new as HTML
  Rendering devise/registrations/new.html.erb within layouts/application
  Rendered devise/shared/_links.html.erb (6.8ms)
  Rendered devise/registrations/new.html.erb within layouts/application (64.4ms)
Completed 200 OK in 143ms (Views: 141.7ms | ActiveRecord: 0.0ms)


Started POST "/users" for 127.0.0.1 at 2018-02-03 16:58:27 +0800
Processing by Devise::RegistrationsController#create as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"zQfCc+PuEmIUcQDLfp6UYb4Bg3ZhfAgNkIpR/W7gezgtUNvshOZuiaOwvOldsXgftvULMQLBIDstPpvCfR9ykQ==", "user"=>{"email"=>"admin@test.com", "password"=>"[FILTERED]", "password_confirmation"=>"[FILTERED]"}, "commit"=>"Sign up"}
   (0.1ms)  begin transaction
  User Exists (0.2ms)  SELECT  1 AS one FROM "users" WHERE "users"."email" = ? LIMIT ?  [["email", "admin@test.com"], ["LIMIT", 1]]
  SQL (0.8ms)  INSERT INTO "users" ("email", "encrypted_password", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["email", "admin@test.com"], ["encrypted_password", "$2a$11$lXYR6/ACKP2W02RKBDNI.uy5Az3MgCm4PxPbiPus7txx5KWjXu2Fi"], ["created_at", "2018-02-03 08:58:27.260609"], ["updated_at", "2018-02-03 08:58:27.260609"]]
   (9.4ms)  commit transaction
   (0.1ms)  begin transaction
  SQL (1.1ms)  UPDATE "users" SET "sign_in_count" = ?, "current_sign_in_at" = ?, "last_sign_in_at" = ?, "current_sign_in_ip" = ?, "last_sign_in_ip" = ?, "updated_at" = ? WHERE "users"."id" = ?  [["sign_in_count", 1], ["current_sign_in_at", "2018-02-03 08:58:27.274487"], ["last_sign_in_at", "2018-02-03 08:58:27.274487"], ["current_sign_in_ip", "127.0.0.1"], ["last_sign_in_ip", "127.0.0.1"], ["updated_at", "2018-02-03 08:58:27.276061"], ["id", 2]]
   (1.1ms)  commit transaction
Redirected to http://localhost:3000/
Completed 302 Found in 203ms (ActiveRecord: 12.7ms)


Started GET "/" for 127.0.0.1 at 2018-02-03 16:58:27 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.2ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (2.2ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 80ms (Views: 76.6ms | ActiveRecord: 0.5ms)


Started GET "/messages/new" for 127.0.0.1 at 2018-02-03 16:58:49 +0800
Processing by MessagesController#new as HTML
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
  Rendering messages/new.html.erb within layouts/application
  Rendered messages/_form.html.erb (7.7ms)
  Rendered messages/new.html.erb within layouts/application (10.1ms)
Completed 200 OK in 78ms (Views: 54.9ms | ActiveRecord: 0.2ms)


Started POST "/messages" for 127.0.0.1 at 2018-02-03 16:58:51 +0800
Processing by MessagesController#create as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"sNwmKeY6JggmVayF41NyHpF2sgnHcgRy9Eh3YChSXU1rcAVgkH4FcPCcge7jJcy4/l3E2FsvwZkJ/YhOIVtJIg==", "message"=>{"title"=>"dfsfsd", "description"=>"fsfsdf"}, "commit"=>"Create Message"}
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.3ms)  begin transaction
  SQL (0.4ms)  INSERT INTO "messages" ("title", "description", "created_at", "updated_at", "user_id") VALUES (?, ?, ?, ?, ?)  [["title", "dfsfsd"], ["description", "fsfsdf"], ["created_at", "2018-02-03 08:58:51.552766"], ["updated_at", "2018-02-03 08:58:51.552766"], ["user_id", 2]]
   (0.9ms)  commit transaction
Redirected to http://localhost:3000/
Completed 302 Found in 9ms (ActiveRecord: 1.9ms)


Started GET "/" for 127.0.0.1 at 2018-02-03 16:58:51 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (1.6ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (4.9ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 69ms (Views: 64.9ms | ActiveRecord: 1.8ms)


^C- Gracefully stopping, waiting for requests to finish
=== puma shutdown: 2018-02-03 16:59:13 +0800 ===
- Goodbye!
Exiting

```

```
rails c
```
Running via Spring preloader in process 6728
Loading development environment (Rails 5.1.4)
```
2.3.1 :001 > Message.connection
```
 => #<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fa4bad61918 @transaction_manager=#<ActiveRecord::ConnectionAdapters::TransactionManager:0x007fa4be45be78 @stack=[], @connection=#<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fa4bad61918 ...>>, @query_cache={}, @query_cache_enabled=false, @connection=#<SQLite3::Database:0x007fa4bad61b48 @tracefunc=nil, @authorizer=nil, @encoding=#<Encoding:UTF-8>, @busy_handler=nil, @collations={}, @functions={}, @results_as_hash=true, @type_translation=nil, @readonly=false>, @owner=#<Thread:0x007fa4ba883428 run>, @instrumenter=#<ActiveSupport::Notifications::Instrumenter:0x007fa4be398bf8 @id="0aadc5a44019b1e52824", @notifier=#<ActiveSupport::Notifications::Fanout:0x007fa4bb22f648 @subscribers=[#<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4bacca220 @pattern="sql.active_record", @delegate=#<ActiveRecord::LogSubscriber:0x007fa4bacca798 @queue_key="ActiveRecord::LogSubscriber-70172742669260", @patterns=["sql.active_record"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4be06a5a8 @pattern="logger.action_view", @delegate=#<ActionView::LogSubscriber:0x007fa4be06ac88 @root=nil, @queue_key="ActionView::LogSubscriber-70172769736260", @patterns=["logger.action_view", "render_partial.action_view", "render_template.action_view", "render_collection.action_view"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4be06a2d8 @pattern="render_partial.action_view", @delegate=#<ActionView::LogSubscriber:0x007fa4be06ac88 @root=nil, @queue_key="ActionView::LogSubscriber-70172769736260", @patterns=["logger.action_view", "render_partial.action_view", "render_template.action_view", "render_collection.action_view"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4be069f90 @pattern="render_template.action_view", @delegate=#<ActionView::LogSubscriber:0x007fa4be06ac88 @root=nil, @queue_key="ActionView::LogSubscriber-70172769736260", @patterns=["logger.action_view", "render_partial.action_view", "render_template.action_view", "render_collection.action_view"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4be069c20 @pattern="render_collection.action_view", @delegate=#<ActionView::LogSubscriber:0x007fa4be06ac88 @root=nil, @queue_key="ActionView::LogSubscriber-70172769736260", @patterns=["logger.action_view", "render_partial.action_view", "render_template.action_view", "render_collection.action_view"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fa4bb4fe608 @pattern="sql.active_record", @delegate=#<ActiveRecord::ExplainSubscriber:0x007fa4bb4fe680>, @can_publish=false>], @listeners_for=#<Concurrent::Map:0x007fa4bb22f5d0 entries=2 default_proc=nil>, @mutex=#<Thread::Mutex:0x007fa4bb22f440>>>, @logger=#<ActiveSupport::Logger:0x007fa4be49b028 @progname=nil, @level=0, @default_formatter=#<Logger::Formatter:0x007fa4be49af88 @datetime_format=nil>, @formatter=#<ActiveSupport::Logger::SimpleFormatter:0x007fa4badb1468 @datetime_format=nil>, @logdev=#<Logger::LogDevice:0x007fa4be49af10 @shift_size=nil, @shift_age=nil, @filename=nil, @dev=#<File:/Users/xiaowei/messageapp/log/development.log>, @mon_owner=nil, @mon_count=0, @mon_mutex=#<Thread::Mutex:0x007fa4be49aec0>>, @local_levels=#<Concurrent::Map:0x007fa4be49ada8 entries=0 default_proc=nil>>, @config={:adapter=>"sqlite3", :pool=>5, :timeout=>5000, :database=>"/Users/xiaowei/messageapp/db/development.sqlite3"}, @pool=#<ActiveRecord::ConnectionAdapters::ConnectionPool:0x007fa4be6e9820 @mon_owner=nil, @mon_count=0, @mon_mutex=#<Thread::Mutex:0x007fa4be6e9550>, @query_cache_enabled=#<Concurrent::Map:0x007fa4be6e9528 entries=0 default_proc=#<Proc:0x007fa4be6e94d8@/Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/activerecord-5.1.4/lib/active_record/connection_adapters/abstract/query_cache.rb:27>>, @spec=#<ActiveRecord::ConnectionAdapters::ConnectionSpecification:0x007fa4be6e9b68 @name="primary", @config={:adapter=>"sqlite3", :pool=>5, :timeout=>5000, :database=>"/Users/xiaowei/messageapp/db/development.sqlite3"}, @adapter_method="sqlite3_connection">, @checkout_timeout=5, @reaper=#<ActiveRecord::ConnectionAdapters::ConnectionPool::Reaper:0x007fa4be6e9410 @pool=#<ActiveRecord::ConnectionAdapters::ConnectionPool:0x007fa4be6e9820 ...>, @frequency=nil>, @size=5, @thread_cached_conns=#<Concurrent::Map:0x007fa4be6e9370 entries=1 default_proc=nil>, @connections=[#<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fa4bad61918 ...>], @automatic_reconnect=true, @now_connecting=0, @threads_blocking_new_connections=0, @available=#<ActiveRecord::ConnectionAdapters::ConnectionPool::ConnectionLeasingQueue:0x007fa4be6e9280 @lock=#<ActiveRecord::ConnectionAdapters::ConnectionPool:0x007fa4be6e9820 ...>, @cond=#<MonitorMixin::ConditionVariable:0x007fa4be6e9258 @monitor=#<ActiveRecord::ConnectionAdapters::ConnectionPool:0x007fa4be6e9820 ...>, @cond=#<Thread::ConditionVariable:0x007fa4be6e91e0>>, @num_waiting=0, @queue=[]>, @lock_thread=false>, @schema_cache=#<ActiveRecord::ConnectionAdapters::SchemaCache:0x007fa4be45bd38 @connection=#<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fa4bad61918 ...>, @columns={}, @columns_hash={}, @primary_keys={}, @data_sources={}>, @quoted_table_names={}, @quoted_column_names={}, @visitor=#<Arel::Visitors::SQLite:0x007fa4be45bb80 @dispatch={}, @connection=#<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fa4bad61918 ...>>, @lock=#<Monitor:0x007fa4be45ba68 @mon_owner=nil, @mon_count=0, @mon_mutex=#<Thread::Mutex:0x007fa4be45b9c8>>, @prepared_statements=true, @active=nil, @statements=#<ActiveRecord::ConnectionAdapters::SQLite3Adapter::StatementPool:0x007fa4be45b888 @cache={}, @statement_limit=1000>>

```
2.3.1 :002 > @message = Message.last
```
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" ORDER BY "messages"."id" DESC LIMIT ?  [["LIMIT", 1]]
 => #<Message id: 9, title: "dfsfsd", description: "fsfsdf", created_at: "2018-02-03 08:58:51", updated_at: "2018-02-03 08:58:51", user_id: 2>
```
2.3.1 :003 > exit
```
git add .
git commit -m "devise step by step"
[xw-020302 eafc3a3] devise step by step
 5 files changed, 11 insertions(+), 3 deletions(-)
 create mode 100644 db/migrate/20180203084514_add_user_id_to_messages.rb

#一对一完成用户关系对应
---

Last login: Sat Feb  3 17:48:29 on console
 xiaowei@xiaoweideMacBook-Pro ⮀ ~ ⮀ ⭠ master± ⮀ cd messageapp
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ xw-020302 ⮀ git checkout master
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ master ⮀ git pull
remote: Counting objects: 1, done.
remote: Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (1/1), done.
From https://github.com/shenzhoudance/messageapp
   05d6306..8544e6d  master     -> origin/master
Updating 05d6306..8544e6d
Fast-forward
 README.md                                          | 375 +++++++++++++++++++++
 app/controllers/messages_controller.rb             |   4 +-
 app/models/message.rb                              |   1 +
 app/models/user.rb                                 |   1 +
 .../20180203084514_add_user_id_to_messages.rb      |   5 +
 db/schema.rb                                       |   3 +-
 6 files changed, 386 insertions(+), 3 deletions(-)
 create mode 100644 db/migrate/20180203084514_add_user_id_to_messages.rb
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ master ⮀ git checkout -b xw-020303
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ xw-020303 ⮀ rails generate model Comment:text message:references user:refenrences
Running via Spring preloader in process 1572
      invoke  active_record
/Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/railties-5.1.4/lib/rails/generators/base.rb:258:in `const_defined?': wrong constant name Comment:text (NameError)
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/railties-5.1.4/lib/rails/generators/base.rb:258:in `block in class_collisions'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/railties-5.1.4/lib/rails/generators/base.rb:249:in `each'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/railties-5.1.4/lib/rails/generators/base.rb:249:in `class_collisions'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/railties-5.1.4/lib/rails/generators/named_base.rb:236:in `block in check_class_collision'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/command.rb:27:in `run'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/invocation.rb:126:in `invoke_command'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/invocation.rb:133:in `block in invoke_all'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/invocation.rb:133:in `each'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/invocation.rb:133:in `map'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/invocation.rb:133:in `invoke_all'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/group.rb:232:in `dispatch'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/invocation.rb:115:in `invoke'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/group.rb:277:in `block in _invoke_for_class_method'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/shell.rb:68:in `with_padding'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/group.rb:266:in `_invoke_for_class_method'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/group.rb:133:in `_invoke_from_option_orm'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/command.rb:27:in `run'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/invocation.rb:126:in `invoke_command'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/invocation.rb:133:in `block in invoke_all'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/invocation.rb:133:in `each'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/invocation.rb:133:in `map'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/invocation.rb:133:in `invoke_all'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/group.rb:232:in `dispatch'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/base.rb:466:in `start'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/railties-5.1.4/lib/rails/generators.rb:269:in `invoke'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/railties-5.1.4/lib/rails/commands/generate/generate_command.rb:24:in `perform'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/command.rb:27:in `run'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/invocation.rb:126:in `invoke_command'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor.rb:387:in `dispatch'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/railties-5.1.4/lib/rails/command/base.rb:63:in `perform'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/railties-5.1.4/lib/rails/command.rb:44:in `invoke'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/railties-5.1.4/lib/rails/commands.rb:16:in `<top (required)>'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/activesupport-5.1.4/lib/active_support/dependencies.rb:292:in `require'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/activesupport-5.1.4/lib/active_support/dependencies.rb:292:in `block in require'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/activesupport-5.1.4/lib/active_support/dependencies.rb:258:in `load_dependency'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/activesupport-5.1.4/lib/active_support/dependencies.rb:292:in `require'
	from /Users/xiaowei/messageapp/bin/rails:9:in `<top (required)>'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/activesupport-5.1.4/lib/active_support/dependencies.rb:286:in `load'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/activesupport-5.1.4/lib/active_support/dependencies.rb:286:in `block in load'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/activesupport-5.1.4/lib/active_support/dependencies.rb:258:in `load_dependency'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/activesupport-5.1.4/lib/active_support/dependencies.rb:286:in `load'
	from /Users/xiaowei/.rvm/rubies/ruby-2.3.1/lib/ruby/site_ruby/2.3.0/rubygems/core_ext/kernel_require.rb:55:in `require'
	from /Users/xiaowei/.rvm/rubies/ruby-2.3.1/lib/ruby/site_ruby/2.3.0/rubygems/core_ext/kernel_require.rb:55:in `require'
	from -e:1:in `<main>'
 ✘ xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ xw-020303 ⮀ rails generate model Commen content:text message:references user:refenrences
Running via Spring preloader in process 1745
[WARNING] The model name 'Commen' was recognized as a plural, using the singular 'Comman' instead. Override with --force-plural or setup custom inflection rules for this noun before running the generator.
      invoke  active_record
      create    db/migrate/20180203095701_create_commen.rb
      create    app/models/comman.rb
      invoke    test_unit
      create      test/models/comman_test.rb
      create      test/fixtures/commen.yml
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ xw-020303± ⮀ atom .
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ xw-020303± ⮀ git add .
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ xw-020303± ⮀ git commit -m "add commen"
[xw-020303 0566c2b] add commen
 4 files changed, 33 insertions(+)
 create mode 100644 app/models/comman.rb
 create mode 100644 db/migrate/20180203095701_create_commen.rb
 create mode 100644 test/fixtures/commen.yml
 create mode 100644 test/models/comman_test.rb
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ xw-020303 ⮀ git checkout master
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ master ⮀ atom .
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ master ⮀ atom .
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ master ⮀ git checkout -b xw-020304
Switched to a new branch 'xw-020304'
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ xw-020304 ⮀ rails generate model Comment content:text message:references user:references
Running via Spring preloader in process 2222
      invoke  active_record
      create    db/migrate/20180203100358_create_comments.rb
      create    app/models/comment.rb
      invoke    test_unit
      create      test/models/comment_test.rb
      create      test/fixtures/comments.yml
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ xw-020304± ⮀ rake db:migrate
== 20180203100358 CreateComments: migrating ===================================
-- create_table(:comments)
   -> 0.0055s
== 20180203100358 CreateComments: migrated (0.0056s) ==========================

 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ xw-020304± ⮀ rails c
Running via Spring preloader in process 2312
Loading development environment (Rails 5.1.4)
2.3.1 :001 > Comment.connection
 => #<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fdec5c09490 @transaction_manager=#<ActiveRecord::ConnectionAdapters::TransactionManager:0x007fdec5be16c0 @stack=[], @connection=#<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fdec5c09490 ...>>, @query_cache={}, @query_cache_enabled=false, @connection=#<SQLite3::Database:0x007fdec5c09828 @tracefunc=nil, @authorizer=nil, @encoding=#<Encoding:UTF-8>, @busy_handler=nil, @collations={}, @functions={}, @results_as_hash=true, @type_translation=nil, @readonly=false>, @owner=#<Thread:0x007fdec2883418 run>, @instrumenter=#<ActiveSupport::Notifications::Instrumenter:0x007fdec5b08e88 @id="401f7b11a16681fdf126", @notifier=#<ActiveSupport::Notifications::Fanout:0x007fdec32328b0 @subscribers=[#<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fdec59e8d50 @pattern="sql.active_record", @delegate=#<ActiveRecord::LogSubscriber:0x007fdec59e9070 @queue_key="ActiveRecord::LogSubscriber-70297387485240", @patterns=["sql.active_record"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fdec5993a30 @pattern="logger.action_view", @delegate=#<ActionView::LogSubscriber:0x007fdec5993e18 @root=nil, @queue_key="ActionView::LogSubscriber-70297387310860", @patterns=["logger.action_view", "render_partial.action_view", "render_template.action_view", "render_collection.action_view"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fdec5993828 @pattern="render_partial.action_view", @delegate=#<ActionView::LogSubscriber:0x007fdec5993e18 @root=nil, @queue_key="ActionView::LogSubscriber-70297387310860", @patterns=["logger.action_view", "render_partial.action_view", "render_template.action_view", "render_collection.action_view"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fdec5993648 @pattern="render_template.action_view", @delegate=#<ActionView::LogSubscriber:0x007fdec5993e18 @root=nil, @queue_key="ActionView::LogSubscriber-70297387310860", @patterns=["logger.action_view", "render_partial.action_view", "render_template.action_view", "render_collection.action_view"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fdec5993440 @pattern="render_collection.action_view", @delegate=#<ActionView::LogSubscriber:0x007fdec5993e18 @root=nil, @queue_key="ActionView::LogSubscriber-70297387310860", @patterns=["logger.action_view", "render_partial.action_view", "render_template.action_view", "render_collection.action_view"]>, @can_publish=false>, #<ActiveSupport::Notifications::Fanout::Subscribers::Evented:0x007fdec2d7cf00 @pattern="sql.active_record", @delegate=#<ActiveRecord::ExplainSubscriber:0x007fdec2d7cfa0>, @can_publish=false>], @listeners_for=#<Concurrent::Map:0x007fdec3232860 entries=2 default_proc=nil>, @_mutex=#<Thread::Mutex:0x007fdec3232748>>>, @logger=#<ActiveSupport::Logger:0x007fdec3630098 @progname=nil, @level=0, @default_formatter=#<Logger::Formatter:0x007fdec3630020 @datetime_format=nil>, @formatter=#<ActiveSupport::Logger::SimpleFormatter:0x007fdec36d2118 @datetime_format=nil>, @logdev=#<Logger::LogDevice:0x007fdec3630a70 @shift_size=nil, @shift_age=nil, @filename=nil, @dev=#<File:/Users/xiaowei/messageapp/log/development.log>, @mon_owner=nil, @mon_count=0, @mon_mutex=#<Thread::Mutex:0x007fdec3630ea8>>, @local_levels=#<Concurrent::Map:0x007fdec3631ee8 entries=0 default_proc=nil>>, @config={:adapter=>"sqlite3", :pool=>5, :timeout=>5000, :database=>"/Users/xiaowei/messageapp/db/development.sqlite3"}, @pool=#<ActiveRecord::ConnectionAdapters::ConnectionPool:0x007fdec5dc8a38 @mon_owner=nil, @mon_count=0, @mon_mutex=#<Thread::Mutex:0x007fdec5dc8948>, @query_cache_enabled=#<Concurrent::Map:0x007fdec5dc8920 entries=0 default_proc=#<Proc:0x007fdec5dc8880@/Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/activerecord-5.1.4/lib/active_record/connection_adapters/abstract/query_cache.rb:27>>, @spec=#<ActiveRecord::ConnectionAdapters::ConnectionSpecification:0x007fdec5dc8bf0 @name="primary", @config={:adapter=>"sqlite3", :pool=>5, :timeout=>5000, :database=>"/Users/xiaowei/messageapp/db/development.sqlite3"}, @adapter_method="sqlite3_connection">, @checkout_timeout=5, @reaper=#<ActiveRecord::ConnectionAdapters::ConnectionPool::Reaper:0x007fdec5dc8808 @pool=#<ActiveRecord::ConnectionAdapters::ConnectionPool:0x007fdec5dc8a38 ...>, @frequency=nil>, @size=5, @thread_cached_conns=#<Concurrent::Map:0x007fdec5dc87b8 entries=1 default_proc=nil>, @connections=[#<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fdec5c09490 ...>], @automatic_reconnect=true, @now_connecting=0, @threads_blocking_new_connections=0, @available=#<ActiveRecord::ConnectionAdapters::ConnectionPool::ConnectionLeasingQueue:0x007fdec5dc86a0 @lock=#<ActiveRecord::ConnectionAdapters::ConnectionPool:0x007fdec5dc8a38 ...>, @cond=#<MonitorMixin::ConditionVariable:0x007fdec5dc8600 @monitor=#<ActiveRecord::ConnectionAdapters::ConnectionPool:0x007fdec5dc8a38 ...>, @cond=#<Thread::ConditionVariable:0x007fdec5dc85b0>>, @num_waiting=0, @queue=[]>, @lock_thread=false>, @schema_cache=#<ActiveRecord::ConnectionAdapters::SchemaCache:0x007fdec5be1440 @connection=#<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fdec5c09490 ...>, @columns={}, @columns_hash={}, @primary_keys={}, @data_sources={}>, @quoted_table_names={}, @quoted_column_names={}, @visitor=#<Arel::Visitors::SQLite:0x007fdec5be11e8 @dispatch={}, @connection=#<ActiveRecord::ConnectionAdapters::SQLite3Adapter:0x007fdec5c09490 ...>>, @lock=#<Monitor:0x007fdec5be1120 @mon_owner=nil, @mon_count=0, @mon_mutex=#<Thread::Mutex:0x007fdec5be10a8>>, @prepared_statements=true, @active=nil, @statements=#<ActiveRecord::ConnectionAdapters::SQLite3Adapter::StatementPool:0x007fdec5be0fb8 @cache={}, @statement_limit=1000>>
2.3.1 :002 > Comment
 => Comment(id: integer, content: text, message_id: integer, user_id: integer, created_at: datetime, updated_at: datetime)
2.3.1 :003 > exit
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ xw-020304± ⮀ rails g controller Comments
Running via Spring preloader in process 2353
      create  app/controllers/comments_controller.rb
      invoke  erb
      create    app/views/comments
      invoke  test_unit
      create    test/controllers/comments_controller_test.rb
      invoke  helper
      create    app/helpers/comments_helper.rb
      invoke    test_unit
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/comments.coffee
      invoke    scss
      create      app/assets/stylesheets/comments.scss
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ xw-020304± ⮀ rake routes
                  Prefix Verb   URI Pattern                    Controller#Action
        new_user_session GET    /users/sign_in(.:format)       devise/sessions#new
            user_session POST   /users/sign_in(.:format)       devise/sessions#create
    destroy_user_session DELETE /users/sign_out(.:format)      devise/sessions#destroy
       new_user_password GET    /users/password/new(.:format)  devise/passwords#new
      edit_user_password GET    /users/password/edit(.:format) devise/passwords#edit
           user_password PATCH  /users/password(.:format)      devise/passwords#update
                         PUT    /users/password(.:format)      devise/passwords#update
                         POST   /users/password(.:format)      devise/passwords#create
cancel_user_registration GET    /users/cancel(.:format)        devise/registrations#cancel
   new_user_registration GET    /users/sign_up(.:format)       devise/registrations#new
  edit_user_registration GET    /users/edit(.:format)          devise/registrations#edit
       user_registration PATCH  /users(.:format)               devise/registrations#update
                         PUT    /users(.:format)               devise/registrations#update
                         DELETE /users(.:format)               devise/registrations#destroy
                         POST   /users(.:format)               devise/registrations#create
                messages GET    /messages(.:format)            messages#index
                         POST   /messages(.:format)            messages#create
             new_message GET    /messages/new(.:format)        messages#new
            edit_message GET    /messages/:id/edit(.:format)   messages#edit
                 message GET    /messages/:id(.:format)        messages#show
                         PATCH  /messages/:id(.:format)        messages#update
                         PUT    /messages/:id(.:format)        messages#update
                         DELETE /messages/:id(.:format)        messages#destroy
                    root GET    /                              messages#index
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ xw-020304± ⮀ rake routes
                  Prefix Verb   URI Pattern                                       Controller#Action
        new_user_session GET    /users/sign_in(.:format)                          devise/sessions#new
            user_session POST   /users/sign_in(.:format)                          devise/sessions#create
    destroy_user_session DELETE /users/sign_out(.:format)                         devise/sessions#destroy
       new_user_password GET    /users/password/new(.:format)                     devise/passwords#new
      edit_user_password GET    /users/password/edit(.:format)                    devise/passwords#edit
           user_password PATCH  /users/password(.:format)                         devise/passwords#update
                         PUT    /users/password(.:format)                         devise/passwords#update
                         POST   /users/password(.:format)                         devise/passwords#create
cancel_user_registration GET    /users/cancel(.:format)                           devise/registrations#cancel
   new_user_registration GET    /users/sign_up(.:format)                          devise/registrations#new
  edit_user_registration GET    /users/edit(.:format)                             devise/registrations#edit
       user_registration PATCH  /users(.:format)                                  devise/registrations#update
                         PUT    /users(.:format)                                  devise/registrations#update
                         DELETE /users(.:format)                                  devise/registrations#destroy
                         POST   /users(.:format)                                  devise/registrations#create
        message_comments GET    /messages/:message_id/comments(.:format)          comments#index
                         POST   /messages/:message_id/comments(.:format)          comments#create
     new_message_comment GET    /messages/:message_id/comments/new(.:format)      comments#new
    edit_message_comment GET    /messages/:message_id/comments/:id/edit(.:format) comments#edit
         message_comment GET    /messages/:message_id/comments/:id(.:format)      comments#show
                         PATCH  /messages/:message_id/comments/:id(.:format)      comments#update
                         PUT    /messages/:message_id/comments/:id(.:format)      comments#update
                         DELETE /messages/:message_id/comments/:id(.:format)      comments#destroy
                messages GET    /messages(.:format)                               messages#index
                         POST   /messages(.:format)                               messages#create
             new_message GET    /messages/new(.:format)                           messages#new
            edit_message GET    /messages/:id/edit(.:format)                      messages#edit
                 message GET    /messages/:id(.:format)                           messages#show
                         PATCH  /messages/:id(.:format)                           messages#update
                         PUT    /messages/:id(.:format)                           messages#update
                         DELETE /messages/:id(.:format)                           messages#destroy
                    root GET    /                                                 messages#index
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ xw-020304± ⮀ rails s
=> Booting Puma
=> Rails 5.1.4 application starting in development
=> Run `rails server -h` for more startup options
Puma starting in single mode...
* Version 3.11.2 (ruby 2.3.1-p112), codename: Love Song
* Min threads: 5, max threads: 5
* Environment: development
* Listening on tcp://0.0.0.0:3000
Use Ctrl-C to stop
Started GET "/" for 127.0.0.1 at 2018-02-03 18:23:39 +0800
   (0.2ms)  SELECT "schema_migrations"."version" FROM "schema_migrations" ORDER BY "schema_migrations"."version" ASC
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.6ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (12.2ms)
Completed 200 OK in 2597ms (Views: 2572.7ms | ActiveRecord: 1.2ms)


Started GET "/users/sign_in" for 127.0.0.1 at 2018-02-03 18:23:46 +0800
Processing by Devise::SessionsController#new as HTML
  Rendering devise/sessions/new.html.erb within layouts/application
  Rendered devise/shared/_links.html.erb (1.2ms)
  Rendered devise/sessions/new.html.erb within layouts/application (83.7ms)
Completed 200 OK in 157ms (Views: 141.1ms | ActiveRecord: 0.6ms)


Started POST "/users/sign_in" for 127.0.0.1 at 2018-02-03 18:23:47 +0800
Processing by Devise::SessionsController#create as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"r7G/sPfmcdx2KuE8K4WM6W0zZfn3MPr1rVwNvVroIgl3wgU/Bq/92+g+UDGHgYEajyIsuKFNVnrJc/+41Xvd+Q==", "user"=>{"email"=>"admin@test.com", "password"=>"[FILTERED]", "remember_me"=>"0"}, "commit"=>"Log in"}
  User Load (1.0ms)  SELECT  "users".* FROM "users" WHERE "users"."email" = ? ORDER BY "users"."id" ASC LIMIT ?  [["email", "admin@test.com"], ["LIMIT", 1]]
   (0.1ms)  begin transaction
  SQL (0.9ms)  UPDATE "users" SET "current_sign_in_at" = ?, "sign_in_count" = ?, "updated_at" = ? WHERE "users"."id" = ?  [["current_sign_in_at", "2018-02-03 10:23:48.047010"], ["sign_in_count", 2], ["updated_at", "2018-02-03 10:23:48.048802"], ["id", 2]]
   (1.0ms)  commit transaction
Redirected to http://localhost:3000/
Completed 302 Found in 186ms (ActiveRecord: 3.0ms)


Started GET "/" for 127.0.0.1 at 2018-02-03 18:23:48 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.4ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (2.9ms)
  User Load (0.4ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 60ms (Views: 57.2ms | ActiveRecord: 0.8ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:23:49 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.3ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Rendered comments/_form.html.erb (39.3ms)
  Rendered messages/show.html.erb within layouts/application (42.6ms)
Completed 401 Unauthorized in 65ms (ActiveRecord: 1.3ms)



ActionView::Template::Error (undefined method `connect' for #<Comment:0x007fbca9aa88f0>
Did you mean?  content):
    1: <%= simple_form_for ([@message,@message.comments.build]) do |f| %>
    2: <%= f.input :connect, label: "comment" %>
    3: <%= f.button :submit %>
    4: <% end %>

app/views/comments/_form.html.erb:2:in `block in _app_views_comments__form_html_erb__836480001008184435_70224132684660'
app/views/comments/_form.html.erb:1:in `_app_views_comments__form_html_erb__836480001008184435_70224132684660'
app/views/messages/show.html.erb:6:in `_app_views_messages_show_html_erb__142144221223395500_70224158838540'
Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:24:21 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Rendered comments/_form.html.erb (14.3ms)
  Rendered messages/show.html.erb within layouts/application (19.9ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 98ms (Views: 94.6ms | ActiveRecord: 0.5ms)


Started POST "/messages/9/comments" for 127.0.0.1 at 2018-02-03 18:24:24 +0800

SyntaxError (/Users/xiaowei/messageapp/app/controllers/comments_controller.rb:20: syntax error, unexpected end-of-input, expecting keyword_end):

app/controllers/comments_controller.rb:20: syntax error, unexpected end-of-input, expecting keyword_end
Started GET "/messages/9/comments" for 127.0.0.1 at 2018-02-03 18:24:40 +0800

AbstractController::ActionNotFound (The action 'index' could not be found for CommentsController):

actionpack (5.1.4) lib/abstract_controller/base.rb:119:in `process'
actionview (5.1.4) lib/action_view/rendering.rb:30:in `process'
actionpack (5.1.4) lib/action_controller/metal.rb:189:in `dispatch'
actionpack (5.1.4) lib/action_controller/metal.rb:253:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:49:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:31:in `serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:50:in `block in serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `each'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `serve'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:834:in `call'
warden (1.2.7) lib/warden/manager.rb:36:in `block in call'
warden (1.2.7) lib/warden/manager.rb:35:in `catch'
warden (1.2.7) lib/warden/manager.rb:35:in `call'
rack (2.0.4) lib/rack/etag.rb:25:in `call'
rack (2.0.4) lib/rack/conditional_get.rb:25:in `call'
rack (2.0.4) lib/rack/head.rb:12:in `call'
rack (2.0.4) lib/rack/session/abstract/id.rb:232:in `context'
rack (2.0.4) lib/rack/session/abstract/id.rb:226:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/cookies.rb:613:in `call'
activerecord (5.1.4) lib/active_record/migration.rb:556:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:26:in `block in call'
activesupport (5.1.4) lib/active_support/callbacks.rb:97:in `run_callbacks'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:24:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/debug_exceptions.rb:59:in `call'
web-console (3.5.1) lib/web_console/middleware.rb:135:in `call_app'
web-console (3.5.1) lib/web_console/middleware.rb:28:in `block in call'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `catch'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/show_exceptions.rb:31:in `call'
railties (5.1.4) lib/rails/rack/logger.rb:36:in `call_app'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `block in call'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `block in tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:26:in `tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `tagged'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `call'
sprockets-rails (3.2.1) lib/sprockets/rails/quiet_assets.rb:13:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/remote_ip.rb:79:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/request_id.rb:25:in `call'
rack (2.0.4) lib/rack/method_override.rb:22:in `call'
rack (2.0.4) lib/rack/runtime.rb:22:in `call'
activesupport (5.1.4) lib/active_support/cache/strategy/local_cache_middleware.rb:27:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/static.rb:125:in `call'
rack (2.0.4) lib/rack/sendfile.rb:111:in `call'
railties (5.1.4) lib/rails/engine.rb:522:in `call'
puma (3.11.2) lib/puma/configuration.rb:225:in `call'
puma (3.11.2) lib/puma/server.rb:624:in `handle_request'
puma (3.11.2) lib/puma/server.rb:438:in `process_client'
puma (3.11.2) lib/puma/server.rb:302:in `block in run'
puma (3.11.2) lib/puma/thread_pool.rb:120:in `block in spawn_thread'
Started GET "/messages/" for 127.0.0.1 at 2018-02-03 18:24:49 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.4ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (12.0ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 132ms (Views: 123.1ms | ActiveRecord: 3.0ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:24:51 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (2.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Rendered comments/_form.html.erb (16.7ms)
  Rendered messages/show.html.erb within layouts/application (20.0ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 94ms (Views: 86.9ms | ActiveRecord: 3.1ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:30:25 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (8.0ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of templates [0 times] (0.0ms)
  Rendered comments/_form.html.erb (3.3ms)
  Rendered messages/show.html.erb within layouts/application (15.7ms)
  User Load (0.4ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 82ms (Views: 69.7ms | ActiveRecord: 8.6ms)


Started POST "/messages/9/comments" for 127.0.0.1 at 2018-02-03 18:30:28 +0800
Processing by CommentsController#create as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"dm47fBEXVCE1Sqtemg7Ui64LQ+FZLt4EmghG8C9siwED10r0MO8Y0i4bS9Hf3KJqgoCPvdUXlWbrNN0CufWh8Q==", "comment"=>{"content"=>"dfdfd"}, "commit"=>"Create Comment", "message_id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
   (0.1ms)  begin transaction
   (0.1ms)  commit transaction
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.1ms)  begin transaction
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  SQL (10.5ms)  INSERT INTO "comments" ("content", "message_id", "user_id", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?)  [["content", "dfdfd"], ["message_id", 9], ["user_id", 2], ["created_at", "2018-02-03 10:30:28.259989"], ["updated_at", "2018-02-03 10:30:28.259989"]]
   (1.8ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 26ms (ActiveRecord: 13.2ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:30:28 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.1ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [1 times] (7.7ms)
  Rendered messages/show.html.erb within layouts/application (12.0ms)
Completed 401 Unauthorized in 24ms (ActiveRecord: 0.3ms)



ActionView::Template::Error (undefined method `contert' for #<Comment:0x007fbcb01a1318>
Did you mean?  content
               content=
               content?):
    1: <p><% comment.contert %></p>

app/views/comments/_comment.html.erb:1:in `_app_views_comments__comment_html_erb___1766926366244662605_70224192526140'
app/views/messages/show.html.erb:7:in `_app_views_messages_show_html_erb__142144221223395500_70224174519160'
Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:30:53 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.1ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [1 times] (7.7ms)
  Rendered messages/show.html.erb within layouts/application (11.4ms)
Completed 401 Unauthorized in 21ms (ActiveRecord: 0.3ms)



ActionView::Template::Error (undefined method `contert' for #<Comment:0x007fbcac178ae8>
Did you mean?  content
               content=
               content?):
    1: <p><%= comment.contert %></p>

app/views/comments/_comment.html.erb:1:in `_app_views_comments__comment_html_erb___1766926366244662605_70224158885600'
app/views/messages/show.html.erb:7:in `_app_views_messages_show_html_erb__142144221223395500_70224132712740'
Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:31:06 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.1ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [1 times] (0.3ms)
  Rendered comments/_form.html.erb (3.0ms)
  Rendered messages/show.html.erb within layouts/application (9.2ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 71ms (Views: 67.4ms | ActiveRecord: 0.6ms)


Started POST "/messages/9/comments" for 127.0.0.1 at 2018-02-03 18:31:13 +0800
Processing by CommentsController#create as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"RpUoZFn2wWBxsUyL8t+U8Y5eAlVDsENCRxQZUhpt1PIzLFnseA6Nk2rgrAS3DeIQotXOCc+JCCA2KIKgjPT+Ag==", "comment"=>{"content"=>"sfsfsdfdsfdfs"}, "commit"=>"Create Comment", "message_id"=>"9"}
  Message Load (0.6ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
   (0.1ms)  begin transaction
   (0.1ms)  commit transaction
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.0ms)  begin transaction
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  SQL (0.5ms)  INSERT INTO "comments" ("content", "message_id", "user_id", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?)  [["content", "sfsfsdfdsfdfs"], ["message_id", 9], ["user_id", 2], ["created_at", "2018-02-03 10:31:13.109296"], ["updated_at", "2018-02-03 10:31:13.109296"]]
   (1.0ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 12ms (ActiveRecord: 2.5ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:31:13 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [2 times] (0.6ms)
  Rendered comments/_form.html.erb (4.5ms)
  Rendered messages/show.html.erb within layouts/application (16.4ms)
  User Load (1.0ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 81ms (Views: 77.1ms | ActiveRecord: 1.3ms)


Started POST "/messages/9/comments" for 127.0.0.1 at 2018-02-03 18:31:15 +0800
Processing by CommentsController#create as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"ct75K+T4ZAbYMXKRP9mtbuquxIQyphddGOgEBrNu4q4HZ4ijxQAo9cNgkh56C9uPxiUI2L6fXD9p1J/0JffIXg==", "comment"=>{"content"=>"sdfsfsdfsdfsf"}, "commit"=>"Create Comment", "message_id"=>"9"}
  Message Load (0.3ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
   (0.1ms)  begin transaction
   (0.1ms)  commit transaction
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.1ms)  begin transaction
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  SQL (0.4ms)  INSERT INTO "comments" ("content", "message_id", "user_id", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?)  [["content", "sdfsfsdfsdfsf"], ["message_id", 9], ["user_id", 2], ["created_at", "2018-02-03 10:31:15.095274"], ["updated_at", "2018-02-03 10:31:15.095274"]]
   (8.8ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 19ms (ActiveRecord: 10.2ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:31:15 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [3 times] (0.9ms)
  Rendered comments/_form.html.erb (5.1ms)
  Rendered messages/show.html.erb within layouts/application (15.7ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 90ms (Views: 86.8ms | ActiveRecord: 0.7ms)


Started POST "/messages/9/comments" for 127.0.0.1 at 2018-02-03 18:31:17 +0800
Processing by CommentsController#create as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"WdvInMzr6Z2CYaKWdj8oqNH2BxsEt4q+WpNqkUYNh4AsYrkU7ROlbpkwQhkz7V5J/X3LR4iOwdwrr/Fj0JStcA==", "comment"=>{"content"=>"fsfsdfsfsdf"}, "commit"=>"创建注释", "message_id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
   (0.1ms)  begin transaction
   (0.1ms)  commit transaction
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.1ms)  begin transaction
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  SQL (0.6ms)  INSERT INTO "comments" ("content", "message_id", "user_id", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?)  [["content", "fsfsdfsfsdf"], ["message_id", 9], ["user_id", 2], ["created_at", "2018-02-03 10:31:17.372418"], ["updated_at", "2018-02-03 10:31:17.372418"]]
   (8.9ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 22ms (ActiveRecord: 10.3ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:31:17 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.1ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [4 times] (0.3ms)
  Rendered comments/_form.html.erb (10.1ms)
  Rendered messages/show.html.erb within layouts/application (16.2ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 85ms (Views: 80.4ms | ActiveRecord: 0.6ms)


Started POST "/messages/9/comments" for 127.0.0.1 at 2018-02-03 18:31:19 +0800
Processing by CommentsController#create as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"pXhD30gB8J0SSqCreRqwOuSeKREMoYhHl31JZf3/3U7QwTJXafm8bgkbQCQ8yMbbyBXlTYCYwyXmQdKXa2b3vg==", "comment"=>{"content"=>"fsdfsdfdsfds"}, "commit"=>"Create Comment", "message_id"=>"9"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
   (0.1ms)  begin transaction
   (0.2ms)  commit transaction
  User Load (0.4ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.1ms)  begin transaction
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  SQL (0.5ms)  INSERT INTO "comments" ("content", "message_id", "user_id", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?)  [["content", "fsdfsdfdsfds"], ["message_id", 9], ["user_id", 2], ["created_at", "2018-02-03 10:31:19.978883"], ["updated_at", "2018-02-03 10:31:19.978883"]]
   (9.3ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 26ms (ActiveRecord: 10.9ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:31:19 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [5 times] (0.4ms)
  Rendered comments/_form.html.erb (3.3ms)
  Rendered messages/show.html.erb within layouts/application (9.5ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 89ms (Views: 85.3ms | ActiveRecord: 0.5ms)


Started POST "/messages/9/comments" for 127.0.0.1 at 2018-02-03 18:31:22 +0800
Processing by CommentsController#create as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"3u5oLye1mgJXCc2c1+XwoQUzmdAIly0H8gcF6r8qTxarVxmnBk3W8UxYLROSN4ZAKbhVjISuZmWDO54YKbNl5g==", "comment"=>{"content"=>"sfsfdsfdsfsd"}, "commit"=>"Create Comment", "message_id"=>"9"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
   (0.1ms)  begin transaction
   (0.1ms)  commit transaction
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.1ms)  begin transaction
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  SQL (0.5ms)  INSERT INTO "comments" ("content", "message_id", "user_id", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?)  [["content", "sfsfdsfdsfsd"], ["message_id", 9], ["user_id", 2], ["created_at", "2018-02-03 10:31:22.195217"], ["updated_at", "2018-02-03 10:31:22.195217"]]
   (9.2ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 25ms (ActiveRecord: 10.5ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:31:22 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [6 times] (0.8ms)
  Rendered comments/_form.html.erb (9.5ms)
  Rendered messages/show.html.erb within layouts/application (22.5ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 83ms (Views: 79.6ms | ActiveRecord: 0.5ms)


Started POST "/messages/9/comments" for 127.0.0.1 at 2018-02-03 18:31:24 +0800
Processing by CommentsController#create as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"FaLBXYMQRV8DHkdaw7U1YQC4Z6bj4Ahz0fK6dm5wenxgG7DVougJrBhPp9WGZ0OALDOr+m/ZQxGgziGE+OlQjA==", "comment"=>{"content"=>"fsfsfdfdsf"}, "commit"=>"Create Comment", "message_id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
   (0.1ms)  begin transaction
   (0.1ms)  commit transaction
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.0ms)  begin transaction
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  SQL (0.8ms)  INSERT INTO "comments" ("content", "message_id", "user_id", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?)  [["content", "fsfsfdfdsf"], ["message_id", 9], ["user_id", 2], ["created_at", "2018-02-03 10:31:24.369603"], ["updated_at", "2018-02-03 10:31:24.369603"]]
   (8.9ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 20ms (ActiveRecord: 10.4ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:31:24 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.4ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [7 times] (0.7ms)
  Rendered comments/_form.html.erb (3.2ms)
  Rendered messages/show.html.erb within layouts/application (11.4ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 89ms (Views: 82.7ms | ActiveRecord: 0.8ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:34:08 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.3ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [7 times] (2.5ms)
  Rendered comments/_form.html.erb (3.4ms)
  Rendered messages/show.html.erb within layouts/application (18.3ms)
  User Load (0.4ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 80ms (Views: 77.2ms | ActiveRecord: 0.8ms)


Started GET "/messages/9/comments/1/edit" for 127.0.0.1 at 2018-02-03 18:34:12 +0800

AbstractController::ActionNotFound (The action 'edit' could not be found for CommentsController):

actionpack (5.1.4) lib/abstract_controller/base.rb:119:in `process'
actionview (5.1.4) lib/action_view/rendering.rb:30:in `process'
actionpack (5.1.4) lib/action_controller/metal.rb:189:in `dispatch'
actionpack (5.1.4) lib/action_controller/metal.rb:253:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:49:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:31:in `serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:50:in `block in serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `each'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `serve'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:834:in `call'
warden (1.2.7) lib/warden/manager.rb:36:in `block in call'
warden (1.2.7) lib/warden/manager.rb:35:in `catch'
warden (1.2.7) lib/warden/manager.rb:35:in `call'
rack (2.0.4) lib/rack/etag.rb:25:in `call'
rack (2.0.4) lib/rack/conditional_get.rb:25:in `call'
rack (2.0.4) lib/rack/head.rb:12:in `call'
rack (2.0.4) lib/rack/session/abstract/id.rb:232:in `context'
rack (2.0.4) lib/rack/session/abstract/id.rb:226:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/cookies.rb:613:in `call'
activerecord (5.1.4) lib/active_record/migration.rb:556:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:26:in `block in call'
activesupport (5.1.4) lib/active_support/callbacks.rb:97:in `run_callbacks'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:24:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/debug_exceptions.rb:59:in `call'
web-console (3.5.1) lib/web_console/middleware.rb:135:in `call_app'
web-console (3.5.1) lib/web_console/middleware.rb:28:in `block in call'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `catch'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/show_exceptions.rb:31:in `call'
railties (5.1.4) lib/rails/rack/logger.rb:36:in `call_app'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `block in call'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `block in tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:26:in `tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `tagged'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `call'
sprockets-rails (3.2.1) lib/sprockets/rails/quiet_assets.rb:13:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/remote_ip.rb:79:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/request_id.rb:25:in `call'
rack (2.0.4) lib/rack/method_override.rb:22:in `call'
rack (2.0.4) lib/rack/runtime.rb:22:in `call'
activesupport (5.1.4) lib/active_support/cache/strategy/local_cache_middleware.rb:27:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/static.rb:125:in `call'
rack (2.0.4) lib/rack/sendfile.rb:111:in `call'
railties (5.1.4) lib/rails/engine.rb:522:in `call'
puma (3.11.2) lib/puma/configuration.rb:225:in `call'
puma (3.11.2) lib/puma/server.rb:624:in `handle_request'
puma (3.11.2) lib/puma/server.rb:438:in `process_client'
puma (3.11.2) lib/puma/server.rb:302:in `block in run'
puma (3.11.2) lib/puma/thread_pool.rb:120:in `block in spawn_thread'
Started GET "/messages/9/comments/1/edit" for 127.0.0.1 at 2018-02-03 18:45:04 +0800

SyntaxError (/Users/xiaowei/messageapp/app/controllers/comments_controller.rb:44: syntax error, unexpected end-of-input, expecting keyword_end):

app/controllers/comments_controller.rb:44: syntax error, unexpected end-of-input, expecting keyword_end
Started GET "/messages/9/comments/1/edit" for 127.0.0.1 at 2018-02-03 18:45:23 +0800
Processing by CommentsController#edit as HTML
  Parameters: {"message_id"=>"9", "id"=>"1"}
  Message Load (0.5ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.1ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
Completed 401 Unauthorized in 173ms (ActiveRecord: 2.3ms)



ActionController::UnknownFormat (CommentsController#edit is missing a template for this request format and variant.

request.formats: ["text/html"]
request.variant: []

NOTE! For XHR/Ajax or API requests, this action would normally respond with 204 No Content: an empty white screen. Since you're loading it in a web browser, we assume that you expected to actually render a template, not nothing, so we're showing an error to be extra-clear. If you expect 204 No Content, carry on. That's what you'll get from an XHR or API request. Give it a shot.):

actionpack (5.1.4) lib/action_controller/metal/implicit_render.rb:53:in `default_render'
actionpack (5.1.4) lib/action_controller/metal/basic_implicit_render.rb:4:in `block in send_action'
actionpack (5.1.4) lib/action_controller/metal/basic_implicit_render.rb:4:in `tap'
actionpack (5.1.4) lib/action_controller/metal/basic_implicit_render.rb:4:in `send_action'
actionpack (5.1.4) lib/abstract_controller/base.rb:186:in `process_action'
actionpack (5.1.4) lib/action_controller/metal/rendering.rb:30:in `process_action'
actionpack (5.1.4) lib/abstract_controller/callbacks.rb:20:in `block in process_action'
activesupport (5.1.4) lib/active_support/callbacks.rb:131:in `run_callbacks'
actionpack (5.1.4) lib/abstract_controller/callbacks.rb:19:in `process_action'
actionpack (5.1.4) lib/action_controller/metal/rescue.rb:20:in `process_action'
actionpack (5.1.4) lib/action_controller/metal/instrumentation.rb:32:in `block in process_action'
activesupport (5.1.4) lib/active_support/notifications.rb:166:in `block in instrument'
activesupport (5.1.4) lib/active_support/notifications/instrumenter.rb:21:in `instrument'
activesupport (5.1.4) lib/active_support/notifications.rb:166:in `instrument'
actionpack (5.1.4) lib/action_controller/metal/instrumentation.rb:30:in `process_action'
actionpack (5.1.4) lib/action_controller/metal/params_wrapper.rb:252:in `process_action'
activerecord (5.1.4) lib/active_record/railties/controller_runtime.rb:22:in `process_action'
actionpack (5.1.4) lib/abstract_controller/base.rb:124:in `process'
actionview (5.1.4) lib/action_view/rendering.rb:30:in `process'
actionpack (5.1.4) lib/action_controller/metal.rb:189:in `dispatch'
actionpack (5.1.4) lib/action_controller/metal.rb:253:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:49:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:31:in `serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:50:in `block in serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `each'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `serve'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:834:in `call'
warden (1.2.7) lib/warden/manager.rb:36:in `block in call'
warden (1.2.7) lib/warden/manager.rb:35:in `catch'
warden (1.2.7) lib/warden/manager.rb:35:in `call'
rack (2.0.4) lib/rack/etag.rb:25:in `call'
rack (2.0.4) lib/rack/conditional_get.rb:25:in `call'
rack (2.0.4) lib/rack/head.rb:12:in `call'
rack (2.0.4) lib/rack/session/abstract/id.rb:232:in `context'
rack (2.0.4) lib/rack/session/abstract/id.rb:226:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/cookies.rb:613:in `call'
activerecord (5.1.4) lib/active_record/migration.rb:556:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:26:in `block in call'
activesupport (5.1.4) lib/active_support/callbacks.rb:97:in `run_callbacks'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:24:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/debug_exceptions.rb:59:in `call'
web-console (3.5.1) lib/web_console/middleware.rb:135:in `call_app'
web-console (3.5.1) lib/web_console/middleware.rb:28:in `block in call'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `catch'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/show_exceptions.rb:31:in `call'
railties (5.1.4) lib/rails/rack/logger.rb:36:in `call_app'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `block in call'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `block in tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:26:in `tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `tagged'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `call'
sprockets-rails (3.2.1) lib/sprockets/rails/quiet_assets.rb:13:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/remote_ip.rb:79:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/request_id.rb:25:in `call'
rack (2.0.4) lib/rack/method_override.rb:22:in `call'
rack (2.0.4) lib/rack/runtime.rb:22:in `call'
activesupport (5.1.4) lib/active_support/cache/strategy/local_cache_middleware.rb:27:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/static.rb:125:in `call'
rack (2.0.4) lib/rack/sendfile.rb:111:in `call'
railties (5.1.4) lib/rails/engine.rb:522:in `call'
puma (3.11.2) lib/puma/configuration.rb:225:in `call'
puma (3.11.2) lib/puma/server.rb:624:in `handle_request'
puma (3.11.2) lib/puma/server.rb:438:in `process_client'
puma (3.11.2) lib/puma/server.rb:302:in `block in run'
puma (3.11.2) lib/puma/thread_pool.rb:120:in `block in spawn_thread'
Started GET "/messages/9/comments/" for 127.0.0.1 at 2018-02-03 18:45:35 +0800

AbstractController::ActionNotFound (The action 'index' could not be found for CommentsController):

actionpack (5.1.4) lib/abstract_controller/base.rb:119:in `process'
actionview (5.1.4) lib/action_view/rendering.rb:30:in `process'
actionpack (5.1.4) lib/action_controller/metal.rb:189:in `dispatch'
actionpack (5.1.4) lib/action_controller/metal.rb:253:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:49:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:31:in `serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:50:in `block in serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `each'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `serve'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:834:in `call'
warden (1.2.7) lib/warden/manager.rb:36:in `block in call'
warden (1.2.7) lib/warden/manager.rb:35:in `catch'
warden (1.2.7) lib/warden/manager.rb:35:in `call'
rack (2.0.4) lib/rack/etag.rb:25:in `call'
rack (2.0.4) lib/rack/conditional_get.rb:25:in `call'
rack (2.0.4) lib/rack/head.rb:12:in `call'
rack (2.0.4) lib/rack/session/abstract/id.rb:232:in `context'
rack (2.0.4) lib/rack/session/abstract/id.rb:226:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/cookies.rb:613:in `call'
activerecord (5.1.4) lib/active_record/migration.rb:556:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:26:in `block in call'
activesupport (5.1.4) lib/active_support/callbacks.rb:97:in `run_callbacks'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:24:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/debug_exceptions.rb:59:in `call'
web-console (3.5.1) lib/web_console/middleware.rb:135:in `call_app'
web-console (3.5.1) lib/web_console/middleware.rb:28:in `block in call'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `catch'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/show_exceptions.rb:31:in `call'
railties (5.1.4) lib/rails/rack/logger.rb:36:in `call_app'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `block in call'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `block in tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:26:in `tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `tagged'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `call'
sprockets-rails (3.2.1) lib/sprockets/rails/quiet_assets.rb:13:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/remote_ip.rb:79:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/request_id.rb:25:in `call'
rack (2.0.4) lib/rack/method_override.rb:22:in `call'
rack (2.0.4) lib/rack/runtime.rb:22:in `call'
activesupport (5.1.4) lib/active_support/cache/strategy/local_cache_middleware.rb:27:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/static.rb:125:in `call'
rack (2.0.4) lib/rack/sendfile.rb:111:in `call'
railties (5.1.4) lib/rails/engine.rb:522:in `call'
puma (3.11.2) lib/puma/configuration.rb:225:in `call'
puma (3.11.2) lib/puma/server.rb:624:in `handle_request'
puma (3.11.2) lib/puma/server.rb:438:in `process_client'
puma (3.11.2) lib/puma/server.rb:302:in `block in run'
puma (3.11.2) lib/puma/thread_pool.rb:120:in `block in spawn_thread'
Started GET "/messages/" for 127.0.0.1 at 2018-02-03 18:45:39 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.3ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (2.4ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 95ms (Views: 90.1ms | ActiveRecord: 1.5ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:45:41 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.4ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [7 times] (1.5ms)
  Rendered comments/_form.html.erb (4.0ms)
  Rendered messages/show.html.erb within layouts/application (12.0ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 75ms (Views: 69.7ms | ActiveRecord: 0.9ms)


Started GET "/messages/9/comments/1/edit" for 127.0.0.1 at 2018-02-03 18:45:56 +0800
Processing by CommentsController#edit as HTML
  Parameters: {"message_id"=>"9", "id"=>"1"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.3ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
Completed 401 Unauthorized in 157ms (ActiveRecord: 0.4ms)



ActionController::UnknownFormat (CommentsController#edit is missing a template for this request format and variant.

request.formats: ["text/html"]
request.variant: []

NOTE! For XHR/Ajax or API requests, this action would normally respond with 204 No Content: an empty white screen. Since you're loading it in a web browser, we assume that you expected to actually render a template, not nothing, so we're showing an error to be extra-clear. If you expect 204 No Content, carry on. That's what you'll get from an XHR or API request. Give it a shot.):

actionpack (5.1.4) lib/action_controller/metal/implicit_render.rb:53:in `default_render'
actionpack (5.1.4) lib/action_controller/metal/basic_implicit_render.rb:4:in `block in send_action'
actionpack (5.1.4) lib/action_controller/metal/basic_implicit_render.rb:4:in `tap'
actionpack (5.1.4) lib/action_controller/metal/basic_implicit_render.rb:4:in `send_action'
actionpack (5.1.4) lib/abstract_controller/base.rb:186:in `process_action'
actionpack (5.1.4) lib/action_controller/metal/rendering.rb:30:in `process_action'
actionpack (5.1.4) lib/abstract_controller/callbacks.rb:20:in `block in process_action'
activesupport (5.1.4) lib/active_support/callbacks.rb:131:in `run_callbacks'
actionpack (5.1.4) lib/abstract_controller/callbacks.rb:19:in `process_action'
actionpack (5.1.4) lib/action_controller/metal/rescue.rb:20:in `process_action'
actionpack (5.1.4) lib/action_controller/metal/instrumentation.rb:32:in `block in process_action'
activesupport (5.1.4) lib/active_support/notifications.rb:166:in `block in instrument'
activesupport (5.1.4) lib/active_support/notifications/instrumenter.rb:21:in `instrument'
activesupport (5.1.4) lib/active_support/notifications.rb:166:in `instrument'
actionpack (5.1.4) lib/action_controller/metal/instrumentation.rb:30:in `process_action'
actionpack (5.1.4) lib/action_controller/metal/params_wrapper.rb:252:in `process_action'
activerecord (5.1.4) lib/active_record/railties/controller_runtime.rb:22:in `process_action'
actionpack (5.1.4) lib/abstract_controller/base.rb:124:in `process'
actionview (5.1.4) lib/action_view/rendering.rb:30:in `process'
actionpack (5.1.4) lib/action_controller/metal.rb:189:in `dispatch'
actionpack (5.1.4) lib/action_controller/metal.rb:253:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:49:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:31:in `serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:50:in `block in serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `each'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `serve'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:834:in `call'
warden (1.2.7) lib/warden/manager.rb:36:in `block in call'
warden (1.2.7) lib/warden/manager.rb:35:in `catch'
warden (1.2.7) lib/warden/manager.rb:35:in `call'
rack (2.0.4) lib/rack/etag.rb:25:in `call'
rack (2.0.4) lib/rack/conditional_get.rb:25:in `call'
rack (2.0.4) lib/rack/head.rb:12:in `call'
rack (2.0.4) lib/rack/session/abstract/id.rb:232:in `context'
rack (2.0.4) lib/rack/session/abstract/id.rb:226:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/cookies.rb:613:in `call'
activerecord (5.1.4) lib/active_record/migration.rb:556:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:26:in `block in call'
activesupport (5.1.4) lib/active_support/callbacks.rb:97:in `run_callbacks'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:24:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/debug_exceptions.rb:59:in `call'
web-console (3.5.1) lib/web_console/middleware.rb:135:in `call_app'
web-console (3.5.1) lib/web_console/middleware.rb:28:in `block in call'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `catch'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/show_exceptions.rb:31:in `call'
railties (5.1.4) lib/rails/rack/logger.rb:36:in `call_app'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `block in call'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `block in tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:26:in `tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `tagged'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `call'
sprockets-rails (3.2.1) lib/sprockets/rails/quiet_assets.rb:13:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/remote_ip.rb:79:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/request_id.rb:25:in `call'
rack (2.0.4) lib/rack/method_override.rb:22:in `call'
rack (2.0.4) lib/rack/runtime.rb:22:in `call'
activesupport (5.1.4) lib/active_support/cache/strategy/local_cache_middleware.rb:27:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/static.rb:125:in `call'
rack (2.0.4) lib/rack/sendfile.rb:111:in `call'
railties (5.1.4) lib/rails/engine.rb:522:in `call'
puma (3.11.2) lib/puma/configuration.rb:225:in `call'
puma (3.11.2) lib/puma/server.rb:624:in `handle_request'
puma (3.11.2) lib/puma/server.rb:438:in `process_client'
puma (3.11.2) lib/puma/server.rb:302:in `block in run'
puma (3.11.2) lib/puma/thread_pool.rb:120:in `block in spawn_thread'
Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:45:59 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.1ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [7 times] (1.4ms)
  Rendered comments/_form.html.erb (2.9ms)
  Rendered messages/show.html.erb within layouts/application (9.8ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 65ms (Views: 61.8ms | ActiveRecord: 0.5ms)


Started DELETE "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 18:46:01 +0800

AbstractController::ActionNotFound (The action 'destroy' could not be found for CommentsController):

actionpack (5.1.4) lib/abstract_controller/base.rb:119:in `process'
actionview (5.1.4) lib/action_view/rendering.rb:30:in `process'
actionpack (5.1.4) lib/action_controller/metal.rb:189:in `dispatch'
actionpack (5.1.4) lib/action_controller/metal.rb:253:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:49:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:31:in `serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:50:in `block in serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `each'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `serve'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:834:in `call'
warden (1.2.7) lib/warden/manager.rb:36:in `block in call'
warden (1.2.7) lib/warden/manager.rb:35:in `catch'
warden (1.2.7) lib/warden/manager.rb:35:in `call'
rack (2.0.4) lib/rack/etag.rb:25:in `call'
rack (2.0.4) lib/rack/conditional_get.rb:38:in `call'
rack (2.0.4) lib/rack/head.rb:12:in `call'
rack (2.0.4) lib/rack/session/abstract/id.rb:232:in `context'
rack (2.0.4) lib/rack/session/abstract/id.rb:226:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/cookies.rb:613:in `call'
activerecord (5.1.4) lib/active_record/migration.rb:556:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:26:in `block in call'
activesupport (5.1.4) lib/active_support/callbacks.rb:97:in `run_callbacks'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:24:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/debug_exceptions.rb:59:in `call'
web-console (3.5.1) lib/web_console/middleware.rb:135:in `call_app'
web-console (3.5.1) lib/web_console/middleware.rb:28:in `block in call'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `catch'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/show_exceptions.rb:31:in `call'
railties (5.1.4) lib/rails/rack/logger.rb:36:in `call_app'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `block in call'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `block in tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:26:in `tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `tagged'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `call'
sprockets-rails (3.2.1) lib/sprockets/rails/quiet_assets.rb:13:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/remote_ip.rb:79:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/request_id.rb:25:in `call'
rack (2.0.4) lib/rack/method_override.rb:22:in `call'
rack (2.0.4) lib/rack/runtime.rb:22:in `call'
activesupport (5.1.4) lib/active_support/cache/strategy/local_cache_middleware.rb:27:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/static.rb:125:in `call'
rack (2.0.4) lib/rack/sendfile.rb:111:in `call'
railties (5.1.4) lib/rails/engine.rb:522:in `call'
puma (3.11.2) lib/puma/configuration.rb:225:in `call'
puma (3.11.2) lib/puma/server.rb:624:in `handle_request'
puma (3.11.2) lib/puma/server.rb:438:in `process_client'
puma (3.11.2) lib/puma/server.rb:302:in `block in run'
puma (3.11.2) lib/puma/thread_pool.rb:120:in `block in spawn_thread'
Started GET "/messages/9/comments/1/edit" for 127.0.0.1 at 2018-02-03 18:51:39 +0800
Processing by CommentsController#edit as HTML
  Parameters: {"message_id"=>"9", "id"=>"1"}
  Message Load (0.4ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.2ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
  Rendering comments/edit.html.erb within layouts/application
  Rendered comments/edit.html.erb within layouts/application (3.4ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 92ms (Views: 67.6ms | ActiveRecord: 2.8ms)


Started PATCH "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 18:51:41 +0800
Processing by CommentsController#update as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"UOnpKH6hx5yVtXSlKDXtLM70k8n0SIiz/o2KfC+LM/wz/FNZuXqKOtwrmMKkCvzchZyXot8r42WcWuGEuyh6Qg==", "comment"=>{"content"=>"dfdfd"}, "commit"=>"Update Comment", "message_id"=>"9", "id"=>"1"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.2ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
   (0.1ms)  begin transaction
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.1ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 9ms (ActiveRecord: 0.7ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:51:41 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [7 times] (1.8ms)
  Rendered comments/_form.html.erb (3.3ms)
  Rendered messages/show.html.erb within layouts/application (11.9ms)
  User Load (0.4ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 70ms (Views: 64.8ms | ActiveRecord: 0.7ms)


Started DELETE "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 18:51:43 +0800

AbstractController::ActionNotFound (The action 'destroy' could not be found for CommentsController):

actionpack (5.1.4) lib/abstract_controller/base.rb:119:in `process'
actionview (5.1.4) lib/action_view/rendering.rb:30:in `process'
actionpack (5.1.4) lib/action_controller/metal.rb:189:in `dispatch'
actionpack (5.1.4) lib/action_controller/metal.rb:253:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:49:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:31:in `serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:50:in `block in serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `each'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `serve'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:834:in `call'
warden (1.2.7) lib/warden/manager.rb:36:in `block in call'
warden (1.2.7) lib/warden/manager.rb:35:in `catch'
warden (1.2.7) lib/warden/manager.rb:35:in `call'
rack (2.0.4) lib/rack/etag.rb:25:in `call'
rack (2.0.4) lib/rack/conditional_get.rb:38:in `call'
rack (2.0.4) lib/rack/head.rb:12:in `call'
rack (2.0.4) lib/rack/session/abstract/id.rb:232:in `context'
rack (2.0.4) lib/rack/session/abstract/id.rb:226:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/cookies.rb:613:in `call'
activerecord (5.1.4) lib/active_record/migration.rb:556:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:26:in `block in call'
activesupport (5.1.4) lib/active_support/callbacks.rb:97:in `run_callbacks'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:24:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/debug_exceptions.rb:59:in `call'
web-console (3.5.1) lib/web_console/middleware.rb:135:in `call_app'
web-console (3.5.1) lib/web_console/middleware.rb:28:in `block in call'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `catch'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/show_exceptions.rb:31:in `call'
railties (5.1.4) lib/rails/rack/logger.rb:36:in `call_app'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `block in call'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `block in tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:26:in `tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `tagged'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `call'
sprockets-rails (3.2.1) lib/sprockets/rails/quiet_assets.rb:13:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/remote_ip.rb:79:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/request_id.rb:25:in `call'
rack (2.0.4) lib/rack/method_override.rb:22:in `call'
rack (2.0.4) lib/rack/runtime.rb:22:in `call'
activesupport (5.1.4) lib/active_support/cache/strategy/local_cache_middleware.rb:27:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/static.rb:125:in `call'
rack (2.0.4) lib/rack/sendfile.rb:111:in `call'
railties (5.1.4) lib/rails/engine.rb:522:in `call'
puma (3.11.2) lib/puma/configuration.rb:225:in `call'
puma (3.11.2) lib/puma/server.rb:624:in `handle_request'
puma (3.11.2) lib/puma/server.rb:438:in `process_client'
puma (3.11.2) lib/puma/server.rb:302:in `block in run'
puma (3.11.2) lib/puma/thread_pool.rb:120:in `block in spawn_thread'
Started GET "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 18:53:50 +0800

AbstractController::ActionNotFound (The action 'show' could not be found for CommentsController):

actionpack (5.1.4) lib/abstract_controller/base.rb:119:in `process'
actionview (5.1.4) lib/action_view/rendering.rb:30:in `process'
actionpack (5.1.4) lib/action_controller/metal.rb:189:in `dispatch'
actionpack (5.1.4) lib/action_controller/metal.rb:253:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:49:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:31:in `serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:50:in `block in serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `each'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `serve'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:834:in `call'
warden (1.2.7) lib/warden/manager.rb:36:in `block in call'
warden (1.2.7) lib/warden/manager.rb:35:in `catch'
warden (1.2.7) lib/warden/manager.rb:35:in `call'
rack (2.0.4) lib/rack/etag.rb:25:in `call'
rack (2.0.4) lib/rack/conditional_get.rb:25:in `call'
rack (2.0.4) lib/rack/head.rb:12:in `call'
rack (2.0.4) lib/rack/session/abstract/id.rb:232:in `context'
rack (2.0.4) lib/rack/session/abstract/id.rb:226:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/cookies.rb:613:in `call'
activerecord (5.1.4) lib/active_record/migration.rb:556:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:26:in `block in call'
activesupport (5.1.4) lib/active_support/callbacks.rb:97:in `run_callbacks'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:24:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/debug_exceptions.rb:59:in `call'
web-console (3.5.1) lib/web_console/middleware.rb:135:in `call_app'
web-console (3.5.1) lib/web_console/middleware.rb:28:in `block in call'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `catch'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/show_exceptions.rb:31:in `call'
railties (5.1.4) lib/rails/rack/logger.rb:36:in `call_app'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `block in call'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `block in tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:26:in `tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `tagged'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `call'
sprockets-rails (3.2.1) lib/sprockets/rails/quiet_assets.rb:13:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/remote_ip.rb:79:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/request_id.rb:25:in `call'
rack (2.0.4) lib/rack/method_override.rb:22:in `call'
rack (2.0.4) lib/rack/runtime.rb:22:in `call'
activesupport (5.1.4) lib/active_support/cache/strategy/local_cache_middleware.rb:27:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/static.rb:125:in `call'
rack (2.0.4) lib/rack/sendfile.rb:111:in `call'
railties (5.1.4) lib/rails/engine.rb:522:in `call'
puma (3.11.2) lib/puma/configuration.rb:225:in `call'
puma (3.11.2) lib/puma/server.rb:624:in `handle_request'
puma (3.11.2) lib/puma/server.rb:438:in `process_client'
puma (3.11.2) lib/puma/server.rb:302:in `block in run'
puma (3.11.2) lib/puma/thread_pool.rb:120:in `block in spawn_thread'
Started GET "/messages/9/comments/" for 127.0.0.1 at 2018-02-03 18:53:55 +0800

AbstractController::ActionNotFound (The action 'index' could not be found for CommentsController):

actionpack (5.1.4) lib/abstract_controller/base.rb:119:in `process'
actionview (5.1.4) lib/action_view/rendering.rb:30:in `process'
actionpack (5.1.4) lib/action_controller/metal.rb:189:in `dispatch'
actionpack (5.1.4) lib/action_controller/metal.rb:253:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:49:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:31:in `serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:50:in `block in serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `each'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `serve'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:834:in `call'
warden (1.2.7) lib/warden/manager.rb:36:in `block in call'
warden (1.2.7) lib/warden/manager.rb:35:in `catch'
warden (1.2.7) lib/warden/manager.rb:35:in `call'
rack (2.0.4) lib/rack/etag.rb:25:in `call'
rack (2.0.4) lib/rack/conditional_get.rb:25:in `call'
rack (2.0.4) lib/rack/head.rb:12:in `call'
rack (2.0.4) lib/rack/session/abstract/id.rb:232:in `context'
rack (2.0.4) lib/rack/session/abstract/id.rb:226:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/cookies.rb:613:in `call'
activerecord (5.1.4) lib/active_record/migration.rb:556:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:26:in `block in call'
activesupport (5.1.4) lib/active_support/callbacks.rb:97:in `run_callbacks'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:24:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/debug_exceptions.rb:59:in `call'
web-console (3.5.1) lib/web_console/middleware.rb:135:in `call_app'
web-console (3.5.1) lib/web_console/middleware.rb:28:in `block in call'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `catch'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/show_exceptions.rb:31:in `call'
railties (5.1.4) lib/rails/rack/logger.rb:36:in `call_app'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `block in call'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `block in tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:26:in `tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `tagged'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `call'
sprockets-rails (3.2.1) lib/sprockets/rails/quiet_assets.rb:13:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/remote_ip.rb:79:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/request_id.rb:25:in `call'
rack (2.0.4) lib/rack/method_override.rb:22:in `call'
rack (2.0.4) lib/rack/runtime.rb:22:in `call'
activesupport (5.1.4) lib/active_support/cache/strategy/local_cache_middleware.rb:27:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/static.rb:125:in `call'
rack (2.0.4) lib/rack/sendfile.rb:111:in `call'
railties (5.1.4) lib/rails/engine.rb:522:in `call'
puma (3.11.2) lib/puma/configuration.rb:225:in `call'
puma (3.11.2) lib/puma/server.rb:624:in `handle_request'
puma (3.11.2) lib/puma/server.rb:438:in `process_client'
puma (3.11.2) lib/puma/server.rb:302:in `block in run'
puma (3.11.2) lib/puma/thread_pool.rb:120:in `block in spawn_thread'
Started GET "/messages/" for 127.0.0.1 at 2018-02-03 18:53:58 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.7ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (12.0ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 104ms (Views: 99.1ms | ActiveRecord: 3.1ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:54:00 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (1.0ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [7 times] (1.6ms)
  Rendered comments/_form.html.erb (3.5ms)
  Rendered messages/show.html.erb within layouts/application (22.9ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 82ms (Views: 75.9ms | ActiveRecord: 2.0ms)


Started GET "/messages/9/comments/1/edit" for 127.0.0.1 at 2018-02-03 18:54:02 +0800
Processing by CommentsController#edit as HTML
  Parameters: {"message_id"=>"9", "id"=>"1"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.3ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
  Rendering comments/edit.html.erb within layouts/application
  Rendered comments/edit.html.erb within layouts/application (2.8ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 67ms (Views: 50.0ms | ActiveRecord: 0.6ms)


Started PATCH "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 18:54:03 +0800
Processing by CommentsController#update as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"jj7jriHk9ZLNAVyYmdQJwTeekpqpozTtu+e28XAmpATtK1nf5j+4NISfsP8V6xgxfPaW8YLAXzvZMN0J5IXtug==", "comment"=>{"content"=>"dfdfd"}, "commit"=>"Update Comment", "message_id"=>"9", "id"=>"1"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.2ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
   (0.0ms)  begin transaction
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.1ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 7ms (ActiveRecord: 0.6ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:54:03 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [7 times] (3.5ms)
  Rendered comments/_form.html.erb (6.8ms)
  Rendered messages/show.html.erb within layouts/application (19.7ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 77ms (Views: 73.5ms | ActiveRecord: 0.6ms)


Started DELETE "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 18:54:06 +0800
Processing by CommentsController#destroy as HTML
  Parameters: {"authenticity_token"=>"FfkDAN4pvN52n1RRcOmTU3OUwMYyFujnHZHaFlTi7mpgQHKI/9HwLW3OtN41O+WyXx8Mmr4vo4VsrUHkwnvEmg==", "message_id"=>"9", "id"=>"1"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
Completed 401 Unauthorized in 4ms (ActiveRecord: 0.2ms)



NoMethodError (undefined method `destory' for nil:NilClass):

app/controllers/comments_controller.rb:30:in `destroy'
Started GET "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 18:59:08 +0800

AbstractController::ActionNotFound (The action 'show' could not be found for CommentsController):

actionpack (5.1.4) lib/abstract_controller/base.rb:119:in `process'
actionview (5.1.4) lib/action_view/rendering.rb:30:in `process'
actionpack (5.1.4) lib/action_controller/metal.rb:189:in `dispatch'
actionpack (5.1.4) lib/action_controller/metal.rb:253:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:49:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:31:in `serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:50:in `block in serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `each'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `serve'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:834:in `call'
warden (1.2.7) lib/warden/manager.rb:36:in `block in call'
warden (1.2.7) lib/warden/manager.rb:35:in `catch'
warden (1.2.7) lib/warden/manager.rb:35:in `call'
rack (2.0.4) lib/rack/etag.rb:25:in `call'
rack (2.0.4) lib/rack/conditional_get.rb:25:in `call'
rack (2.0.4) lib/rack/head.rb:12:in `call'
rack (2.0.4) lib/rack/session/abstract/id.rb:232:in `context'
rack (2.0.4) lib/rack/session/abstract/id.rb:226:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/cookies.rb:613:in `call'
activerecord (5.1.4) lib/active_record/migration.rb:556:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:26:in `block in call'
activesupport (5.1.4) lib/active_support/callbacks.rb:97:in `run_callbacks'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:24:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/debug_exceptions.rb:59:in `call'
web-console (3.5.1) lib/web_console/middleware.rb:135:in `call_app'
web-console (3.5.1) lib/web_console/middleware.rb:28:in `block in call'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `catch'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/show_exceptions.rb:31:in `call'
railties (5.1.4) lib/rails/rack/logger.rb:36:in `call_app'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `block in call'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `block in tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:26:in `tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `tagged'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `call'
sprockets-rails (3.2.1) lib/sprockets/rails/quiet_assets.rb:13:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/remote_ip.rb:79:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/request_id.rb:25:in `call'
rack (2.0.4) lib/rack/method_override.rb:22:in `call'
rack (2.0.4) lib/rack/runtime.rb:22:in `call'
activesupport (5.1.4) lib/active_support/cache/strategy/local_cache_middleware.rb:27:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/static.rb:125:in `call'
rack (2.0.4) lib/rack/sendfile.rb:111:in `call'
railties (5.1.4) lib/rails/engine.rb:522:in `call'
puma (3.11.2) lib/puma/configuration.rb:225:in `call'
puma (3.11.2) lib/puma/server.rb:624:in `handle_request'
puma (3.11.2) lib/puma/server.rb:438:in `process_client'
puma (3.11.2) lib/puma/server.rb:302:in `block in run'
puma (3.11.2) lib/puma/thread_pool.rb:120:in `block in spawn_thread'
Started GET "/messages/9/" for 127.0.0.1 at 2018-02-03 18:59:13 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [7 times] (2.4ms)
  Rendered comments/_form.html.erb (4.3ms)
  Rendered messages/show.html.erb within layouts/application (13.7ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 81ms (Views: 78.3ms | ActiveRecord: 0.6ms)


Started DELETE "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 18:59:16 +0800
Processing by CommentsController#destroy as HTML
  Parameters: {"authenticity_token"=>"0w0ZONoRK6BzwX+Eghf+ZybhUDSXKKKgy235SYysTaqmtGiw++lnU2iQnwvHxYiGCmqcaBsR6cK6UWK7GjVnWg==", "message_id"=>"9", "id"=>"1"}
  Message Load (0.6ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
Completed 401 Unauthorized in 9ms (ActiveRecord: 0.6ms)



NoMethodError (undefined method `destory' for nil:NilClass):

app/controllers/comments_controller.rb:30:in `destroy'
Started GET "/messages/9/comments/1/edit" for 127.0.0.1 at 2018-02-03 18:59:30 +0800
Processing by CommentsController#edit as HTML
  Parameters: {"message_id"=>"9", "id"=>"1"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.4ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
  Rendering comments/edit.html.erb within layouts/application
  Rendered comments/edit.html.erb within layouts/application (3.0ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 58ms (Views: 50.1ms | ActiveRecord: 0.9ms)


Started PATCH "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 18:59:31 +0800
Processing by CommentsController#update as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"yzEmtgwZG2T6GEMgoT34SWULhwkU10LgKnpL8hYL2NmoJJzHy8JWwrOGr0ctAum5LmODYj+0KTZIrSAKgqiRZw==", "comment"=>{"content"=>"dfdfd"}, "commit"=>"Update Comment", "message_id"=>"9", "id"=>"1"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.2ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
   (0.1ms)  begin transaction
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.1ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 6ms (ActiveRecord: 0.6ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:59:31 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [7 times] (2.3ms)
  Rendered comments/_form.html.erb (4.2ms)
  Rendered messages/show.html.erb within layouts/application (15.7ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 75ms (Views: 71.8ms | ActiveRecord: 0.7ms)


Started DELETE "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 18:59:40 +0800
Processing by CommentsController#destroy as HTML
  Parameters: {"authenticity_token"=>"Y2mUr+xX9t8YsPqR3E/V+0Q4BkW4B/cgiXYgO/r2gtgW0OUnza+6LAPhGh6ZnaMaaLPKGTQ+vEL4SrvJbG+oKA==", "message_id"=>"9", "id"=>"1"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
Completed 401 Unauthorized in 3ms (ActiveRecord: 0.2ms)



NoMethodError (undefined method `destory' for nil:NilClass):

app/controllers/comments_controller.rb:30:in `destroy'
Started DELETE "/messages/9/comments/2" for 127.0.0.1 at 2018-02-03 18:59:45 +0800
Processing by CommentsController#destroy as HTML
  Parameters: {"authenticity_token"=>"Y2mUr+xX9t8YsPqR3E/V+0Q4BkW4B/cgiXYgO/r2gtgW0OUnza+6LAPhGh6ZnaMaaLPKGTQ+vEL4SrvJbG+oKA==", "message_id"=>"9", "id"=>"2"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
Completed 401 Unauthorized in 3ms (ActiveRecord: 0.1ms)



NoMethodError (undefined method `destory' for nil:NilClass):

app/controllers/comments_controller.rb:30:in `destroy'
Started POST "/messages/9/comments" for 127.0.0.1 at 2018-02-03 18:59:57 +0800
Processing by CommentsController#create as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"Y2mUr+xX9t8YsPqR3E/V+0Q4BkW4B/cgiXYgO/r2gtgW0OUnza+6LAPhGh6ZnaMaaLPKGTQ+vEL4SrvJbG+oKA==", "comment"=>{"content"=>"gdgdfg"}, "commit"=>"Create Comment", "message_id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  CACHE Message Load (0.0ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
   (0.1ms)  begin transaction
   (0.1ms)  commit transaction
  User Load (0.9ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.1ms)  begin transaction
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  SQL (0.7ms)  INSERT INTO "comments" ("content", "message_id", "user_id", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?)  [["content", "gdgdfg"], ["message_id", 9], ["user_id", 2], ["created_at", "2018-02-03 10:59:57.020670"], ["updated_at", "2018-02-03 10:59:57.020670"]]
   (9.8ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 24ms (ActiveRecord: 11.9ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 18:59:57 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [8 times] (1.6ms)
  Rendered comments/_form.html.erb (2.9ms)
  Rendered messages/show.html.erb within layouts/application (10.1ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 80ms (Views: 76.6ms | ActiveRecord: 0.6ms)


Started GET "/messages/9/comments/8/edit" for 127.0.0.1 at 2018-02-03 19:00:00 +0800
Processing by CommentsController#edit as HTML
  Parameters: {"message_id"=>"9", "id"=>"8"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.3ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 8], ["LIMIT", 1]]
  Rendering comments/edit.html.erb within layouts/application
  Rendered comments/edit.html.erb within layouts/application (2.9ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 55ms (Views: 48.5ms | ActiveRecord: 0.7ms)


Started PATCH "/messages/9/comments/8" for 127.0.0.1 at 2018-02-03 19:00:01 +0800
Processing by CommentsController#update as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"pP/yZiWBuzbfH44nRnhZnCheB0qDBIH1VwTvyynSfbPhW1ssmj0QEjdJBjT2DTJLiGpt3i8ShhgRC1Ny+rRNpw==", "comment"=>{"content"=>"gdgdfg"}, "commit"=>"Update Comment", "message_id"=>"9", "id"=>"8"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.2ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 8], ["LIMIT", 1]]
   (0.0ms)  begin transaction
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.1ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 6ms (ActiveRecord: 0.6ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:00:01 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.1ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [8 times] (1.7ms)
  Rendered comments/_form.html.erb (3.5ms)
  Rendered messages/show.html.erb within layouts/application (11.3ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 74ms (Views: 71.6ms | ActiveRecord: 0.5ms)


Started DELETE "/messages/9/comments/8" for 127.0.0.1 at 2018-02-03 19:00:03 +0800
Processing by CommentsController#destroy as HTML
  Parameters: {"authenticity_token"=>"3tLZoUlVb+CZWGhlO59EE1TQMT+U7ZnlqENzmY+G6uyra6gpaK0jE4IJiOp+TTLyeFv9YxjU0ofZf+hrGR/AHA==", "message_id"=>"9", "id"=>"8"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
Completed 401 Unauthorized in 3ms (ActiveRecord: 0.1ms)



NoMethodError (undefined method `destory' for nil:NilClass):

app/controllers/comments_controller.rb:30:in `destroy'
Started GET "/messages" for 127.0.0.1 at 2018-02-03 19:01:06 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.6ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (11.0ms)
  User Load (0.5ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 86ms (Views: 78.3ms | ActiveRecord: 2.7ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:01:08 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [8 times] (2.0ms)
  Rendered comments/_form.html.erb (4.5ms)
  Rendered messages/show.html.erb within layouts/application (33.8ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 96ms (Views: 89.3ms | ActiveRecord: 1.2ms)


Started DELETE "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 19:01:10 +0800
Processing by CommentsController#destroy as HTML
  Parameters: {"authenticity_token"=>"tPs7mGfJcXjWKjivd9y4Ek8TtScaUJst8/9mNHKLxPnBQkoQRjE9i8172CAyDs7zY5h5e5Zp0E+Cw/3G5BLuCQ==", "message_id"=>"9", "id"=>"1"}
  Message Load (0.3ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
Completed 401 Unauthorized in 3ms (ActiveRecord: 0.3ms)



NoMethodError (undefined method `destory' for nil:NilClass):

app/controllers/comments_controller.rb:30:in `destroy'
Started GET "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 19:06:23 +0800

AbstractController::ActionNotFound (The action 'show' could not be found for CommentsController):

actionpack (5.1.4) lib/abstract_controller/base.rb:119:in `process'
actionview (5.1.4) lib/action_view/rendering.rb:30:in `process'
actionpack (5.1.4) lib/action_controller/metal.rb:189:in `dispatch'
actionpack (5.1.4) lib/action_controller/metal.rb:253:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:49:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:31:in `serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:50:in `block in serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `each'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `serve'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:834:in `call'
warden (1.2.7) lib/warden/manager.rb:36:in `block in call'
warden (1.2.7) lib/warden/manager.rb:35:in `catch'
warden (1.2.7) lib/warden/manager.rb:35:in `call'
rack (2.0.4) lib/rack/etag.rb:25:in `call'
rack (2.0.4) lib/rack/conditional_get.rb:25:in `call'
rack (2.0.4) lib/rack/head.rb:12:in `call'
rack (2.0.4) lib/rack/session/abstract/id.rb:232:in `context'
rack (2.0.4) lib/rack/session/abstract/id.rb:226:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/cookies.rb:613:in `call'
activerecord (5.1.4) lib/active_record/migration.rb:556:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:26:in `block in call'
activesupport (5.1.4) lib/active_support/callbacks.rb:97:in `run_callbacks'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:24:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/debug_exceptions.rb:59:in `call'
web-console (3.5.1) lib/web_console/middleware.rb:135:in `call_app'
web-console (3.5.1) lib/web_console/middleware.rb:28:in `block in call'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `catch'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/show_exceptions.rb:31:in `call'
railties (5.1.4) lib/rails/rack/logger.rb:36:in `call_app'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `block in call'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `block in tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:26:in `tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `tagged'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `call'
sprockets-rails (3.2.1) lib/sprockets/rails/quiet_assets.rb:13:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/remote_ip.rb:79:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/request_id.rb:25:in `call'
rack (2.0.4) lib/rack/method_override.rb:22:in `call'
rack (2.0.4) lib/rack/runtime.rb:22:in `call'
activesupport (5.1.4) lib/active_support/cache/strategy/local_cache_middleware.rb:27:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/static.rb:125:in `call'
rack (2.0.4) lib/rack/sendfile.rb:111:in `call'
railties (5.1.4) lib/rails/engine.rb:522:in `call'
puma (3.11.2) lib/puma/configuration.rb:225:in `call'
puma (3.11.2) lib/puma/server.rb:624:in `handle_request'
puma (3.11.2) lib/puma/server.rb:438:in `process_client'
puma (3.11.2) lib/puma/server.rb:302:in `block in run'
puma (3.11.2) lib/puma/thread_pool.rb:120:in `block in spawn_thread'
Started GET "/messages/" for 127.0.0.1 at 2018-02-03 19:06:27 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.3ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (2.7ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 72ms (Views: 64.8ms | ActiveRecord: 0.6ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:06:29 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.3ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [8 times] (3.5ms)
  Rendered comments/_form.html.erb (3.2ms)
  Rendered messages/show.html.erb within layouts/application (14.2ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 68ms (Views: 61.4ms | ActiveRecord: 0.7ms)


Started DELETE "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 19:06:31 +0800
Processing by CommentsController#destroy as HTML
  Parameters: {"authenticity_token"=>"Q0Ch498TCH+IB12PDbRgtM304N6YER5S/TfAnydM7102+dBr/utEjJNWvQBIZhZV4X8sghQoVTCMC1ttsdXFrQ==", "message_id"=>"9", "id"=>"1"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
Completed 401 Unauthorized in 3ms (ActiveRecord: 0.1ms)



NoMethodError (undefined method `destory' for nil:NilClass):

app/controllers/comments_controller.rb:30:in `destroy'
Started GET "/messages/9/comments/1/edit" for 127.0.0.1 at 2018-02-03 19:06:41 +0800
Processing by CommentsController#edit as HTML
  Parameters: {"message_id"=>"9", "id"=>"1"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.3ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
  Rendering comments/edit.html.erb within layouts/application
  Rendered comments/edit.html.erb within layouts/application (2.9ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 57ms (Views: 50.3ms | ActiveRecord: 0.7ms)


Started PATCH "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 19:06:42 +0800
Processing by CommentsController#update as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"kXNxcYPsMng4rqagOGcxRjIZfU9yFs2ptBpbfpG2ICjyZssARDd/3nEwSse0WCC2eXF5JFl1pn/WzTCGBRVplg==", "comment"=>{"content"=>"dfdfd"}, "commit"=>"Update Comment", "message_id"=>"9", "id"=>"1"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.2ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
   (0.1ms)  begin transaction
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.1ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 9ms (ActiveRecord: 0.8ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:06:42 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.1ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [8 times] (1.8ms)
  Rendered comments/_form.html.erb (5.7ms)
  Rendered messages/show.html.erb within layouts/application (13.9ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 72ms (Views: 69.3ms | ActiveRecord: 0.5ms)


Started GET "/messages/9/comments/1/edit" for 127.0.0.1 at 2018-02-03 19:06:43 +0800
Processing by CommentsController#edit as HTML
  Parameters: {"message_id"=>"9", "id"=>"1"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.2ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
  Rendering comments/edit.html.erb within layouts/application
  Rendered comments/edit.html.erb within layouts/application (5.2ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 70ms (Views: 61.5ms | ActiveRecord: 0.6ms)


Started PATCH "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 19:06:46 +0800
Processing by CommentsController#update as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"LXLvlvJpOQauw+y8b0XGpQC8BpC5VtDZFfMJu5YKILxOZ1XnNbJ0oOddANvjetdVS9QC+5I1uw93JGJDAqlpAg==", "comment"=>{"content"=>"dfdfdstrtst"}, "commit"=>"Update Comment", "message_id"=>"9", "id"=>"1"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.2ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
   (0.0ms)  begin transaction
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  SQL (0.3ms)  UPDATE "comments" SET "content" = ?, "updated_at" = ? WHERE "comments"."id" = ?  [["content", "dfdfdstrtst"], ["updated_at", "2018-02-03 11:06:46.384786"], ["id", 1]]
   (0.9ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 7ms (ActiveRecord: 1.7ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:06:46 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.3ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.1ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [8 times] (3.3ms)
  Rendered comments/_form.html.erb (3.8ms)
  Rendered messages/show.html.erb within layouts/application (13.4ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 77ms (Views: 73.0ms | ActiveRecord: 0.7ms)


Started GET "/" for 127.0.0.1 at 2018-02-03 19:07:25 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.3ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (2.1ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 57ms (Views: 52.4ms | ActiveRecord: 0.5ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:07:28 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.5ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [8 times] (2.2ms)
  Rendered comments/_form.html.erb (4.0ms)
  Rendered messages/show.html.erb within layouts/application (20.3ms)
  User Load (0.4ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 96ms (Views: 88.3ms | ActiveRecord: 1.0ms)


Started GET "/messages/9/comments/1/edit" for 127.0.0.1 at 2018-02-03 19:07:33 +0800
Processing by CommentsController#edit as HTML
  Parameters: {"message_id"=>"9", "id"=>"1"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.2ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
  Rendering comments/edit.html.erb within layouts/application
  Rendered comments/edit.html.erb within layouts/application (3.0ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 72ms (Views: 64.5ms | ActiveRecord: 0.7ms)


Started PATCH "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 19:07:34 +0800
Processing by CommentsController#update as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"kiFBabpRQnftI1nLKj90bm6M+mfzBcHhNWbASbawJMTxNPsYfYoP0aS9taymAGWeJeT+DNhmqjdXsauxIhNteg==", "comment"=>{"content"=>"dfdfdstrtst"}, "commit"=>"Update Comment", "message_id"=>"9", "id"=>"1"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.3ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
   (0.1ms)  begin transaction
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.1ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 6ms (ActiveRecord: 0.7ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:07:34 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [8 times] (1.9ms)
  Rendered comments/_form.html.erb (3.5ms)
  Rendered messages/show.html.erb within layouts/application (11.8ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 64ms (Views: 60.8ms | ActiveRecord: 0.5ms)


Started GET "/messages/9/comments/1/edit" for 127.0.0.1 at 2018-02-03 19:07:37 +0800
Processing by CommentsController#edit as HTML
  Parameters: {"message_id"=>"9", "id"=>"1"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.3ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
  Rendering comments/edit.html.erb within layouts/application
  Rendered comments/edit.html.erb within layouts/application (2.9ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 61ms (Views: 52.8ms | ActiveRecord: 0.6ms)


Started PATCH "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 19:07:39 +0800
Processing by CommentsController#update as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"u7k+j/k0P1ByEyH8zgcXymRk38om/uOqvsBe8eeOpd3YrIT+Pu9y9juNzZtCOAY6LwzboQ2diHzcFzUJcy3sYw==", "comment"=>{"content"=>"dfdfdstrtst"}, "commit"=>"Update Comment", "message_id"=>"9", "id"=>"1"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.2ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
   (0.1ms)  begin transaction
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.1ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 6ms (ActiveRecord: 0.6ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:07:39 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [8 times] (1.5ms)
  Rendered comments/_form.html.erb (3.5ms)
  Rendered messages/show.html.erb within layouts/application (11.4ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 65ms (Views: 62.5ms | ActiveRecord: 0.5ms)


Started DELETE "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 19:08:11 +0800
Processing by CommentsController#destroy as HTML
  Parameters: {"authenticity_token"=>"Wjw2SOjWtvCfqIy4xHsxTEpdFtAUiLIZJ+FEJquBGDkvhUfAyS76A4T5bDeBqUetZtbajJix+XtW3d/UPRgyyQ==", "message_id"=>"9", "id"=>"1"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
Completed 401 Unauthorized in 17ms (ActiveRecord: 1.2ms)



NoMethodError (undefined method `destory' for nil:NilClass):

app/controllers/comments_controller.rb:30:in `destroy'
Started GET "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 19:08:42 +0800

AbstractController::ActionNotFound (The action 'show' could not be found for CommentsController):

actionpack (5.1.4) lib/abstract_controller/base.rb:119:in `process'
actionview (5.1.4) lib/action_view/rendering.rb:30:in `process'
actionpack (5.1.4) lib/action_controller/metal.rb:189:in `dispatch'
actionpack (5.1.4) lib/action_controller/metal.rb:253:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:49:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:31:in `serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:50:in `block in serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `each'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `serve'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:834:in `call'
warden (1.2.7) lib/warden/manager.rb:36:in `block in call'
warden (1.2.7) lib/warden/manager.rb:35:in `catch'
warden (1.2.7) lib/warden/manager.rb:35:in `call'
rack (2.0.4) lib/rack/etag.rb:25:in `call'
rack (2.0.4) lib/rack/conditional_get.rb:25:in `call'
rack (2.0.4) lib/rack/head.rb:12:in `call'
rack (2.0.4) lib/rack/session/abstract/id.rb:232:in `context'
rack (2.0.4) lib/rack/session/abstract/id.rb:226:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/cookies.rb:613:in `call'
activerecord (5.1.4) lib/active_record/migration.rb:556:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:26:in `block in call'
activesupport (5.1.4) lib/active_support/callbacks.rb:97:in `run_callbacks'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:24:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/debug_exceptions.rb:59:in `call'
web-console (3.5.1) lib/web_console/middleware.rb:135:in `call_app'
web-console (3.5.1) lib/web_console/middleware.rb:28:in `block in call'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `catch'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/show_exceptions.rb:31:in `call'
railties (5.1.4) lib/rails/rack/logger.rb:36:in `call_app'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `block in call'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `block in tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:26:in `tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `tagged'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `call'
sprockets-rails (3.2.1) lib/sprockets/rails/quiet_assets.rb:13:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/remote_ip.rb:79:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/request_id.rb:25:in `call'
rack (2.0.4) lib/rack/method_override.rb:22:in `call'
rack (2.0.4) lib/rack/runtime.rb:22:in `call'
activesupport (5.1.4) lib/active_support/cache/strategy/local_cache_middleware.rb:27:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/static.rb:125:in `call'
rack (2.0.4) lib/rack/sendfile.rb:111:in `call'
railties (5.1.4) lib/rails/engine.rb:522:in `call'
puma (3.11.2) lib/puma/configuration.rb:225:in `call'
puma (3.11.2) lib/puma/server.rb:624:in `handle_request'
puma (3.11.2) lib/puma/server.rb:438:in `process_client'
puma (3.11.2) lib/puma/server.rb:302:in `block in run'
puma (3.11.2) lib/puma/thread_pool.rb:120:in `block in spawn_thread'
Started GET "/messages/" for 127.0.0.1 at 2018-02-03 19:08:45 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.2ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (12.0ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 91ms (Views: 85.1ms | ActiveRecord: 2.2ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:08:47 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.3ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [8 times] (1.8ms)
  Rendered comments/_form.html.erb (5.2ms)
  Rendered messages/show.html.erb within layouts/application (25.9ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 83ms (Views: 75.8ms | ActiveRecord: 1.3ms)


Started DELETE "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 19:08:49 +0800
Processing by CommentsController#destroy as HTML
  Parameters: {"authenticity_token"=>"wLZwUq/y4S9A1Cjg2JIguRbkWpgHbKZ9EYSMa+arB2W1DwHajgqt3FuFyG+dQFZYOm+WxItV7R9guBeZcDItlQ==", "message_id"=>"9", "id"=>"1"}
  Message Load (0.5ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
Completed 401 Unauthorized in 9ms (ActiveRecord: 0.5ms)



NoMethodError (undefined method `destory' for nil:NilClass):

app/controllers/comments_controller.rb:30:in `destroy'
Started GET "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 19:12:35 +0800

AbstractController::ActionNotFound (The action 'show' could not be found for CommentsController):

actionpack (5.1.4) lib/abstract_controller/base.rb:119:in `process'
actionview (5.1.4) lib/action_view/rendering.rb:30:in `process'
actionpack (5.1.4) lib/action_controller/metal.rb:189:in `dispatch'
actionpack (5.1.4) lib/action_controller/metal.rb:253:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:49:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:31:in `serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:50:in `block in serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `each'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `serve'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:834:in `call'
warden (1.2.7) lib/warden/manager.rb:36:in `block in call'
warden (1.2.7) lib/warden/manager.rb:35:in `catch'
warden (1.2.7) lib/warden/manager.rb:35:in `call'
rack (2.0.4) lib/rack/etag.rb:25:in `call'
rack (2.0.4) lib/rack/conditional_get.rb:25:in `call'
rack (2.0.4) lib/rack/head.rb:12:in `call'
rack (2.0.4) lib/rack/session/abstract/id.rb:232:in `context'
rack (2.0.4) lib/rack/session/abstract/id.rb:226:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/cookies.rb:613:in `call'
activerecord (5.1.4) lib/active_record/migration.rb:556:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:26:in `block in call'
activesupport (5.1.4) lib/active_support/callbacks.rb:97:in `run_callbacks'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:24:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/debug_exceptions.rb:59:in `call'
web-console (3.5.1) lib/web_console/middleware.rb:135:in `call_app'
web-console (3.5.1) lib/web_console/middleware.rb:28:in `block in call'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `catch'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/show_exceptions.rb:31:in `call'
railties (5.1.4) lib/rails/rack/logger.rb:36:in `call_app'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `block in call'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `block in tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:26:in `tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `tagged'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `call'
sprockets-rails (3.2.1) lib/sprockets/rails/quiet_assets.rb:13:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/remote_ip.rb:79:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/request_id.rb:25:in `call'
rack (2.0.4) lib/rack/method_override.rb:22:in `call'
rack (2.0.4) lib/rack/runtime.rb:22:in `call'
activesupport (5.1.4) lib/active_support/cache/strategy/local_cache_middleware.rb:27:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/static.rb:125:in `call'
rack (2.0.4) lib/rack/sendfile.rb:111:in `call'
railties (5.1.4) lib/rails/engine.rb:522:in `call'
puma (3.11.2) lib/puma/configuration.rb:225:in `call'
puma (3.11.2) lib/puma/server.rb:624:in `handle_request'
puma (3.11.2) lib/puma/server.rb:438:in `process_client'
puma (3.11.2) lib/puma/server.rb:302:in `block in run'
puma (3.11.2) lib/puma/thread_pool.rb:120:in `block in spawn_thread'
Started GET "/messages/9/" for 127.0.0.1 at 2018-02-03 19:12:39 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.4ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [8 times] (2.6ms)
  Rendered comments/_form.html.erb (3.9ms)
  Rendered messages/show.html.erb within layouts/application (34.2ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 140ms (Views: 124.7ms | ActiveRecord: 3.5ms)


Started DELETE "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 19:12:41 +0800
Processing by CommentsController#destroy as HTML
  Parameters: {"authenticity_token"=>"4VNjScUsGENNpcM1iFtjWiUGR1AOm0WKVJzSistvuNmU6hLB5NRUsFb0I7rNiRW7CY2LDIKiDugloEl4XfaSKQ==", "message_id"=>"9", "id"=>"1"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
Completed 401 Unauthorized in 6ms (ActiveRecord: 0.1ms)



NoMethodError (undefined method `destory' for nil:NilClass):

app/controllers/comments_controller.rb:30:in `destroy'
Started GET "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 19:13:20 +0800

AbstractController::ActionNotFound (The action 'show' could not be found for CommentsController):

actionpack (5.1.4) lib/abstract_controller/base.rb:119:in `process'
actionview (5.1.4) lib/action_view/rendering.rb:30:in `process'
actionpack (5.1.4) lib/action_controller/metal.rb:189:in `dispatch'
actionpack (5.1.4) lib/action_controller/metal.rb:253:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:49:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:31:in `serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:50:in `block in serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `each'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `serve'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:834:in `call'
warden (1.2.7) lib/warden/manager.rb:36:in `block in call'
warden (1.2.7) lib/warden/manager.rb:35:in `catch'
warden (1.2.7) lib/warden/manager.rb:35:in `call'
rack (2.0.4) lib/rack/etag.rb:25:in `call'
rack (2.0.4) lib/rack/conditional_get.rb:25:in `call'
rack (2.0.4) lib/rack/head.rb:12:in `call'
rack (2.0.4) lib/rack/session/abstract/id.rb:232:in `context'
rack (2.0.4) lib/rack/session/abstract/id.rb:226:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/cookies.rb:613:in `call'
activerecord (5.1.4) lib/active_record/migration.rb:556:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:26:in `block in call'
activesupport (5.1.4) lib/active_support/callbacks.rb:97:in `run_callbacks'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:24:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/debug_exceptions.rb:59:in `call'
web-console (3.5.1) lib/web_console/middleware.rb:135:in `call_app'
web-console (3.5.1) lib/web_console/middleware.rb:28:in `block in call'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `catch'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/show_exceptions.rb:31:in `call'
railties (5.1.4) lib/rails/rack/logger.rb:36:in `call_app'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `block in call'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `block in tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:26:in `tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `tagged'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `call'
sprockets-rails (3.2.1) lib/sprockets/rails/quiet_assets.rb:13:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/remote_ip.rb:79:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/request_id.rb:25:in `call'
rack (2.0.4) lib/rack/method_override.rb:22:in `call'
rack (2.0.4) lib/rack/runtime.rb:22:in `call'
activesupport (5.1.4) lib/active_support/cache/strategy/local_cache_middleware.rb:27:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/static.rb:125:in `call'
rack (2.0.4) lib/rack/sendfile.rb:111:in `call'
railties (5.1.4) lib/rails/engine.rb:522:in `call'
puma (3.11.2) lib/puma/configuration.rb:225:in `call'
puma (3.11.2) lib/puma/server.rb:624:in `handle_request'
puma (3.11.2) lib/puma/server.rb:438:in `process_client'
puma (3.11.2) lib/puma/server.rb:302:in `block in run'
puma (3.11.2) lib/puma/thread_pool.rb:120:in `block in spawn_thread'
Started GET "/messages" for 127.0.0.1 at 2018-02-03 19:13:24 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.2ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (9.5ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 94ms (Views: 89.5ms | ActiveRecord: 2.3ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:13:25 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.6ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [8 times] (2.0ms)
  Rendered comments/_form.html.erb (5.3ms)
  Rendered messages/show.html.erb within layouts/application (29.8ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 93ms (Views: 84.9ms | ActiveRecord: 1.6ms)


Started DELETE "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 19:13:27 +0800
Processing by CommentsController#destroy as HTML
  Parameters: {"authenticity_token"=>"sEMEy/Le28MX3tQ1lATciTgc3Fly+/7730O2D5UvZ77F+nVD0yaXMAyPNLrR1qpoFJcQBf7CtZmufy39A7ZNTg==", "message_id"=>"9", "id"=>"1"}
  Message Load (0.7ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
Completed 401 Unauthorized in 10ms (ActiveRecord: 0.7ms)



NoMethodError (undefined method `destroy' for nil:NilClass):

app/controllers/comments_controller.rb:30:in `destroy'
Started GET "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 19:14:31 +0800

AbstractController::ActionNotFound (The action 'show' could not be found for CommentsController):

actionpack (5.1.4) lib/abstract_controller/base.rb:119:in `process'
actionview (5.1.4) lib/action_view/rendering.rb:30:in `process'
actionpack (5.1.4) lib/action_controller/metal.rb:189:in `dispatch'
actionpack (5.1.4) lib/action_controller/metal.rb:253:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:49:in `dispatch'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:31:in `serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:50:in `block in serve'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `each'
actionpack (5.1.4) lib/action_dispatch/journey/router.rb:33:in `serve'
actionpack (5.1.4) lib/action_dispatch/routing/route_set.rb:834:in `call'
warden (1.2.7) lib/warden/manager.rb:36:in `block in call'
warden (1.2.7) lib/warden/manager.rb:35:in `catch'
warden (1.2.7) lib/warden/manager.rb:35:in `call'
rack (2.0.4) lib/rack/etag.rb:25:in `call'
rack (2.0.4) lib/rack/conditional_get.rb:25:in `call'
rack (2.0.4) lib/rack/head.rb:12:in `call'
rack (2.0.4) lib/rack/session/abstract/id.rb:232:in `context'
rack (2.0.4) lib/rack/session/abstract/id.rb:226:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/cookies.rb:613:in `call'
activerecord (5.1.4) lib/active_record/migration.rb:556:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:26:in `block in call'
activesupport (5.1.4) lib/active_support/callbacks.rb:97:in `run_callbacks'
actionpack (5.1.4) lib/action_dispatch/middleware/callbacks.rb:24:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/debug_exceptions.rb:59:in `call'
web-console (3.5.1) lib/web_console/middleware.rb:135:in `call_app'
web-console (3.5.1) lib/web_console/middleware.rb:28:in `block in call'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `catch'
web-console (3.5.1) lib/web_console/middleware.rb:18:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/show_exceptions.rb:31:in `call'
railties (5.1.4) lib/rails/rack/logger.rb:36:in `call_app'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `block in call'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `block in tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:26:in `tagged'
activesupport (5.1.4) lib/active_support/tagged_logging.rb:69:in `tagged'
railties (5.1.4) lib/rails/rack/logger.rb:24:in `call'
sprockets-rails (3.2.1) lib/sprockets/rails/quiet_assets.rb:13:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/remote_ip.rb:79:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/request_id.rb:25:in `call'
rack (2.0.4) lib/rack/method_override.rb:22:in `call'
rack (2.0.4) lib/rack/runtime.rb:22:in `call'
activesupport (5.1.4) lib/active_support/cache/strategy/local_cache_middleware.rb:27:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/executor.rb:12:in `call'
actionpack (5.1.4) lib/action_dispatch/middleware/static.rb:125:in `call'
rack (2.0.4) lib/rack/sendfile.rb:111:in `call'
railties (5.1.4) lib/rails/engine.rb:522:in `call'
puma (3.11.2) lib/puma/configuration.rb:225:in `call'
puma (3.11.2) lib/puma/server.rb:624:in `handle_request'
puma (3.11.2) lib/puma/server.rb:438:in `process_client'
puma (3.11.2) lib/puma/server.rb:302:in `block in run'
puma (3.11.2) lib/puma/thread_pool.rb:120:in `block in spawn_thread'
Started GET "/messages/9/" for 127.0.0.1 at 2018-02-03 19:14:34 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.3ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [8 times] (2.1ms)
  Rendered comments/_form.html.erb (4.2ms)
  Rendered messages/show.html.erb within layouts/application (38.0ms)
  User Load (0.4ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 122ms (Views: 105.3ms | ActiveRecord: 3.5ms)


Started DELETE "/messages/9/comments/1" for 127.0.0.1 at 2018-02-03 19:14:36 +0800
Processing by CommentsController#destroy as HTML
  Parameters: {"authenticity_token"=>"imGSuxzfs2yRyMbFwXZmvwilanGIC8a7zigwYKjsJC7/2OMzPSf/n4qZJkqEpBBeJC6mLQQyjdm/FKuSPnUO3g==", "message_id"=>"9", "id"=>"1"}
  Message Load (0.7ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.7ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 1], ["LIMIT", 1]]
   (0.1ms)  begin transaction
  SQL (0.5ms)  DELETE FROM "comments" WHERE "comments"."id" = ?  [["id", 1]]
   (8.9ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 21ms (ActiveRecord: 11.0ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:14:36 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [7 times] (1.4ms)
  Rendered comments/_form.html.erb (5.9ms)
  Rendered messages/show.html.erb within layouts/application (14.2ms)
  User Load (0.4ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 69ms (Views: 65.4ms | ActiveRecord: 0.7ms)


Started DELETE "/messages/9/comments/2" for 127.0.0.1 at 2018-02-03 19:14:39 +0800
Processing by CommentsController#destroy as HTML
  Parameters: {"authenticity_token"=>"KeJqooj9724TJPPGzr+Wf98R4WtgNYNVb21QLfjzEQ9cWxsqqQWjnQh1E0mLbeCe85otN+wMyDceUcvfbmo7/w==", "message_id"=>"9", "id"=>"2"}
  Message Load (1.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Comment Load (0.5ms)  SELECT  "comments".* FROM "comments" WHERE "comments"."message_id" = ? AND "comments"."id" = ? LIMIT ?  [["message_id", 9], ["id", 2], ["LIMIT", 1]]
   (0.1ms)  begin transaction
  SQL (0.4ms)  DELETE FROM "comments" WHERE "comments"."id" = ?  [["id", 2]]
   (9.1ms)  commit transaction
Redirected to http://localhost:3000/messages/9
Completed 302 Found in 24ms (ActiveRecord: 11.3ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:14:39 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Comment Load (0.1ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [6 times] (1.8ms)
  Rendered comments/_form.html.erb (4.1ms)
  Rendered messages/show.html.erb within layouts/application (11.7ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 79ms (Views: 75.6ms | ActiveRecord: 0.6ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:18:12 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  Rendered messages/show.html.erb within layouts/application (174.0ms)
Completed 401 Unauthorized in 184ms (ActiveRecord: 0.3ms)



ActionView::Template::Error (undefined method `time_agp_in_words' for #<#<Class:0x007fbcb1943f70>:0x007fbcadde9128>
Did you mean?  time_ago_in_words):
    1: <h2><%= @message.title %></h2>
    2: <p>posted by <%= @message.user.email %><%= time_agp_in_words(@message.created_at) %>ago</p>
    3: <p><%= @message.description %></p>
    4: <!-- add a poster at timestamp -->
    5:

app/views/messages/show.html.erb:2:in `_app_views_messages_show_html_erb__142144221223395500_70224173813140'
Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:18:43 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.4ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  Comment Load (0.1ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [6 times] (1.3ms)
  Rendered comments/_form.html.erb (2.7ms)
  Rendered messages/show.html.erb within layouts/application (12.5ms)
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 74ms (Views: 69.0ms | ActiveRecord: 0.9ms)


Started GET "/" for 127.0.0.1 at 2018-02-03 19:19:06 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.2ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (1.9ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 61ms (Views: 55.4ms | ActiveRecord: 0.5ms)


Started GET "/messages/8" for 127.0.0.1 at 2018-02-03 19:19:07 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"8"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 8], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  Comment Load (0.1ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 8]]
  Rendered collection of templates [0 times] (0.0ms)
  Rendered comments/_form.html.erb (3.3ms)
  Rendered messages/show.html.erb within layouts/application (8.9ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 83ms (Views: 76.9ms | ActiveRecord: 0.7ms)


Started POST "/messages/8/comments" for 127.0.0.1 at 2018-02-03 19:19:11 +0800
Processing by CommentsController#create as HTML
  Parameters: {"utf8"=>"✓", "authenticity_token"=>"ybMs23HS4ySh9esklfHdDL9txFsNJg9Qg71DDTwyZzO42BVE2qvmeuI2XX7ZU8CnY8argpuW94/Y7SyFL3zibA==", "comment"=>{"content"=>"dfdfd"}, "commit"=>"Create Comment", "message_id"=>"8"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 8], ["LIMIT", 1]]
   (0.1ms)  begin transaction
   (0.2ms)  commit transaction
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
   (0.0ms)  begin transaction
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  SQL (0.5ms)  INSERT INTO "comments" ("content", "message_id", "user_id", "created_at", "updated_at") VALUES (?, ?, ?, ?, ?)  [["content", "dfdfd"], ["message_id", 8], ["user_id", 2], ["created_at", "2018-02-03 11:19:11.161732"], ["updated_at", "2018-02-03 11:19:11.161732"]]
   (9.0ms)  commit transaction
Redirected to http://localhost:3000/messages/8
Completed 302 Found in 25ms (ActiveRecord: 10.3ms)


Started GET "/messages/8" for 127.0.0.1 at 2018-02-03 19:19:11 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"8"}
  Message Load (0.4ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 8], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  User Load (0.1ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 8]]
  Rendered collection of comments/_comment.html.erb [1 times] (1.5ms)
  Rendered comments/_form.html.erb (13.7ms)
  Rendered messages/show.html.erb within layouts/application (29.1ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 117ms (Views: 110.8ms | ActiveRecord: 1.0ms)


Started GET "/messages/8" for 127.0.0.1 at 2018-02-03 19:20:49 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"8"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 8], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 1], ["LIMIT", 1]]
  Comment Load (0.1ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 8]]
  Rendered collection of comments/_comment.html.erb [1 times] (3.6ms)
  Rendered messages/show.html.erb within layouts/application (9.4ms)
Completed 401 Unauthorized in 19ms (ActiveRecord: 0.5ms)



ActionView::Template::Error (undefined method `user' for nil:NilClass):
    1: <p><%= comment.content %></p>
    2: <p>posted by <%= @comment.user.email %><%= time_ago_in_words(@comment.created_at) %>ago</p>
    3: <%= link_to "edit", edit_message_comment_path(comment.message,comment) %>
    4: <%= link_to "Delete", message_comment_path(comment.message, comment), method: :delete, data: { confirm: "Are you sure?" }, class: "btn btn-default" %>

app/views/comments/_comment.html.erb:2:in `_app_views_comments__comment_html_erb___1766926366244662605_70224183971640'
app/views/messages/show.html.erb:8:in `_app_views_messages_show_html_erb__142144221223395500_70224173025640'
Started GET "/" for 127.0.0.1 at 2018-02-03 19:21:11 +0800
Processing by MessagesController#index as HTML
  Rendering messages/index.html.erb within layouts/application
  Message Load (0.2ms)  SELECT "messages".* FROM "messages" ORDER BY created_at DESC
  Rendered messages/index.html.erb within layouts/application (2.3ms)
  User Load (0.3ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 82ms (Views: 78.6ms | ActiveRecord: 0.5ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:21:13 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.2ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  Rendered collection of comments/_comment.html.erb [6 times] (4.0ms)
  Rendered messages/show.html.erb within layouts/application (13.0ms)
Completed 401 Unauthorized in 24ms (ActiveRecord: 0.5ms)



ActionView::Template::Error (undefined method `user' for nil:NilClass):
    1: <p><%= comment.content %></p>
    2: <p>posted by <%= @comment.user.email %><%= time_ago_in_words(@comment.created_at) %>ago</p>
    3: <%= link_to "edit", edit_message_comment_path(comment.message,comment) %>
    4: <%= link_to "Delete", message_comment_path(comment.message, comment), method: :delete, data: { confirm: "Are you sure?" }, class: "btn btn-default" %>

app/views/comments/_comment.html.erb:2:in `_app_views_comments__comment_html_erb___1766926366244662605_70224127394860'
app/views/messages/show.html.erb:8:in `_app_views_messages_show_html_erb__142144221223395500_70224130882380'
Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:21:47 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  Comment Load (0.2ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  CACHE User Load (0.0ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  Rendered collection of comments/_comment.html.erb [6 times] (10.7ms)
  Rendered messages/show.html.erb within layouts/application (21.7ms)
Completed 401 Unauthorized in 30ms (ActiveRecord: 0.5ms)



ActionView::Template::Error (undefined method `created_at' for nil:NilClass):
    1: <p><%= comment.content %></p>
    2: <p>posted by <%= comment.user.email %><%= time_ago_in_words(@comment.created_at) %>ago</p>
    3: <%= link_to "edit", edit_message_comment_path(comment.message,comment) %>
    4: <%= link_to "Delete", message_comment_path(comment.message, comment), method: :delete, data: { confirm: "Are you sure?" }, class: "btn btn-default" %>

app/views/comments/_comment.html.erb:2:in `_app_views_comments__comment_html_erb___1766926366244662605_70224186674600'
app/views/messages/show.html.erb:8:in `_app_views_messages_show_html_erb__142144221223395500_70224203797280'
Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:22:02 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  User Load (0.2ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  Comment Load (0.1ms)  SELECT "comments".* FROM "comments" WHERE "comments"."message_id" = ?  [["message_id", 9]]
  CACHE User Load (0.0ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  CACHE User Load (0.0ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  CACHE User Load (0.0ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  CACHE User Load (0.0ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  CACHE User Load (0.0ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  CACHE User Load (0.0ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? LIMIT ?  [["id", 2], ["LIMIT", 1]]
  Rendered collection of comments/_comment.html.erb [6 times] (8.2ms)
  Rendered comments/_form.html.erb (5.1ms)
  Rendered messages/show.html.erb within layouts/application (21.2ms)
  User Load (0.4ms)  SELECT  "users".* FROM "users" WHERE "users"."id" = ? ORDER BY "users"."id" ASC LIMIT ?  [["id", 2], ["LIMIT", 1]]
Completed 200 OK in 84ms (Views: 80.6ms | ActiveRecord: 0.9ms)


Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:24:13 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Rendered messages/show.html.erb within layouts/application (0.8ms)
Completed 401 Unauthorized in 10ms (ActiveRecord: 0.1ms)



SyntaxError (/Users/xiaowei/messageapp/app/views/messages/show.html.erb:20: syntax error, unexpected keyword_end
'.freeze;@output_buffer.append=( end );
                                    ^
/Users/xiaowei/messageapp/app/views/messages/show.html.erb:23: syntax error, unexpected keyword_ensure, expecting ')'
/Users/xiaowei/messageapp/app/views/messages/show.html.erb:25: syntax error, unexpected keyword_end, expecting ')'):

app/views/messages/show.html.erb:20: syntax error, unexpected keyword_end
app/views/messages/show.html.erb:23: syntax error, unexpected keyword_ensure, expecting ')'
app/views/messages/show.html.erb:25: syntax error, unexpected keyword_end, expecting ')'
Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:26:03 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Rendered messages/show.html.erb within layouts/application (1.0ms)
Completed 401 Unauthorized in 9ms (ActiveRecord: 0.1ms)



SyntaxError (/Users/xiaowei/messageapp/app/views/messages/show.html.erb:16: syntax error, unexpected ')', expecting keyword_then or ';' or '\n'
...r.append=( if user_signed_in? );@output_buffer.safe_append='
...                               ^
/Users/xiaowei/messageapp/app/views/messages/show.html.erb:21: syntax error, unexpected keyword_end
'.freeze;@output_buffer.append=( end );@output_buffer.safe_append='
                                    ^
/Users/xiaowei/messageapp/app/views/messages/show.html.erb:22: syntax error, unexpected keyword_end
'.freeze;@output_buffer.append=( end );
                                    ^
/Users/xiaowei/messageapp/app/views/messages/show.html.erb:25: syntax error, unexpected keyword_ensure, expecting ')'
/Users/xiaowei/messageapp/app/views/messages/show.html.erb:27: syntax error, unexpected keyword_end, expecting ')'):

app/views/messages/show.html.erb:16: syntax error, unexpected ')', expecting keyword_then or ';' or '\n'
app/views/messages/show.html.erb:21: syntax error, unexpected keyword_end
app/views/messages/show.html.erb:22: syntax error, unexpected keyword_end
app/views/messages/show.html.erb:25: syntax error, unexpected keyword_ensure, expecting ')'
app/views/messages/show.html.erb:27: syntax error, unexpected keyword_end, expecting ')'
Started GET "/messages/9" for 127.0.0.1 at 2018-02-03 19:26:32 +0800
Processing by MessagesController#show as HTML
  Parameters: {"id"=>"9"}
  Message Load (0.1ms)  SELECT  "messages".* FROM "messages" WHERE "messages"."id" = ? LIMIT ?  [["id", 9], ["LIMIT", 1]]
  Rendering messages/show.html.erb within layouts/application
  Rendered messages/show.html.erb within layouts/application (1.0ms)
Completed 401 Unauthorized in 10ms (ActiveRecord: 0.1ms)



SyntaxError (/Users/xiaowei/messageapp/app/views/messages/show.html.erb:16: syntax error, unexpected ')', expecting keyword_then or ';' or '\n'
...r.append=( if user_signed_in? );@output_buffer.safe_append='
...                               ^
/Users/xiaowei/messageapp/app/views/messages/show.html.erb:17: syntax error, unexpected ')', expecting keyword_then or ';' or '\n'
...ge.user_id == current_user.id );@output_buffer.safe_append='
...                               ^
/Users/xiaowei/messageapp/app/views/messages/show.html.erb:21: syntax error, unexpected keyword_end
'.freeze;@output_buffer.append=( end );@output_buffer.safe_append='
                                    ^
/Users/xiaowei/messageapp/app/views/messages/show.html.erb:22: syntax error, unexpected keyword_end
'.freeze;@output_buffer.append=( end );
                                    ^
/Users/xiaowei/messageapp/app/views/messages/show.html.erb:25: syntax error, unexpected keyword_ensure, expecting ')'
/Users/xiaowei/messageapp/app/views/messages/show.html.erb:27: syntax error, unexpected keyword_end, expecting ')'):

app/views/messages/show.html.erb:16: syntax error, unexpected ')', expecting keyword_then or ';' or '\n'
app/views/messages/show.html.erb:17: syntax error, unexpected ')', expecting keyword_then or ';' or '\n'
app/views/messages/show.html.erb:21: syntax error, unexpected keyword_end
app/views/messages/show.html.erb:22: syntax error, unexpected keyword_end
app/views/messages/show.html.erb:25: syntax error, unexpected keyword_ensure, expecting ')'
app/views/messages/show.html.erb:27: syntax error, unexpected keyword_end, expecting ')'
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ master ⮀ git checkout -b xw-020303
 xiaowei@xiaoweideMacBook-Pro ⮀ ~/messageapp ⮀ ⭠ xw-020303 ⮀ rails generate model Comment:text message:references user:refenrences
Running via Spring preloader in process 1572
      invoke  active_record
/Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/railties-5.1.4/lib/rails/generators/base.rb:258:in `const_defined?': wrong constant name Comment:text (NameError)
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/railties-5.1.4/lib/rails/generators/base.rb:258:in `block in class_collisions'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/railties-5.1.4/lib/rails/generators/base.rb:249:in `each'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/railties-5.1.4/lib/rails/generators/base.rb:249:in `class_collisions'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/railties-5.1.4/lib/rails/generators/named_base.rb:236:in `block in check_class_collision'
	from /Users/xiaowei/.rvm/gems/ruby-2.3.1/gems/thor-0.20.0/lib/thor/com

```
  代码展示：

  message  show

  ```
  <h2><%= @message.title %></h2>
  <p>posted by <%= @message.user.email %><%= time_ago_in_words(@message.created_at) %>ago</p>
  <p><%= @message.description %></p>
  <!-- add a poster at timestamp -->


  <h3>Comments:</h3>
  <%= render @message.comments %>


  <h3>reply to message:</h3>
  <%= render 'comments/form' %>


  <%= link_to "back", messages_path(@message) %>
  <% if user_signed_in? %>
  <% if @message.user_id == current_user.id %>
  <%= link_to "edit", edit_message_path(@message) %>
  <%= link_to "delete", message_path(@message), method: :delete, date: { confirm: "are you sure?" } %>
  <%= link_to "Delete", message_path(@message), method: :delete, data: { confirm: "Are you sure?" }, class: "btn btn-default" %>
  <% end %>
  <% end %>

  ```

  comment

  _comment.html.erb
  ```
  <p><%= comment.content %></p>
  <p>posted by <%= comment.user.email %><%= time_ago_in_words(comment.created_at) %>ago</p>
  <% if user_signed_in? %>
  <% if comment.user_id == current_user.id %>
  <%= link_to "edit", edit_message_comment_path(comment.message,comment) %>
  <%= link_to "Delete", message_comment_path(comment.message, comment), method: :delete, data: { confirm: "Are you sure?" }, class: "btn btn-default" %>
  <% end %>
  <% end %>
  ```
  _form.html.erb

```
  <%= simple_form_for ([@message,@message.comments.build]) do |f| %>
  <%= f.input :content, label: "comment" %>
  <%= f.button :submit %>
  <% end %>
  ```
edit
```
<h1>edit comment</h1>
<%= simple_form_for([@message,@comment]) do |f| %>
<%= f.input :content, label: "comment" %>
<%= f.button :submit %>
<% end %>
```

最后一部分：完成页面的美化：
添加scss代码

https://github.com/RyanHemrick/Message-Board/blob/master/app/assets/stylesheets/application.css.scss

```



```
