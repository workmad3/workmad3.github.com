---
layout: post
title: Omniauth and Devise
excerpt: Testing Omniauth and Devise integration 
---
One of the last implementation parts of the fishdelish project is a self-publishing mechanism. Having gotten to the start of this task, I realised that it needed 
some form of user identification. With the recent gawker security breach on my mind, I was very much in the mood for an external solution which led me to Omniauth
and the latest branch of Devise which contains strategies for its use.

One key part of integrating such a sign-on mechanism to me is to make sure it is testable and then to test it, which in turn led me to find out how to test with 
third party services in omniauth. This is where Devise comes in handy, as it provides test modes and stubs for the omniauth library that allows you to test that
your integration is correct while stubbing out the third party calls that would otherwise prove problematic (after all, you don't want to actually authenticate
against facebook with your tests).

The first part is to get Cucumber set up for these tests. Drawing on the [Devise omniauth documentation](https://github.com/plataformatec/devise/wiki/OmniAuth:-Testing), 
I came up with the following:

{% highlight ruby %}
Devise::OmniAuth.test_mode!

ACCESS_TOKEN = {
  :access_token => "plataformatec"
}

FACEBOOK_INFO = {
  :id => '12345',
  :link => 'http://facebook.com/user_example',
  :email => 'user@example.com',
  :first_name => 'User',
  :last_name => 'Example',
  :website => 'http://blog.plataformatec.com.br'
}

Before do
  Devise::OmniAuth.short_circuit_authorizers!
  Devise::OmniAuth.stub!(:facebook) do |b|
    b.post('/oauth/access_token') { [200, {}, ACCESS_TOKEN.to_json] }
    b.get('/me?access_token=plataformatec') { [200, {}, FACEBOOK_INFO.to_json] }
  end  
end

After do |scenario|
  Devise::OmniAuth.unshort_circuit_authorizers!
  Devise::OmniAuth.reset_stubs!
end
{% endhighlight %}

Basically, you start by putting OmniAuth into test mode, and then short-circuit and stub the authorizers before each scenario and reset them afterwards (to return them to a clean slate).

You can then login by following the link to the facebook/twitter/open-id login on your page in your Scenario. Once you've done the authentication callbacks for your chosen service(s) then the step 
will pass and you can continue as an authenticated user. Magic! Next, I'll drill down into the implementation of the callback handlers and work out how to test them through RSpec.
