---
title: laravel-2-route
date: 2022-02-09 17:15:50
categories:
	- php
tags:
	- laravel
	- route
---

## 基础路由

```php

// 闭包
Route::get('foo', function(){
	return 'hello world';
});

// 控制器
Route::get('/user', 'UserController@index');

// 带参数
Route::get('posts/{postid}', function($postid){
	//
});

```


## 参数

```php
Route::get('posts/{postid}/comment/{commnetid}', function($pid, $cid){
	//
});


//可选参数必须设置默认值
Route::get('users/{name?}', function($name='Json'){
	return $name;
});


# 局部参数约束
Route::get('user/{id}', function($id){
	//
})->where('id', '[0-9]+');

Route::get('user/{id}/{name}', function($id,$name){
	//
})->where(['id' => '[0-9]+'], 'name'=>'[a-z]+');


# 全局约束
// RouteServiceProvider

public function boot(){

	Route::patter('id', '[0-9]+');
	parent::boot();
}

Route::get('user/{id}', function(){
	// id必须是整数才能执行这里
});

```

## 参数绑定

隐式绑定

```php

# 源地址的{}中的变量(即:user)和传参名必须完全一致
Route::get('api/users/{user}', function(App\User $user){
	return $user->email;
});


# 自定义键名，在模型中修改 (默认指数据库的主键id)
# App/User.php

public function getRouteKeyName(){
	return 'slug';
	// api/users/2
	// select * from `users` where `slug`=2 limit 1;
}

```

显示绑定

```php

# RouteServiceProvider

public function boot(){
	parent::boot();

	Route::model('user', App\User::class);
}

Route::get('profile/{user}', function($user){
	//
})


```

自定义解析逻辑

```php

// 在 app/Prividers/RouteService/Provider.php 中

public function boot(){
	parent::boot();

	Route::bind('user', function($user){
		return App\User::where('name', $value)->first() ?? abort(404);
	});
}
```

```php

在 app/User.php中

public function resolverRouteBinding($value){
	return $this->where('name', $value)->first() ?? abort(404);
}

```

## http 请求方法

单个方法

```php

Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);   //全体更新
Route::patch($uri, $callback); //局部更新
Route::delete($uri, $callback);
Route::options($uri, $callback); //允许客户端检查性能

Route::resource('photos', 'PhotoController');

```

|方法|uri|路由名称|控制器@方法|
|--|--|--|--|
|Get|photos|photos.index|PhotoController@index|
|Post|photos|photos.store|PhotoController@store|
|Get|photos/create|photos.create|PhotoController@create|
|Get|photos/{photo}|photos.show|PhotoController@show|
|Put/PATCH|photos/{photo}|photos.update|PhotoController@update|
|Delete|photos/{photo}|photos.destory|PhotoController@destory|
|Get|photos/{photo}/edit|photos.edit|PhotoController@edit|

```php

* Get /photos
index()   //展示照片

* Post /photos
store()  //添加照片

* Get /photos/create
create()

* Get /photos/{id}
show($id)

* Put /photos/{id}
update($id)

* Delete /photos/{id}
destory($id)

* Get /photos/{id}/edit

```

## 组合

```php

Route::any($uri, $callback);

Route::match(['get','post'], '/ ', function(){
	//
})

```

注意： web路由里的POST,PUT,DELETE方法，在提交表单时必须加上CSRF参数。

## 表单伪造

```html

<input type="hidden" name="_method" value="PUT" />
// 或者 @method('PUT')

```

## 命名路由

```php

Route::get('user/profile', function (){
	//
})->name('profile');


//使用

$url = route('profile');
return redirect()->route('profile');


// 带参数情况

Route::get('user/{id}/profile', function($id){
	//
})->name('profile');

$url = route('profile', ['id' => 1]);

```

## 根据需求丰富路由

命名空间

```php

Route::namespace('Admin')->get('/user', 'UserController@index');
// Admin/UserController@index

ROute::namespace('Admin')->get('user2', 'UserController@user2')
// Admin/UserController@user2

```

子域名路由

```php

Route::domain('{account}.myapp.com')->group(function(){
	Route::get('user/{id}', function($account, $id){
		//
	})
});

```

路由前缀

```php

Route::prefix('admin')->group(function(){
	Route::get('ser', function(){
		
	})
})

//相当于

Route::get('admin/users', function(){
});

```

路由命名前缀

```php

Route::name('admin.')->group(function(){
	
	 Route::get('users', function(){
	})->name('users');  //name('admin.users')
});
```

## 添加中间件

```php

Route::middleware('throttle:60,1')->group(function(){

	Route::get('/user', function(){
		//
	})
})


Route::get('/user', function(){
	//
})->middleware('auth:api');


Route::middleware(['first,'second'])->group(function(){

	Route::get('/', function(){
		//
	});

	Route::get('user/profile', function(){
		//
	})
})

```

## 简化路由

```php

# 重定向路由
Route::redirect('/here', '/there');
Route::permanentRedirect('/here', '/there'); //301
Route::redirect('/here', '/there', 301);  // 不写第三个参数默认302

# 只需返回一个视图
Route::view('/welcome', 'welcome');
Route::view('/welcome', 'welcome', ['name' => 'Taylor']);

```

## 默认路由

```php

// 一定要放所有路由最后面
Route::fallback(function(){
	// 404请求
})

```

## 获取当前路由信息

```php

// 假设有路由 Route::get('/', 'TestController@test')->name('mytest');

$route = Route::current();     // 返回object(Illuminate\Routing\Route)
$name = Route::currentRouteName();   // 返回mytest

$action = Route::currentRouteAction();  //控制器中返回 App\Http\Controllers\TestController@test ,闭包中返回null
 

```

