Wallhaven API for PHP
===================
[![](https://img.shields.io/packagist/v/ivkos/wallhaven.svg?style=flat-square)](https://packagist.org/packages/ivkos/wallhaven)
[![](https://img.shields.io/packagist/dt/ivkos/wallhaven.svg?style=flat-square)](https://packagist.org/packages/ivkos/wallhaven)
[![](https://img.shields.io/github/license/ivkos/Wallhaven.svg?style=flat-square)](LICENSE)

## Description
A PHP library for **[Wallhaven](https://wallhaven.cc)** that allows you to search for wallpapers and get information
about them in convenient OOP syntax. Additionally, this library provides the ability to download individual
wallpapers, or batch download many wallpapers asynchronously which considerably reduces download times.

## Requirements
* PHP 5.4 or newer
* Composer

## Install
Create a `composer.json` file in your project root:
```json
{
    "require": {
        "ivkos/wallhaven": "2.*"
    }
}
```

Run `php composer.phar install` to download the library and its dependencies.

## Quick Documentation
Add this line to include Composer packages:
```php
<?php
require 'vendor/autoload.php';
```

Initialize Wallhaven:
```php
use Wallhaven\Category;
use Wallhaven\Order;
use Wallhaven\Purity;
use Wallhaven\Sorting;
use Wallhaven\Wallhaven;

$wh = new Wallhaven();
```

If you have an account on Wallhaven, you can use it to login and access all available wallpapers:
```php
$wh = new Wallhaven('YOUR_USERNAME', 'YOUR_PASSWORD');
```

### Searching
You can search for wallpapers and filter them using the `Wallhaven::filter()` method. It returns a `Filter` object that acts as a fluent interface with the following methods:

 - `keywords()` – Search query or #tagname, for example:
	 -  `"landscape"`
	 -  `"#cars"`
 - `categories()` – Category, or multiple categories as a bit field. For example:
	 - `Category::PEOPLE` 
	 - `Category::GENERAL | Category::PEOPLE`
	 - `Category::ALL` (default) - shorthand for `Category::GENERAL | Category::ANIME | Category::PEOPLE`
 - `purity()` – Purity, or multiple purities as a bit field. For example:
	 - `Purity::SFW` (default)
	 - `Purity::SFW | Purity::SKETCHY`
	 - `Purity::ALL` - shorthand for `Purity::SFW | Purity::SKETCHY | Purity::NSFW`
 - `sorting()` – Sorting. Can be one of the following:
	 - `Sorting::RELEVANCE` (default)
	 - `Sorting::RANDOM`
	 - `Sorting::DATE_ADDED`
	 - `Sorting::VIEWS`
	 - `Sorting::FAVORITES`
 - `order()` – Order of results. Can be one of the following:
	 - `Order::DESC` (default)
	 - `Order::ASC`
 - `resolutions()` – Resolutions. Should be an array of strings in the format of WxH, for example:
	 - `["1920x1080"]`
	 - `["1280x720", "2560x1440"]`
 - `ratios()` – Ratios. Should be an array of strings in the format of WxH, for example:
	 - `["9x16"]`
	 - `["16x9", "4x3"]`
 - `pages()`– Number of pages to fetch from Wallhaven. A single page typically consists of 24, 32 or 64 wallpapers.
 - `getWallpapers()` – Execute the search with the specified filters.

Examples:
```php
$wallpapers = $wh->filter()
	->keywords("#cars")
	->categories(Category::GENERAL)
	->purity(Purity::SFW)
	->sorting(Sorting::FAVORITES)
	->order(Order::DESC)
	->resolutions(["1920x1080", "2560x1440"])
	->ratios(["16x9"])
	->pages(3)
	->getWallpapers();
```

```php
$wallpapers = $wh->filter()
	->keywords("landscape")
	->ratios(["16x9"])
	->pages(2)
	->getWallpapers();
```
Returns a `WallpaperList` object containing `Wallpaper` objects that match the criteria above.

The `WallpaperList` object can be accessed like an array, iterated over using `foreach`, and has a `WallpaperList::count()` method:
```php
// Get favorites count for the first wallpaper in the list
$wallpapers[0]->getFavorites();

// Print resolutions of all wallpapers in the list
foreach ($wallpapers as $w) {
	echo $w->getResolution() . PHP_EOL;
}

// Get the number of wallpapers in the list
echo "There are " . $wallpapers->count() . " wallpapers!" . PHP_EOL;
```

### Wallpaper Information
The `Wallpaper` object has a number of methods that provide information about the wallpaper:

- `getId()`
- `getTags()`
- `getPurity()`
- `getResolution()`
- `getSize()`
- `getCategory()`
- `getViews()`
- `getFavorites()`
- `getFeaturedBy()` - not accessible if not logged in
- `getFeaturedDate()` - not accessible if not logged in
- `getUploadedBy()`
- `getUploadedDate()`
- `getImageUrl()`
- `getThumbnailUrl()`

You can get information about a single wallpaper if you know its ID:
```php
$w = $wh->wallpaper(198320);

$w->getTags();  // ["cats", "closeups"]
$w->getViews(); // int(3500)
```

You can also get information about wallpapers from a search result:
```php
$wallpapers = $wh->filter()->keywords(...)->getWallpapers();

$wallpapers[0]->getId();        // int(103929)
$wallpapers[0]->getFavorites(); // int(367)
```

### Downloading
To download a single wallpaper to a specific directory:
```php
$wh->wallpaper(198320)->download("/home/user/wallpapers");
```

To batch download wallpapers from a search result:
```php
$wallpapers = $wh->filter()->keywords(...)->getWallpapers();
$wallpapers->downloadAll("/home/user/wallpapers");
```

You can also create a `WallpaperList`, add specific wallpapers to it, and then batch download them, like so:
```php
use Wallhaven\Wallhaven;
use Wallhaven\WallpaperList;

$wh = new Wallhaven();
$batch = new WallpaperList();
$batch[] = $wh->wallpaper(198320);
$batch[] = $wh->wallpaper(103929);

$batch->downloadAll("/home/user/wallpapers");
```


#### For more information, please refer to the source code and the PHPDoc blocks.
