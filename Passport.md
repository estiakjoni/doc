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

## Resources
Resources will be the targets of the actions, in our case Product and they have their own endpoints:
<ul>
<li>/products</li>
</ul>

## HTTP Status Codes and the Response Format
This lets us explicitly return JSON data as well as send an HTTP code that can be parsed by the client. The most common codes you’ll be returning will be:
<ul>
<li>200: OK. The standard success code and default option.</li>
<li>201: Object created. Useful for the store actions.</li>
<li>204: No content. When an action was executed successfully, but there is no content to return.</li>
<li>206: Partial content. Useful when you have to return a paginated list of resources.</li>
<li>400: Bad request. The standard option for requests that fail to pass validation.</li>
<li>401: Unauthorized. The user needs to be authenticated.</li>
<li>403: Forbidden. The user is authenticated, but does not have the permissions to perform an action.</li>
<li>404: Not found. This will be returned automatically by Laravel when the resource is not found.</li>
<li>500: Internal server error. Ideally you're not going to be explicitly returning this, but if something unexpected breaks, this is what your user is going to receive.</li>
<li>503: Service unavailable. Pretty self explanatory, but also another code that is not going to be returned explicitly by the application.</li>


## New Laravel app
<ul>
<li>We’ll start by creating a new Laravel app called <code>api</code>, To create Laravel project using the following command:
<pre>laravel new api</pre>
or
<pre>composer create-project --prefer-dist laravel/laravel api</pre>
</li>

<li>After the project creation finishes, you should be able to start the server and test if everything is working:
<pre>php artisan serve</pre>
</li>


## Product Model and Migrations
<ul>
<li>Let’s get started with our first Controller, Model, Migration and Factory for the Product.
<pre>php artisan make:model Product -a</pre>
The -a option is short for --all and it tells Artisan to Generate a model, migration, factory, and resource controller for the model.
</li>

<li>We’ll do the same for the Rating model
<pre>
php artisan make:model Rating -a
</pre>
</li>

<li>
Before actually writing your first migration, make sure you have a database created for this app and add credentials to the .env file located in the root of the project.
<pre>
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=your_db_name
DB_USERNAME=your_username
DB_PASSWORD=your_password
</pre>
</li>

<li>
Let’s open the product migration file and update the up() method as below: <code>database/migrations/__create_products_table.php</code>
<pre>
    public function up()
    {
        Schema::create('products', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->bigInteger('user_id')->unsigned()->index();
            $table->foreign('user_id')->references('id')->on('users')->onDelete('cascade');
            $table->string('name');
            $table->longText('description');
            $table->timestamps();
        });
    }
</pre>
</li>

<li>Next, open the rating migration file and update the up() method as below: <code>database/migrations/__create_ratings_table</code>
<pre>
    public function up()
    {
        Schema::create('ratings', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->bigInteger('product_id')->unsigned()->index();
            $table->foreign('product_id')->references('id')->on('products')->onDelete('cascade');
            $table->integer('rating');
            $table->timestamps();
        });
    }
</pre>
</li>

<li>Let’s add those attributes to the $fillable field in the Product model, that we can use them in our Product::create and Product::update. Add the code below in the Product class: <code>app/Product.php</code>
<pre>
class Product extends Model
{
    protected $fillable = [
        'user_id', 'name', 'description',
    ];
}
</pre>
</li>


<li>Add those attributes to the $fillable field in the Rating model, that we can use them in our Rating::create and Rating::update. Add the code below in the Rating class: <code>app/Rating.php</code>
<pre>
class Rating extends Model
{
    protected $fillable = [
        'product_id', 'rating',
    ];
}
</pre>
</li>
<li>Let’s go ahead and migrate
<pre>
php artisan migrate
</pre>
</li>
</ul>



## Relationships between Models
<ul>
<li>A user can add as many products as they wish, but a product can only belong to one user. So, the relationship between the User model and Product model is a one-to-many relationship. Let’s define that. Add the code below inside the User class: <code>app/User.php</code>
<pre>
    public function products()
    {
      return $this->hasMany(Product::class);
    }
</pre>
</li>
<li>Next, let’s define the inverse relationship on the Product class: <code>app/Product.php</code>
<pre>
    public function user()
    {
        return $this->belongsTo(User::class, 'user_id', 'id');
    }
</pre>
</li>

<li>Likewise, a product can be rated by various users, hence a product can have many ratings. A rating can only belong to one product. This is also a one-to-many relationship. Add the code below in the Product class: <code>app/Product.php</code>
<pre>
    public function ratings()
    {
      return $this->hasMany(Rating::class);
    }
</pre>
</li>
<li>Then we define the inverse relationship inside the Rating class: <code>app/Rating.php</code>
<pre>
    public function product()
    {
      return $this->belongsTo(Product::class);
    }
</pre>
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



## Database Seeding (if require)
Database seeding is the process of filling up our database with dummy data that we can use to test it. Laravel comes with [Faker](https://github.com/fzaninotto/Faker), a great library for generating just the correct format of dummy data for us. Instead of manually specifying the value of each column when you create this test data, Laravel allows you to define a default set of attributes for each of your Eloquent models using model factories.
<ul>

<li>Open the ProductFactory file generated for the Product model, <code>use App\User;</code> on top and update the return array as below: <code>database/factories/ProductFactory.php</code>
<pre>
    return [
        'user_id' => function (){
            return User::all()->random();
        },
        'name' => $faker->word,
        'description' => $faker->paragraph,
    ];
</pre>
</li>

<li>Let’s repeat the process, open the RantingFactory file generated for the Ranting model, <code>use App\Product;</code> on top and update the return array as below: <code>database/factories/RatingFactory.php</code>
<pre>
    return [
        'product_id' => function (){
            return Product::all()->random();
        },
        'rating' => $faker->numberBetween(0,5),
    ];
</pre>
</li>
<li>Once you have defined your factories, then open DatabaseSeeder and update run() method as below: <code>database/seeds/DatabaseSeeder.php</code>
<pre>
    public function run()
    {
        factory(App\User::class,1)->create();
        factory(App\Product::class,15)->create();
        factory(App\Rating::class,45)->create();
    }
</pre>
</li>
<li>So let’s run the seed command: <code>php artisan db:seed</code></li>
</ul>


## Product Routes, Resource and Controller
<ul>
<li>Let’s create the basic endpoints for our application: create, retrieve the list, retrieve a single one, update, and delete. On the routes/api.php file, we can simply do this:
<pre>
Route::apiResource('product', 'ProductController')->middleware('auth:api');
</pre>
</li>

<li>We can check route list by run this
<pre>
php artisan route:list
</pre></li>

<li>Laravel's resource classes allow you to expressively and easily transform your models and model collections into JSON. To generate a resource class run:
<pre>
php artisan make:resource ProductResource
</pre>
</li>

<li>Once ProductResource is created, let’s open it and update the toArray() method as below: <code>Resources/ProductResource.php</code>
<pre>
    public function toArray($request)
    {
        //return parent::toArray($request);
        return [
            'user' => $this->user->email,
            'name' => $this->name,
            'description' => $this->description,
            'rating' => $this->ratings->count() > 0 ? round($this->ratings->sum('rating')/$this->ratings->count(),2) : 'No rating yet',
        ];
    }
</pre>
</li>

<li>Next, open ProductController and paste the following code into it: <code>app/Http/Controller/ProductController.php</code>

````php
namespace App\Http\Controllers;

use App\Exceptions\UserCheckHandler;
use Illuminate\Support\Facades\Auth;
use Symfony\Component\HttpFoundation\Response;
use App\Http\Resources\ProductResource;
use App\Product;
use Illuminate\Http\Request;

class ProductController extends Controller
{
    public function index()
    {
        return ProductResource::collection(auth()->user()->products()->latest()->paginate(10));
    }

    public function store(Request $request)
    {
        $request->validate([
            'name' => 'required|string|max:255|unique:products',
            'description' => 'required',
        ]);

        $product = Product::create([
            'user_id' => auth()->user()->id,
            'name' => $request->name,
            'description' => $request->description,
        ]);

        return response([
            'data' => new ProductResource($product)
        ], Response::HTTP_CREATED);

    }

    public function show(Product $product)
    {
        $this->UserCheck($product);
        return new ProductResource($product);
    }

    public function update(Request $request, Product $product)
    {
        $this->UserCheck($product);
        $product->update($request->only(['name', 'description']));
        return response([
            'data' => new ProductResource($product)
        ], Response::HTTP_CREATED);
    }


    public function destroy(Product $product)
    {
        $this->UserCheck($product);
        $product->delete();
        return response(null, Response::HTTP_NO_CONTENT);
    }


    protected function UserCheck($product)
    {
        if (Auth::id() !== $product->user_id) {
            throw new UserCheckHandler;
        }
    }
}
````

</li>
</ul>

## Handle Exceptions
<ul>
<li>If you tried to fetch a non-existent resource, you’ll be thrown an exception and you’ll receive the whole stacktrace. We can fix that by editing our exception handler class, update render() method  and use Response class on top, file located in <code>app/Exceptions/Handler.php</code> to return a JSON response:

````php
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
````
</li>

<li>Let's create user check exception handler and paste below codes: <code>app/Exceptions/UserCheckHandler.php</code>

`````php
namespace App\Exceptions;


use Exception;
use Symfony\Component\HttpFoundation\Response;

class UserCheckHandler extends Exception
{
    public function render()
    {
        return response([
            'data' => 'Product not belongs to this user'
        ], Response::HTTP_FORBIDDEN);
    }
}
`````

</li>
</ul>


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
