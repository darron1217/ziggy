![Ziggy - Use your Laravel Named Routes inside JavaScript](https://raw.githubusercontent.com/tightenco/ziggy/master/ziggy-banner.png?version=2)

# Ziggy - Use your Laravel Named Routes inside JavaScript
![TravisCi Status for tightenco/ziggy](https://travis-ci.org/tightenco/ziggy.svg?branch=master)


Ziggy creates a Blade directive which you can include in your views. This will export a JavaScript object of your application's named routes, keyed by their names (aliases), as well as a global `route()` helper function which you can use to access your routes in your JavaScript.

## Installation

1. Add Ziggy to your Composer file: `composer require tightenco/ziggy`

2. (if Laravel 5.4) Add `Tightenco\Ziggy\ZiggyServiceProvider::class` to the `providers` array in your `config/app.php`.

3. Include our Blade Directive (`@routes`) somewhere in your template before your main application JavaScript is loaded&mdash;likely in the header somewhere.

## Usage

This package replaces the `@routes` directive with a collection of all of your application's routes, keyed by their names. This collection is available at `Ziggy.namedRoutes`.

The package also creates an optional `route()` JavaScript helper which functions like Laravel's `route()` PHP helper, which can be used to retrieve URLs by name and (optionally) parameters.

### Examples:

Without parameters:

```js
route('posts.index') // Returns '/posts'
```

With required parameter:

```js
route('posts.show', {id: 1}) // Returns '/posts/1'
route('posts.show', [1]) // Returns '/posts/1'
route('posts.show', 1) // Returns '/posts/1'
```

With multiple required parameters:

```js
route('events.venues.show', {event: 1, venue: 2}) // Returns '/events/1/venues/2'
route('events.venues.show', [1, 2]) // Returns '/events/1/venues/2'
```

If whole objects are passed, Ziggy will automatically look for `id` primary key:

```js
var event = {id: 1, name: 'World Series'};
var venue = {id: 2, name: 'Rogers Centre'};

route('events.venues.show', [event, venue]) // Returns '/events/1/venues/2'
```

Practical AJAX example:

```js
var post = {id: 1, title: 'Ziggy Stardust'};

return axios.get(route('posts.show', post))
    .then((response) => {
        return response.data;
    });
```
### Default Values
See Laravel [documentation](https://laravel.com/docs/5.5/urls#default-values)

Default values work out of the box for Laravel versions >= 5.5.29,
for the previous versions you will need to set the default parameters
by including this code somewhere in the same page as our Blade Directive (@routes)
```js
Ziggy.defautParameters = {
    //example
    locale: "en"
}
```

## Filtering Routes
Filtering routes is *completely* optional. If you want to pass all of your routes to JavaScript by default, you can carry on using Ziggy as described above.

### Basic Whitelisting & Blacklisting
To take advantage of basic whitelisting or blacklisting of routes, you will first need to create a standard config file called `ziggy.php` in the `config/` directory of your Laravel app and set **either** the `whitelist` or `blacklist` setting to an array of route names.

**Note: You've got to choose one or the other. Setting `whitelist` and `blacklist` will disable filtering altogether and simply return the default list of routes.**

#### Example `config/ziggy.php`:
```php
<?php
return [
    // 'whitelist' => ['home', 'api.*'],
    'blacklist' => ['admin.*', 'vulnerabilities.*'],
];
```

As shown in the example above, Ziggy the use of asterisks as wildcards in filters. `home` will only match the route named `home` whereas `api.*` will match any route whose name begins with `api.`, such as `api.posts.index` and `api.users.show`.

### Simple Whitelisting & Blacklisting Macros

Whitelisting and blacklisting can also be achieved using the following macros.

#### Example Whitelisting

```php
Route::whitelist(function () {
    Route::get('...')->name('posts');
});

Route::whitelist()->get('...')->name('posts');
```

#### Example Blacklisting

```php
Route::blacklist(function () {
    Route::get('...')->name('posts');
});

Route::blacklist()->get('...')->name('posts');
```

### Advanced Whitelisting Using Groups

You may also optionally define multiple whitelists by defining `groups` in your `config/ziggy.php`:

```php
<?php
return [
    'groups' => [
        'admin' => [
            'admin.*',
            'posts.*',
        ],
        'author' => [
            'posts.*',
        ]
    ],
];
```

In the above example, you can see we have configured multiple whitelists for different user roles.  You may expose a specific whitelist group by passing the group key into `@routes` within your blade view.  Example:

```php
@routes('author')
```
**Note: Using a group will always take precedence over the above mentioned `whitelist` and `blacklist` settings.**

### Other useful methods

#### `current()`
To get the name of the current route (based on the browser's `window.location`) you can use:

```javascript
route().current()
// returns "home"
```

To check that we are at a current route, pass the desired route in the only param:

```javascript
route().current("home")
// returns true
```

#### `url()`
Ziggy returns a wrapper of the string primitive, which behaves exactly like a string in almost all cases.
In rare cases where third-party libraries use strict type checking, you may require an actual `String` literal.

To achieve this simple call `.url()` on your route:

```javascript
route('home').url()
// http://myapp.dev/
```

## Contributions & Credits

To get started contributing to Ziggy, check out [the contribution guide](CONTRIBUTING.md).

- [Daniel Coulbourne](https://twitter.com/DCoulbourne)
- [Matt Stauffer](https://twitter.com/stauffermatt)

Thanks to [Caleb Porzio](http://twitter.com/calebporzio), [Adam Wathan](http://twitter.com/adamwathan), and [Jeffrey Way](http://twitter.com/jeffrey_way) for help solidifying the idea.

### Thanks to [all our contributors](https://github.com/tightenco/ziggy/graphs/contributors)


