---
layout: post
title: "Building For Change"
category: posts
---

Years ago, when working on an early client's PHP app, we referenced
database columns by *position*. So, if we ran a SQL query that
returned columns `id`, `first_name`, and `last_name` in that order,
we'd pull them out by referencing something like `$columns[0]` for id,
`$columns[1]` for the first name, and so on.

There are two problems with this approach:

1. `$columns[1]` has little semantic meaning, unless you already know
what the second column returned was, which it's easy to forget.
2. It's really easy to update the SQL query later, and completely ruin
your app.

The latter issue is *the worst*. If you modify the query to return
`id`, `prefix`, `first_name`, `last_name`, you just broke all of the
positional references. Time to go update every reference to `$columns`
in the app! And even worse if it's some kind of stored procedure that
was changed without you knowing. Now the site's just *broke*.

## Fragility

Most folks don't reference database columns by position anymore
because of that type of fragility. But I see folks frequently doing
the same thing without realizing it.

Let's say you're building an API client in Rails. You're going to call
a User API, get a list of managers, and save them to your
database. Example code might look like:
<
```ruby
users = UserAPI.search(title: 'Manager')
users.each do |user_hash|
  Manager.create(user_hash)
end
```

You've just made the same mistake that we made back in our
positional-database PHP days! If your User API starts returning
attributes your database doesn't know about, `Manager.create` will
bomb out. A change from outside has caused your app to crash.

## Building For Change

Think about how web browsers work. If your browser sees a tag it
doesn't know, it doesn't vomit, it's just ignored. Or, `<input>` tags:
if the `type` is unknown, browsers default down to a `text`
field. When new input types are introduced (like `date`), your browser
either knows about it and shows a fancy datepicker, or it doesn't and
you just see a regular text entry field, which *still works*.

One way we might change our User API example above is to
[`#slice`](http://api.rubyonrails.org/classes/Hash.html#method-i-slice)
our incoming `user_hash`:

```ruby
users = UserAPI.search(title: 'Manager')
users.each do |user_hash|
  Manager.create(user_hash.slice(:id, :first_name, :last_name))
end
```

This way, if the APi we're working with returns extra data we're not
interested, we don't crash!

It's a little harder, though, if the API suddenly stops returning data
that you require.

When writing an API, think about how your changes will affect
clients. Returning more data than users are expecting is generally
fine. But whenever you want to *remove* data, you need to consider
creating a new version of the API, so that users of the current
version don't suddenly find themselves without data they were
depending on.

It's easy to get yourself into these types of traps if you're not
expecting them, but thankfully spotting and avoiding them is also
pretty easy. With a little forethought, it's easy to build systems
that are robust in the face of unexpected external changes.

This post was inspired by various [work](http://covermymeds.com) conversations, as well
as several points raised in
[Ruby Rogues #172: Extreme Deployment](http://rubyrogues.com/172-rr-extreme-deployment-with-badri-janakiraman-and-florian-motlik/).
