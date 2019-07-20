## RESTful API
REST stands for REpresentational State Transfer and is an architectural style for network communication between applications, which relies on a stateless protocol (usually HTTP) for interaction.







## API resources
API resources present a way to easily transform our models into JSON responses. It acts as a transformation layer that sits between our Eloquent models and the JSON responses that are actually returned by our API. API resources is made of two entities: a resource class and a resource collection. A resource class represents a single model that needs to be transformed into a JSON structure, while a resource collection is used for transforming collections of models into a JSON structure.







## HTTP Verbs Represent Actions
In RESTful APIs, we use the HTTP verbs as actions, and the endpoints are the resources acted upon. We’ll be using the HTTP verbs for their semantic meaning:
<ul>
<li>GET: retrieve resources</li>
<li>POST: create resources</li>
<li>PUT: update resources</li>
<li>DELETE: delete resources</li>
</ul>







## Install Laravel APP
```bash
laravel new api
```







## Model and Migration
<ul>
<li>Let’s get started with our first Model, Migration and Factory for the Product.

```bash
php artisan make:model Product -m -f
```
The -m option is short for --migration, -f option is short for --factory and these tells Artisan to Generate a migration and factory for the model.
</li>

<li>Before actually writing your first migration, make sure you have a database created for this app and add credentials to the .env file located in the root of the project. 

```php
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=your_db_name
DB_USERNAME=your_username
DB_PASSWORD=your_password
```
</li>

<li>Let’s open the product migration <code>database/migrations/___create_products_table.php</code> and update the up() method as below

```php
    public function up()
    {
        Schema::create('products', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->bigInteger('user_id')->unsigned()->index();
            $table->foreign('user_id')->references('id')->on('users')->onDelete('cascade');
            $table->string('name');
            $table->longText('description');
            $table->integer('price')->default(0);
            $table->timestamps();
        });
    }
```
</li> 

<li>Let’s add those attributes to the $fillable field in the Product model class <code>app/Product.php</code>, that we can use them in our Product::create and Product::update.

```php
    protected $fillable = [
        'user_id', 'name', 'description', 'price',
    ];
```
</li> 

<li>Let’s go ahead and migrate 

```bash
php artisan migrate
```
</li> 
</ul>







## Authentication
There are many ways to implement API Authentication in Laravel. One of them being Passport, a great way to implement OAuth2. APIs typically use tokens to authenticate users and do not maintain session state between requests. Laravel makes API authentication a breeze using Laravel Passport, which provides a full OAuth2 server implementation for your Laravel application.
<ul>
<li>To get started, install Passport via the Composer package manager:
<pre>
composer require laravel/passport
</pre>
<li>Migrate your database after installing the package. The Passport migrations will create the tables your application needs to store clients and access tokens:
<pre>
php artisan migrate
</pre>
</li>

<li>Next, you should run the passport:install command. This command will create the encryption keys needed to generate secure access tokens. In addition, the command will create "personal access" and "password grant" clients which will be used to generate access tokens:
<pre>
php artisan passport:install
</pre>
</li>

<li>Next, edit <code>app/User.php</code> Model file. Add <code>use Laravel\Passport\HasApiTokens;</code> on top and Replace <code>use Notifiable;</code> with <code>use HasApiTokens, Notifiable;</code> in User class.

````php
namespace App;

use Laravel\Passport\HasApiTokens;
use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use HasApiTokens, Notifiable;
}
````
</li>


<li>Next, <code>use Laravel\Passport\Passport;</code> on top and update boot() method in <code>app/Providers/AuthServiceProvider.php</code> file with below:

``````php
use Laravel\Passport\Passport;

    public function boot()
    {
        $this->registerPolicies();

        Passport::routes();
    }
    
``````
</li>


<li>Finally, in your <code>config/auth.php</code> configuration file, you should set the driver option of the api authentication guard to passport

``````php

    'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'users',
        ],

        'api' => [
            'driver' => 'passport',
            'provider' => 'users',
            'hash' => false,
        ],
    ],
``````
</li>

<li>Lets create a controller for user register and login called AuthController:
<pre>
php artisan make:controller AuthController
</pre>
</li>

<li>Next, open AuthController and paste the following code into it: <code>app/Http/Controller/AuthController.php</code>

```php
namespace App\Http\Controllers;

use App\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Symfony\Component\HttpFoundation\Response;


class AuthController extends Controller
{
    public function register(Request $request)
    {
        $validatedData = $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|min:8|confirmed',
        ]);

        $user = User::create([
            'name' => $validatedData['name'],
            'email' => $validatedData['email'],
            'password' => Hash::make($validatedData['password']),
        ]);

        $accessToken = $user->createToken('authToken')->accessToken;

        return response([
            'user' => $user,
            'accessToken' => $accessToken,
        ], Response::HTTP_CREATED);
    }

    public function login(Request $request)
    {
        $validatedData = $request->validate([
            'email' => 'required|email',
            'password' => 'required',
        ]);

        if (!auth()->attempt($validatedData)) {
            return response([
                'data' => 'Invalid credentials',
            ], Response::HTTP_UNAUTHORIZED);
        }

        $accessToken = auth()->user()->createToken('authToken')->accessToken;

        return response([
            'user' => auth()->user(),
            'accessToken' => $accessToken,
        ], Response::HTTP_ACCEPTED);
    }
    
}
```
</li>
<li>Finally, Let’s create the authentication register and login route for our application. On the <code>routes/api.php</code> file, we can simply do this:
<pre>
Route::post('register', 'AuthController@register')->name('user.register');
Route::post('login', 'AuthController@login')->name('user.login');
</pre>
</li>
</ul>








## Relationships between Models
<ul>
<li>A user can add as many products as they wish, but a product can only belong to one user. So, the relationship between the User model and Product model is a one-to-many relationship.
Let’s define that. Paste the following code into Product model class: <code>app/User.php</code>

```php
    public function products()
    {
      return $this->hasMany(Product::class);
    }
```
</li> 

<li>Next, let’s define the inverse relationship on the Product class: <code>app/Product.php</code>

```php
    public function user()
    {
        return $this->belongsTo(User::class, 'user_id', 'id');
    }
```
</li>
</ul>







## Database Seeding
Database seeding is the process of filling up our database with dummy data that we can use to test it. Laravel comes with [Faker](https://github.com/fzaninotto/Faker), a great library for generating just the correct format of dummy data for us. Instead of manually specifying the value of each column when you create this test data, Laravel allows you to define a default set of attributes for each of your Eloquent models using model factories.
<ul>

<li>Open the ProductFactory <code>database/factories/ProductFactory.php</code> add <code>use App\User;</code> on top and update the return array as below:

```php
    return [
        'user_id' => function (){
            return User::all()->random();
        },
        'name' => $faker->word,
        'description' => $faker->paragraph,
        'price' => $faker->randomDigit,
    ];
```
</li>

<li>Once you have defined your factory, then open DatabaseSeeder <code>database/seeds/DatabaseSeeder.php</code> and update run() method as below:

```php
    public function run()
    {
        factory(App\User::class,10)->create();
        factory(App\Product::class,200)->create();
    }
```
</li>

<li>So let’s run the seed command: <code>php artisan db:seed</code></li>
</ul>







## Resource
Laravel API Resource classes allow you to expressively and easily transform your model/models collections into JSON.
<ul>
<li>Lets create a ResourceCollection. This will return a collection of resources and paginate response for ProductApiController index() method:

```bash
php artisan make:resource ProductCollection
```
</li>

<li>Once ProductCollection is created, let’s open it and update the toArray() method as below:

```php
    public function toArray($request)
    {
        return [
            'data' => $this->collection->transform(function ($product) {
                return [
                    'id' => $product->id,
                    'name' => $product->name,
                    'description'  => $product->description,
                    'price' => $product->price,
                ];
            })
        ];
    }
```
</li>


<li>Next, create a Resource. This will return JSON response for ProductApiController show() method:

```bash
php artisan make:resource ProductResource
```
</li>

<li>Let’s open ProductResource and update the toArray() method as below:

```php
    public function toArray($request)
    {
        return[
            'name' => $this->name,
            'description' => $this->description,
            'price' => $this->price,
            'user' => $this->user->name,
        ];
    }
```

</li>    

</ul>







## Exceptions Handler
<ul>
<li>
If you tried to fetch a non-existent resource, you’ll be thrown an exception and you’ll receive the whole stacktrace. We can fix that by editing our exception handler class, update render() method and use Response class on top, file located in <code>app/Exceptions/Handler.php </code> to return a JSON response:

```php
use Illuminate\Database\Eloquent\ModelNotFoundException;
use Symfony\Component\HttpKernel\Exception\NotFoundHttpException;
use Symfony\Component\HttpFoundation\Response;


    public function render($request, Exception $exception)
    {
        if($request->expectsJson()) {
            if($exception instanceof ModelNotFoundException) {
                return response()->json([
                    'data' => 'Resource not found'
                ], Response::HTTP_NOT_FOUND);
            }
        }


        if($exception instanceof NotFoundHttpException) {
            return response()->json([
                'data' => 'Route not found'
            ], Response::HTTP_NOT_FOUND);
        }

        return parent::render($request, $exception);
    }
```
</li>

<li>Let's create ownership check exception handler:

```bash
php artisan make:exception isOwnedHandler
```
</li>

<li>Next, open isOwnedHandler and paste the following code into it: <code>app/Exceptions/isOwnedHandler.php</code>

```php
namespace App\Exceptions;

use Symfony\Component\HttpFoundation\Response;
use Exception;

class isOwnedHandler extends Exception
{
    public function render()
    {
        return response([
            'data' => 'Product not belongs to this user'
        ], Response::HTTP_FORBIDDEN);
    }
}
```
</ul>







## Controller
<ul>
<li>To quickly generate an API resource controller that does not include the create or edit methods, use the --api switch when executing the make:controller command:

```bash
php artisan make:controller ProductApiController --api
```
</li>

<li>Next, open ProductController and paste the following code into it: <code>app/Http/Controller/ProductApiController.php</code>

```php
namespace App\Http\Controllers;

use App\Exceptions\isOwnedHandler;
use App\Http\Resources\ProductCollection;
use App\Http\Resources\ProductResource;
use App\Product;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;
use Symfony\Component\HttpFoundation\Response;

class ProductApiController extends Controller
{

    public function index()
    {
        return new ProductCollection(Product::orderBy('id', 'DESC')->paginate(10));
    }

    public function store(Request $request)
    {
        $request->validate([
            'name' => 'required|unique:products|max:255',
            'description' => 'required',
            'price' => 'required|numeric',
        ]);

        $product = new Product();
        $product->user_id = Auth::id();
        $product->name = $request->name;
        $product->description = $request->description;
        $product->price = $request->price;
        $product->save();

        return response([
            'data' => new ProductResource($product)
        ], Response::HTTP_CREATED);
    }

    public function show($id)
    {
        return new ProductResource(Product::findOrFail($id));
    }

    public function update(Request $request, $id)
    {
        $request->validate([
            'name' => 'required|unique:products|max:255',
            'description' => 'required',
            'price' => 'required|numeric',
        ]);

        $product = Product::findOrfail($id);
        $this->isOwned($product);
        $product->name = $request->name;
        $product->description = $request->description;
        $product->price = $request->price;
        $product->save();

        return response([
            'data' => new ProductResource($product)
        ], Response::HTTP_OK);
    }

    public function destroy($id)
    {
        $product = Product::findOrfail($id);
        $this->isOwned($product);
        $product->delete();

        return response([
            'data' => new ProductResource($product)
        ], Response::HTTP_OK);
    }

    public function isOwned($product)
    {
        if (Auth::id() !== $product->user_id) {
            throw new isOwnedHandler;
        }
    }

}
```
</li>
</ul>







## Route
Let’s create the basic endpoints for our application: create, retrieve the list, retrieve a single one, update, and delete. On the <code>routes/api.php</code> file, we can simply do this:
```php
Route::apiResource('product', 'ProductApiController')->middleware('auth:api');
```







## Endpoint Hit
<ul>
<li>Register 
<pre>
curl -X POST http://localhost:8000/api/register \
-H "Accept: application/json" \
-H "Content-Type: application/json" \
-d '{"name":"yourname","email":"mail@example.com","password":"12341234","password_confirmation":"12341234"}'
</pre></li>

<li>Login 
<pre>
curl -X POST http://localhost:8000/api/login \
-H "Accept: application/json" \
-H "Content-Type: application/json" \
-d '{"email":"mail@example.com","password":"12341234"}'
</pre></li>

<li>Create Product
<pre>
curl -X POST http://localhost:8000/api/product \
-H "Accept: application/json" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer access_token_here" \
-d '{"name":"iPhone","description":"Its an Apple product"}'
</pre></li>

<li>Update Product
<pre>
curl -X PUT http://localhost:8000/api/product/{product_id} \
-H "Accept: application/json" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer access_token_here" \
-d '{"name":"Google Pixel","description":"Its a Google product"}'
</pre></li>

<li>Product Collection Index
<pre>
curl -X GET http://localhost:8000/api/product \
-H "Accept: application/json" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer access_token_here"
</pre></li>

<li>Product Show By ID
<pre>
curl -X GET http://localhost:8000/api/product/{product_id} \
-H "Accept: application/json" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer access_token_here"
</pre></li>

<li>Product Delete
<pre>
curl -X DELETE http://localhost:8000/api/product/{product_id} \
-H "Accept: application/json" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer access_token_here"
</pre></li>
</ul>
