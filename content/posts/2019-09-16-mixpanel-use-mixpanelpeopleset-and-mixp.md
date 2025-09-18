+++
date = "Mon, 16 Sep 2019 23:06:00 +0000"
title = "MixPanel use mixpanelpeopleset and mixpanelidentify together"
slug = "mixpanel-peopleset-identify-together"
authors = [ "Ivan Lawrence" ]
tags = [ "nerdler" ]
categories = []
series = [ ]
+++

# MixPanel use mixpanelpeopleset and mixpanelidentify together

Found at [https://community.mixpanel.com/questions/585/how-to-implement-peopleset-in-js-api.html](https://community.mixpanel.com/questions/585/how-to-implement-peopleset-in-js-api.html)

Question

[![avatar image](https://secure.gravatar.com/avatar/ec922e411573ac5d2fc4727e8cd26d0c?d=identicon&r=PG&s=48 "stephendb9")](https://community.mixpanel.com/users/470/stephendb9.html)

[stephendb9](https://community.mixpanel.com/users/470/stephendb9.html) asked · Jan 22 at 11:45 AM

How to implement people.set() in JS API
=======================================

What is the right way to implement tracking for People properties in JS? The documentation states:

> In order to send profile updates, you _must_ call [mixpanel.identify](https://mixpanel.com/help/reference/javascript-full-api-reference#mixpanel.identify). The Mixpanel library does not automatically create people profiles for any user that performs an event; you have to explicitly call mixpanel.identify, which empowers you to only create profiles for registered users.

But in the examples, _identify()_ is called before _people.set()_ and sometimes it's after. Or not at all.

[https://developer.mixpanel.com/docs/javascript#section-setting-profile-properties](https://developer.mixpanel.com/docs/javascript#section-setting-profile-properties)

-   What is correct sequence of calls?
-   Does _identify()_ have to be called every time you want to change a property? Or just once per Profile?
-   If _identify()_ was already called for this Profile, do you just call it again with the same id?
-   What if this Profile was created with _alias()_?

[api](https://community.mixpanel.com/topics/api.html)[people profiles](https://community.mixpanel.com/topics/people+profiles.html)[people properties](https://community.mixpanel.com/topics/people+properties.html)

 1

1 Answer

[![avatar image](https://secure.gravatar.com/avatar/68b7ed2d59f8c8e6790b90e1c6f1896a?d=identicon&r=PG&s=48 "annecamacho")](https://community.mixpanel.com/users/69/annecamacho.html)

[annecamacho](https://community.mixpanel.com/users/69/annecamacho.html) answered · Mar 11 at 2:27 PM

[@stephendb9](https://community.mixpanel.com/users/470/stephendb9.html) -

The mixpanel.identify() call when paired with a mixpanel.people.set() call is intended to _flush_ the data to Mixpanel, and it is required anytime that you intend to create or update a Mixpanel people property.

**What is correct sequence of calls?**

-   You just need to call identify in the same function/context that you call people.set — the actual sequence in your code is less critical.

**Does _identify()_ have to be called every time you want to change a property? Or just once per Profile?**

-   Identify needs to be called every time you make a people.set call — to either create or update a people profile within Mixpanel.

**If _identify()_ was already called for this Profile, do you just call it again with the same id?**

-   If you've already identified the user you do not need to pass anything through the identify function, as you are just using it to flush the data. My personal preference is to always have an empty identify call i.e. _mixpanel.identify() vs mixpanel.identify(xyz)_ after every people.set to ensure the data is sent to Mixpanel and stored to the profile.

**What if this Profile was created with _alias()_?**

-   If you are aliasing or identifying a user, then a profile will not be created, unless you are also doing a people.set. Alias and identify methods are intended to help manage the identities of your users throughout their interactions in your site or app, but they also serve a second purpose of flushing the data to Mixpanel. The alias method, similar to identify, has a flush mechanism, but should only be used for the initial people.set calls when users are registering or signing up for an account. Whenever you add/update future people properties with people.set, you would call identify() to flush the data.

For more details on the alias and identify methods, [here is a great overview](https://help.mixpanel.com/hc/en-us/articles/115004497803-Identity-Management-Best-Practices).

\-Anne