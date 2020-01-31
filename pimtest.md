## Overview

Example we need a JSON product API for our shop with following features:

- Home Page
- Admin Dashboard
- User Login Register
- Product API

Lets see how fast we can do it. Demo Project URL will be http://pimhw.test

## Create new project

```bash
sites && lara pimhw 5.8
```

## Project URL

http://pimhw.test

## Open project in PHPstorm

```bash
cd pimhw && phps
```

## Database connection

Edit .env for database config

```bash
DB_DATABASE= pimhw    
DB_USERNAME= root       
DB_PASSWORD= hw1999
```

## Create a user login and register system and admin dashboard for the project

```bash
php artisan make:auth && php artisan migrate
```
Now you can create a new user and login to dashboard

## Create a Model, Controller, Migration and Factory for the Product

```bash
php artisan make:model Product -a
```

## Create a product table

`database/migrations/___create_products_table.php`

```bash
$table->string('name');
$table->longText('description');
$table->integer('price')->default(0);
```

## Migrate product table to Database

```bash
php artisan migrate
```

## Add dummy data in product table

`database/factories/ProductFactory.php`

```php
'name' => $faker->word,
'description' => $faker->paragraph,
'price' => $faker->randomDigit,
```

DatabaseSeeder `database/seeds/DatabaseSeeder.php`

```php
factory(App\Product::class,200)->create();
```

Lets seed database

```bash
php artisan db:seed
```

## Create a route/link for product api

Test product route http://pimhw.test/product it will give 404 not found error. Because we haven't add route. Lets add route in the `routes/web.php`

```
 Route::get('/product', 'ProductController@index')->name('product');
```

Now test the product link http://pimhw.test/product it will show a black page.

## Edit Controller - to get all the product as json api

To view your json data add a function to controller `app/Http/Controller/ProductController.php`

```php
return Product::all();
```

Now check your json api http://pimhw.test/product. It will show all the data from database.

## Conclusion

We have typed 9 lines code and 7 lines command. 
