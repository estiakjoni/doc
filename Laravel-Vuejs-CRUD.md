## Install npm
```bash
npm install
```

## Install Auth
```bash
php artisan make:auth
```

## Invokable Controller
```bash
php artisan make:controller ProductFrontEnd -i
```

## web Route
```php
Route::get('/product', 'ProductFrontEnd')->name('Product View');
``` 


## Create blade
```php
resources/views/product.blade.php
```

## Create component
```php
resources/js/components/ProductComponent.vue
```

## Register component in
location: <code>resources/js/app.js</code>
```javascript
Vue.component('product-component', require('./components/ProductComponent.vue').default);
```

## Compile new component
```bash
npm run dev
```

## Run watch
```bash
npm run watch
```

## vue-progressbar

https://github.com/hilongjw/vue-progressbar


## vue-snotify

https://github.com/artemsky/vue-snotify


## vform

https://github.com/cretueusebiu/vform
