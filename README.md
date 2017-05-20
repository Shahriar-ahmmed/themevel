# Laravel-Themevel

Themevel is a Laravel 5 theme and asset management. You can easily integrate this package with any Laravel based project.

### Features

* Custom theme location
* Parent theme support
* Unlimited Parent view finding
* Asset Finding
* Theme translator support
* Multiple theme config extension
* Multiple theme changelog extension
* Artisan console commands

### Installation

Themevel is a Laravel package so you can install it via composer. Run this command in your terminal from your project directory.

```ssh
composer require shipu/themevel
```

Wait for a while, Composer will automatically install Themevel in your project.

### Configuration

When the download is complete, you have to call this package service in `config/app.php` config file. To do that, add this line in `app.php` in `providers` section

```php
Shipu\Themevel\Providers\ThemevelServiceProvider::class,
```

To use facade you have to add this line in `app.php` in `aliases` array

```php
'Theme' => Shipu\Themevel\Facades\Theme::class,
```

Now run this command in your terminal to publish this package resources

```
php artisan vendor:publish --provider="Shipu\Themevel\Providers\ThemevelServiceProvider"
```

### Artisan Command
Run this command in your terminal from your project directory.

Create a theme directory
```ssh
php artisan theme:create your_theme_name


 What is theme title?:
 > 

 What is theme description? []:
 > 

 What is theme author name? []:
 >  

 What is theme version? []:
 > 

 Any parent theme? (yes/no) [no]:
 > y

 What is parent theme name?:
 > 

```
List of all themes
```ssh
php artisan theme:list

+----------+--------------+---------+----------+
| Name     | Author       | Version | Parent   |
+----------+--------------+---------+----------+
| themeone | Shipu Ahamed | 1.1.0   |          |
| themetwo | Shipu Ahamed | 1.0.0   | themeone |
+----------+--------------+---------+----------+
```

### Example folder structure:
```
- app/
- ..
- ..
- Themes/
    - themeone/
        - assets
            - css
                - app.css
            - img
            - js
        - lang
            - en
                -content.php
        - views/
            - layouts
                - master.blade.php
            - welcome.blade.php
        - changelog.yml        
        - theme.json
```
you can change `theme.json` and `changelog.yml` name from `config/theme.php`

```php
// ..
'config' => [
    'name' => 'theme.json',
    'changelog' => 'changelog.yml'
],
// ..
```

`json`, `yml`, `yaml`, `php`, `ini`, `xml` extension supported.  

For example:
```php
// ..
'config' => [
    'name' => 'theme.json',
    'changelog' => 'changelog.json'
],
// ..
```
Then run `theme:create` command which describe above.

Now Please see the API List Doc.

### API List
- [set](https://github.com/shipu/themevel#set)
- [get](https://github.com/shipu/themevel#get)
- [current](https://github.com/shipu/themevel#current)
- [all](https://github.com/shipu/themevel#all)
- [has](https://github.com/shipu/themevel#has)
- [getThemeInfo](https://github.com/shipu/themevel#getThemeInfo)
- [assets](https://github.com/shipu/themevel#assets)
- [lang](https://github.com/shipu/themevel#lang)

### set

For switching current theme you can use `set` method.

```php
Theme::set('theme-name');
```

### get

For getting current theme details you can use `get` method. 

```php
Theme::get(); // return Array
```
you can also get particular theme details.
```php
Theme::get('theme-name'); // return Array
```

```php
Theme::get('theme-name', true); // return Collection
```

### current

Retrieve current theme's name

```php
Theme::current(); // return string
```

### all

Retrieve All Theme Information

```php
Theme::all(); // return Array
```

### has

For getting theme exist or not

```php
Theme::has(); // return bool
```

### getThemeInfo

For getting theme exist or not

```php
$themeInfo = Theme::getThemeInfo('theme-name'); // return Collection

$themeName = $themeInfo->get('name');
// or
$themeName = $themeInfo['name'];
```
Also fallback support
```php
$themeInfo = Theme::getThemeInfo('theme-name'); // return Collection

$themeName = $themeInfo->get('changelog.versions');
// or
$themeName = $themeInfo['changelog.versions'];
// or you can also call like as multi dimension
$themeName = $themeInfo['changelog']['versions'];
```

### assets

For binding theme asset you can use `assets` method

```php
Theme::assets('your_asset_path'); // return string
```
It's Generate `BASE_URL/theme_roots/your_active_theme_name/assets/your_asset_path`

If your_asset_path not exist then it's find to active theme immediate parent assets folder. Look Like `BASE_URL/theme_roots/your_active_theme_parent_name/assets/your_asset_path`

For Using Helper
```php
themes('your_asset_path'); // return string
```

If You you want to bind specific theme assets:
```php
Theme::assets('your_theme_name:your_asset_path'); // return string
//or 
themes('your_theme_name:your_asset_path'); // return string
```

**Suppose you want to bind `app.css` in your blade. Then below code can be applicable**
```php
<link rel="stylesheet" href="{{ themes('app.css') }}">
```
Specific theme assets:
```php
<link rel="stylesheet" href="{{ themes('your_theme_name:app.css') }}">
```
### lang

The `lang` method translates the given language line using your current **theme** [localization files](https://laravel.com/docs/5.4/localization):
```php
echo Theme::lang('content.title'); // return string
// or
echo lang('content.title'); // return string
```
If You you want to bind specific theme assets:
```php
echo Theme::lang('your_theme_name::your_asset_path'); // return string
//or 
echo lang('your_theme_name::your_asset_path'); // return string
```

### How to use in Route
```
Route::get('/', function () {
    Theme::set('your_themen_name');
    return view('welcome');
});
```
This will firstly check if there is a welcome.blade.php in current theme directory. If none is found then it checks parent theme, and finally falls back to default laravel views location.

If You you want to specific theme view:
```
Route::get('/', function () {
    Theme::set('your_theme_name');
    return view('your_theme_name::welcome');
});
```

### Set theme using route middleware
A helper middleware is included out of the box if you want to define a Theme per route. To use it:

First register it in app\Http\Kernel.php:

```
protected $routeMiddleware = [
    // ...
    'theme' => \Shipu\Themevel\Middleware\RouteMiddleware::class,
];
```
Now you can apply the middleware to a route or route-group. Eg:
```
Route::group(['prefix' => 'admin', 'middleware'=>'theme:Your_theme_name'], function() {
    // ... Add your routes here 
    // The Your_theme_name will be applied.
});
```

### Set theme using web middleware
A helper middleware is included out of the box if you want to define a Theme per route. To use it:

First register it in app\Http\Kernel.php:

```
protected $middlewareGroups = [
    'web' => [
        // ...
        \Shipu\Themevel\Middleware\WebMiddleware::class,
    ],
    // ...
];
```
Theme set from `config/theme.php` .

### Dependency Injection
You can also inject theme instance using ThemeContract.

``` php
use Shipu\Themevel\Contracts\ThemeContract;

private $theme;

public function __construct(ThemeContract $theme)
{
    $this->theme = $theme
}
```

## Support for this project
Hey dude! Help me out for a couple of :beers:!

[![Beerpay](https://beerpay.io/Shipu/themevel/badge.svg?style=beer)](https://beerpay.io/Shipu/themevel) [![Beerpay](https://beerpay.io/Shipu/themevel/make-wish.svg?style=flat-square)](https://beerpay.io/Shipu/themevel?focus=wish) 
