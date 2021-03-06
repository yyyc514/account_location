Account Location
================

Account location is a set of protected methods that supports the account-key-as-subdomain 
way of identifying the current scope. These methods allow you to easily produce URLs that
match this style and to get the current account key from the subdomain.

The methods are: `account_url`, `account_host`, and `account_domain`.

current_account can be used to get and set the currently active account.


Example:

    class ApplicationController < ActionController::Base
      include AccountLocation
      before_filter :find_account
    
      protected
        def find_account
          self.current_account = Account.find_by_username(account_subdomain)
        end
    end
  
    class UsersController < ApplicationController
      def index
        @users = current_account.users.all
      end
    end

    class AccountController < ApplicationController
      def new
        @new_account = Account.create(params[:new_account])
        redirect_to :host => account_host(@new_account.username), :controller => "weblog"
      end
    
      def authenticate
        session[account_domain] = :authenticated
        redirect_to :controller => "weblog"
      end
    
      protected
        def authenticated?
          session[account_domain] == :authenticated
        end
    end
  
And in your view:
  
    Your domain: <%= account_url %>

By default, all the methods will query for current_account.username as the account key, but you can
specialize that by overwriting default_account_subdomain. You can of course also pass it in
as the first argument to all the methods.


Copyright (c) 2005 David Heinemeier Hansson, released under the MIT license