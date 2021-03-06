# Clout

[![Build Status](https://travis-ci.org/weavejester/clout.svg?branch=master)](https://travis-ci.org/weavejester/clout)

Clout is a library for matching [Ring][1] HTTP requests. It uses the same
routing syntax as used by popular Ruby web frameworks like Ruby on Rails and
Sinatra.

[1]: https://github.com/ring-clojure/ring

## Installation

Add the following to your project.clj dependencies:

```clj
[clout "1.2.0"]
```

## Usage

These following examples make use of the [ring-mock][2] library to
generate Ring request maps.

[2]: https://github.com/weavejester/ring-mock

```clj
user=> (use 'ring.mock.request 'clout.core)
nil
user=> (route-matches "/article/:title"
                      (request :get "/article/clojure"))
{:title "clojure"}
user=> (route-matches "/public/*"
                      (request :get "/public/style/screen.css"))
{:* "style/screen.css"}
```

Clout can also match absolute routes:

```clj
user=> (route-matches "http://subdomain.example.com/"
                      (request :get "http://subdomain.example.com/"))
{}
```
And scheme-relative routes:

```clj
user=> (route-matches "//subdomain.example.com/"
                      (request :get "http://subdomain.example.com/"))
{}
user=> (route-matches "//subdomain.example.com/"
                      (request :get "https://subdomain.example.com/"))
{}
```

Clout supports both keywords and wildcards. Keywords (like ":title") will
match any character but the following: `/ . , ; ?`. Wildcards (*) will match
anything.

If a route does not match, nil is returned:

```clj
user=> (route-matches "/products" (request :get "/articles"))
nil
```

For additional performance, you can choose to pre-compile a route:

```clj
user=> (def user-route (route-compile "/user/:id"))
#'user/user-route
user=> (route-matches user-route (request :get "/user/10"))
{:id "10"}
```

When compiling a route, you can specify a map of regular expressions to use
for different keywords. This allows more specific routing:

```clj
user=> (def user-route (route-compile "/user/:id" {:id #"\d+"}))
#'user/user-route
user=> (route-matches user-route (request :get "/user/10"))
{:user "10"}
user=> (route-matches user-route (request :get "/user/jsmith"))
nil
```

## License

Copyright © 2014 James Reeves

Distributed under the Eclipse Public License either version 1.0 or (at
your option) any later version.
