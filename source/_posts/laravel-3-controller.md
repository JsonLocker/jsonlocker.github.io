---
title: laravel-3-controller
date: 2022-02-11 10:26:23
categories:
	- php 
tags:
	- laravel
---

>* 定义控制器可以不继承controllers，这样不能使用middleware,validate,dispatch等方法

## 控制器内只有1个方法

```php

// Route::get('user/{id}', 'ShowProfile');
// php artisan make:controller ShowProfile --invokable

public function __invoke($id)
{
	return view('user.profile', ['user' => User::findOrFail($id)]);
}

```

## 中间件

路由中间件

`Route::get('profile', 'UserController@show')->middleware('auth');`

控制器中间件

```php

$this->middleware('auth');

$this->middleware('auth')->only('index');
$this->middleware('auth')->except('store');
$this->middleware(function($request, $next){
	return $next($request);
});

```

## Resource 控制器

```php

php artisan make::controller PhotoController -- resource
php artisan make::controller PhotoController -- resource --model=Photo

Route::resource('photos', 'PhotoController');
Route::apiResource('photos', 'PhotoController');  //没有create和edit

Route::resource([
	'photos' => 'PhotoController',
	'posts' => 'PostController'
]);

Route::apiResource([
	'photos' => 'PhotoController',
	'posts' => 'PostController'
]);

Route::resource('photos', 'PhotoController')->names([
	'create' => 'photos.build'
]);  //重命名路由名称


Route::resource('photo', 'PhotoController', ['only' => ['index', 'show']]);

Route::resource('photo', 'PhotoController', [
	'except' => ['create', 'store','update','destory']
]);

```


| Verb | Uri | Action | Route Name |
| --- | ---- | ---- | ---- | ---- |
| Get | photos | index | photos.index |

| Get|photos/create|create|photos.create|
| Post|photos|store|photos.store|
| Get|photos/{photo}|show|photos.show|
| Get|/photos/{photo}/edit|edit|photos.edit|
| Put/Patch|/photos/{photo}|update|photos.update|
| Delete|/photos/{photo}|destory|photos.destory|


## 命名 resource 路由的参数

默认情况下, Route::resource 会根据资源名称的单数形式创建资源路由的路由参数，你可以在选项数组中传入paramters参数轻松覆盖每个职业。
parameter数组应该是资源名称和参数名称的关联数组。

```php

Route::resource('user', 'PhotoController', [

	'parameters' => [
		'photo' => 'photo_in_phone'
	]
]);

```

重命名所有的动词名 create,edit

```php

Route::resourceVerbs([
	'create' => 'crear',
	'edit' => 'editar'
])

```

>* 如果想加入其他控制器放，尽量写在resource控制器路由之前，否则可能会被resource控制器的路由覆盖。

写的控制器要专一，如果需要典型的resource操作之外的方法，可以考虑将你的控制器分为两个更小的控制器。

## 参数必须在依赖注入之后传入

```php

# Route::put('user/{id}', 'UserController@update')

public function update(Request $request, $id){
	
	// 上面的Request $request 相当于 $request = new Resquest();
	$url = $request->url();
}

```
