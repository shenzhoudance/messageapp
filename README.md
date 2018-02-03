
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
