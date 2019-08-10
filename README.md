# Design Best Practices

It's not a strict do'es and don'ts which you should while designing, it's just a list of few things that you should keep in mind while designing webites.

## Contents

[Avoid Deep Nesting](#avoid-deep-nesting)

[Variable Naming Convention](#variable-naming-convention)

[Use @mixins](#use-mixins)

[Name spacing](#name-spacing)

[Don't mirror DOM structure](#dont-mirror-dom-structure)

[Never use ID selectors](#never-use-id-selectors)

[Prefer to use Eloquent over using Query Builder and raw SQL queries. Prefer collections over arrays](#prefer-to-use-eloquent-over-using-query-builder-and-raw-sql-queries-prefer-collections-over-arrays)

[Mass assignment](#mass-assignment)

[Do not execute queries in Blade templates and use eager loading (N + 1 problem)](#do-not-execute-queries-in-blade-templates-and-use-eager-loading-n--1-problem)

[Comment your code, but prefer descriptive method and variable names over comments](#comment-your-code-but-prefer-descriptive-method-and-variable-names-over-comments)

[Do not put JS and CSS in Blade templates and do not put any HTML in PHP classes](#do-not-put-js-and-css-in-blade-templates-and-do-not-put-any-html-in-php-classes)

[Use config and language files, constants instead of text in the code](#use-config-and-language-files-constants-instead-of-text-in-the-code)

[Use standard Laravel tools accepted by community](#use-standard-laravel-tools-accepted-by-community)

[Follow Laravel naming conventions](#follow-laravel-naming-conventions)

[Use shorter and more readable syntax where possible](#use-shorter-and-more-readable-syntax-where-possible)

[Use IoC container or facades instead of new Class](#use-ioc-container-or-facades-instead-of-new-class)

[Do not get data from the `.env` file directly](#do-not-get-data-from-the-env-file-directly)

[Store dates in the standard format. Use accessors and mutators to modify date format](#store-dates-in-the-standard-format-use-accessors-and-mutators-to-modify-date-format)

[Other good practices](#other-good-practices)

### **Avoid Deep Nesting**

Avoid deep nesting you should try not to go beyond three level of nesting in SCSS.

Bad:

```scss
.parent-class {
  .child-class {
    .child-child-class {
      .target-class {
        background-color :red;
      }
    }
  }
}
```

Good:

```scss
.parent-class {
  .target-class {
    background-color :red;
  }
}
```

[🔝 Back to contents](#contents)

### **Variable Naming Convention**

One of the key feature of SCSS is to give us the chance to declare global variables, and as a designer you should grab this by both hands. Never declare variable based on the behavior or on their appearancs, declare it on the basis of purpose.

Bad:

```scss
$red-color : FF0000;
```

Good:

```scss
$primary-color: FF0000;
```
OR
```scss
$theme-color: FF0000;
```

[🔝 Back to contents](#contents)

### **Use @mixins**

Another good feature of SCSS is it gives liberty to create functions. When you know that you have a bunch of CSS and that needs to be reused with different values you should use @mixin such cases. For instance: when you are create class with different font-size for your project.

Bad:

```scss
.text-small {
  font-size:12px;
}
.text-medium {
  font-size:14px;
}
.text-large {
  font-size:16px;
}
```

Good:

```scss
// variable.scss

$typography: (
        small: 12px,
        medium: 14px,
        regular: 16px,
);

//mixin.scss

@mixin typography-class-generator() {
  @each $name, $value in $typography {
    &_#{$name} {
      font-size: $value !important;
    }
  }
}

// typography.scss

.text {
    @include typography-class-generator()
}
```

[🔝 Back to contents](#contents)

### **Name spacing**

Name spacing is a good thing but it should be done at component level not at project/app level.

Bad:

```scss
.nav,.home-nav,.profile-nav
```

Good:

```scss
.nav,.nav-bar,.nav-list
```

[🔝 Back to contents](#contents)


### **Don't mirror DOM structure**

Don't try to dig your SCSS to the level your DOM does, you know why? For two reasons,
1. SCSS is a great tool for code reusability and you are murdering it if you are replicating DOM structure.
2. You are also killing any possibility of flexible changes in the HTML by mirroring the DOM in SCSS and that's an offense!

[🔝 Back to contents](#contents) 

### **Never use ID selectors**

ID's are subjected to be unique, how can you use a unique selector for styling. You are killing the scope of reusability again a criminal offense!

[🔝 Back to contents](#contents) 




### **Wrap your custom style for library classes into your custom class**

When you wish to overright the thing that your design library provides it is a good move to wrap your custom style with a custom class. Consider you want to change the navbar color of Bootstrap Navbar.


Bad:

```scss
.navbar-nav {
  background-color:red;
}
```

Good:

```php
.app-navbar {
 .navbar-nav {
   background-color:red;
 }
}
```

[🔝 Back to contents](#contents)

### **Prefer to use Eloquent over using Query Builder and raw SQL queries. Prefer collections over arrays**

Eloquent allows you to write readable and maintainable code. Also, Eloquent has great built-in tools like soft deletes, events, scopes etc.

Bad:

```sql
SELECT *
FROM `articles`
WHERE EXISTS (SELECT *
              FROM `users`
              WHERE `articles`.`user_id` = `users`.`id`
              AND EXISTS (SELECT *
                          FROM `profiles`
                          WHERE `profiles`.`user_id` = `users`.`id`) 
              AND `users`.`deleted_at` IS NULL)
AND `verified` = '1'
AND `active` = '1'
ORDER BY `created_at` DESC
```

Good:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 Back to contents](#contents)

### **Mass assignment**

Bad:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Add category to article
$article->category_id = $category->id;
$article->save();
```

Good:

```php
$category->article()->create($request->validated());
```

[🔝 Back to contents](#contents)

### **Do not execute queries in Blade templates and use eager loading (N + 1 problem)**

Bad (for 100 users, 101 DB queries will be executed):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

Good (for 100 users, 2 DB queries will be executed):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 Back to contents](#contents)

### **Comment your code, but prefer descriptive method and variable names over comments**

Bad:

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

Better:

```php
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```

Good:

```php
if ($this->hasJoins())
```

[🔝 Back to contents](#contents)

### **Do not put JS and CSS in Blade templates and do not put any HTML in PHP classes**

Bad:

```php
let article = `{{ json_encode($article) }}`;
```

Better:

```php
<input id="article" type="hidden" value="@json($article)">

Or

<button class="js-fav-article" data-article="@json($article)">{{ $article->name }}<button>
```

In a Javascript file:

```javascript
let article = $('#article').val();
```

The best way is to use specialized PHP to JS package to transfer the data.

[🔝 Back to contents](#contents)

### **Use config and language files, constants instead of text in the code**

Bad:

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```

Good:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 Back to contents](#contents)

### **Use standard Laravel tools accepted by community**

Prefer to use built-in Laravel functionality and community packages instead of using 3rd party packages and tools. Any developer who will work with your app in the future will need to learn new tools. Also, chances to get help from the Laravel community are significantly lower when you're using a 3rd party package or tool. Do not make your client pay for that.

Task | Standard tools | 3rd party tools
------------ | ------------- | -------------
Authorization | Policies | Entrust, Sentinel and other packages
Compiling assets | Laravel Mix | Grunt, Gulp, 3rd party packages
Development Environment | Homestead | Docker
Deployment | Laravel Forge | Deployer and other solutions
Unit testing | PHPUnit, Mockery | Phpspec
Browser testing | Laravel Dusk | Codeception
DB | Eloquent | SQL, Doctrine
Templates | Blade | Twig
Working with data | Laravel collections | Arrays
Form validation | Request classes | 3rd party packages, validation in controller
Authentication | Built-in | 3rd party packages, your own solution
API authentication | Laravel Passport | 3rd party JWT and OAuth packages
Creating API | Built-in | Dingo API and similar packages
Working with DB structure | Migrations | Working with DB structure directly
Localization | Built-in | 3rd party packages
Realtime user interfaces | Laravel Echo, Pusher | 3rd party packages and working with WebSockets directly
Generating testing data | Seeder classes, Model Factories, Faker | Creating testing data manually
Task scheduling | Laravel Task Scheduler | Scripts and 3rd party packages
DB | MySQL, PostgreSQL, SQLite, SQL Server | MongoDB

[🔝 Back to contents](#contents)

### **Follow Laravel naming conventions**

 Follow [PSR standards](http://www.php-fig.org/psr/psr-2/).
 
 Also, follow naming conventions accepted by Laravel community:

What | How | Good | Bad
------------ | ------------- | ------------- | -------------
Controller | singular | ArticleController | ~~ArticlesController~~
Route | plural | articles/1 | ~~article/1~~
Named route | snake_case with dot notation | users.show_active | ~~users.show-active, show-active-users~~
Model | singular | User | ~~Users~~
hasOne or belongsTo relationship | singular | articleComment | ~~articleComments, article_comment~~
All other relationships | plural | articleComments | ~~articleComment, article_comments~~
Table | plural | article_comments | ~~article_comment, articleComments~~
Pivot table | singular model names in alphabetical order | article_user | ~~user_article, articles_users~~
Table column | snake_case without model name | meta_title | ~~MetaTitle; article_meta_title~~
Model property | snake_case | $model->created_at | ~~$model->createdAt~~
Foreign key | singular model name with _id suffix | article_id | ~~ArticleId, id_article, articles_id~~
Primary key | - | id | ~~custom_id~~
Migration | - | 2017_01_01_000000_create_articles_table | ~~2017_01_01_000000_articles~~
Method | camelCase | getAll | ~~get_all~~
Method in resource controller | [table](https://laravel.com/docs/master/controllers#resource-controllers) | store | ~~saveArticle~~
Method in test class | camelCase | testGuestCannotSeeArticle | ~~test_guest_cannot_see_article~~
Variable | camelCase | $articlesWithAuthor | ~~$articles_with_author~~
Collection | descriptive, plural | $activeUsers = User::active()->get() | ~~$active, $data~~
Object | descriptive, singular | $activeUser = User::active()->first() | ~~$users, $obj~~
Config and language files index | snake_case | articles_enabled | ~~ArticlesEnabled; articles-enabled~~
View | snake_case | show_filtered.blade.php | ~~showFiltered.blade.php, show-filtered.blade.php~~
Config | snake_case | google_calendar.php | ~~googleCalendar.php, google-calendar.php~~
Contract (interface) | adjective or noun | Authenticatable | ~~AuthenticationInterface, IAuthentication~~
Trait | adjective | Notifiable | ~~NotificationTrait~~

[🔝 Back to contents](#contents)

### **Use shorter and more readable syntax where possible**

Bad:

```php
$request->session()->get('cart');
$request->input('name');
```

Good:

```php
session('cart');
$request->name;
```

More examples:

Common syntax | Shorter and more readable syntax
------------ | -------------
`Session::get('cart')` | `session('cart')`
`$request->session()->get('cart')` | `session('cart')`
`Session::put('cart', $data)` | `session(['cart' => $data])`
`$request->input('name'), Request::get('name')` | `$request->name, request('name')`
`return Redirect::back()` | `return back()`
`is_null($object->relation) ? null : $object->relation->id` | `optional($object->relation)->id`
`return view('index')->with('title', $title)->with('client', $client)` | `return view('index', compact('title', 'client'))`
`$request->has('value') ? $request->value : 'default';` | `$request->get('value', 'default')`
`Carbon::now(), Carbon::today()` | `now(), today()`
`App::make('Class')` | `app('Class')`
`->where('column', '=', 1)` | `->where('column', 1)`
`->orderBy('created_at', 'desc')` | `->latest()`
`->orderBy('age', 'desc')` | `->latest('age')`
`->orderBy('created_at', 'asc')` | `->oldest()`
`->select('id', 'name')->get()` | `->get(['id', 'name'])`
`->first()->name` | `->value('name')`

[🔝 Back to contents](#contents)

### **Use IoC container or facades instead of new Class**

new Class syntax creates tight coupling between classes and complicates testing. Use IoC container or facades instead.

Bad:

```php
$user = new User;
$user->create($request->validated());
```

Good:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[🔝 Back to contents](#contents)

### **Do not get data from the `.env` file directly**

Pass the data to config files instead and then use the `config()` helper function to use the data in an application.

Bad:

```php
$apiKey = env('API_KEY');
```

Good:

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```

[🔝 Back to contents](#contents)

### **Store dates in the standard format. Use accessors and mutators to modify date format**

Bad:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

Good:

```php
// Model
protected $dates = ['ordered_at', 'created_at', 'updated_at'];
public function getSomeDateAttribute($date)
{
    return $date->format('m-d');
}

// View
{{ $object->ordered_at->toDateString() }}
{{ $object->ordered_at->some_date }}
```

[🔝 Back to contents](#contents)

### **Other good practices**

Never put any logic in routes files.

Minimize usage of vanilla PHP in Blade templates.

[🔝 Back to contents](#contents)
