------
title: yii2_asset
date: 2018-06-14 22:13:52
categories: yii2
tags:
    - yii2
    - asset
------

## 统一控制权
主要集成一般的方法
==原则是: 能减少代码量，经常使用==

- 在根目录新建文件夹common
- 在上面的common目录建立一个**组件** 文件夹命名为`components`
- 在components文件夹里面建立文件 `BaseWebController.php`

```php
namespace app\common\components;

use app\web\Controller;

/**
* 集成常用公用方法，提供给所有controller使用
* get,post,setCookie,getCookie,removeCookie,renderJson
*/

class BaseWebController extends Controller
{
    
    # 关闭CSRF攻击开关关闭
    public $enableCsrfValidation = false;


    # 获取http的get参数
    public function get($key,$default_val=''){
        return \Yii::$app->request->get($key,$default_val);
    }

    # 获取http的post参数
    public function post($key,$default_val){
        return \Yii::$app->request->post($key,$default_val);
    }

    # 设置cookie值
    public function setCookie($name,$value,$expire=0){
        $cookie = \Yii::$app->response->cookies;
        $cookie->add(new \yii\web\Cookie([
            'name' => $name,
            'value' => $value,
            'expire' => $expire
        ]));
    }

    # 获取cookie
    public function getCookie($name,$default_val=''){
        $cookies = \Yii::$app->request->cookies;
        return $cookies->getValue($name,$default_val);
    }

    # 删除cookie
    public function removeCookie($name){
        $cookies = \Yii::$app->response->cookies;
        $cookie->remove($name);
    }

    # api 统一返回json格式方法
    public function renderJson($data=[],$msg='ok',$code=200){
        header("Content-type:application/json");

        echo json_encode([
            'code' => $code,
            'msg' => $msg,
            'data' => $data,
            'req_id' => uniqid()  #随机生成序列号,php本身方法可以保证序列号唯一
        ]);
    }
}
```


然后把controllers下面里面的文件继承类名称都改成BaseWebController

```php
namespace app\controllers;

use app\common\components\BaseWebController;

class DefaultController extends BaseWebController{

    public function actionIndex(){

        return $this->render("index");
    }
}
```

## 统一链接管理

- 在根目录新建文件夹common
- 在上面的common目录建立一个**服务**文件夹命名为`services`
- 在 services 文件夹里面建立文件 `UrlService.php`

```php
namespace app\common\services;

use yii\helpers\Url;

/**
* 本类只负责构建链接
* 方便统一管理调用
*/

class UrlService
{
    // 构建modules 里的 web模块的所有链接
    public function buildWebUrl($url,$params=[]){
        $domain-config = \Yii::$app->params['domain'];
        $path = Url::toRoute(array_merge( [$path],$params ))
        return $domain_config['web'].$path;
    }

    //构建会员端链接
    public static function buildMUrl($url,$params=[]){
        $domain-config = \Yii::$app->params['domain'];
        $path = Url::toRoute(array_merge( [$path],$params ))
        return $domain_config['m'].$path;
    }

    //构建默认访问的www链接
    public static  function buildWwwUrl($url,$params=[]){
        $domain-config = \Yii::$app->params['domain'];
        $path = Url::toRoute(array_merge( [$path],$params ))
        return $domain_config['www'].$path;
    }

    //构建空链接，比如很多a标签想设置为空,让它不要跳转
    public static function buildNullUrl(){
        return 'javascript:void(0);';
    }

}
```
然后去前端view页面里面去修改如下

```php
<?php
use app\common\services\UrlService;
?>
<img src="<?=UrlService::buildWwwUrl("/images/common/grcode.jpg");?>">

```

在`config/params`配置项里配置域名名称

```
return [
    "domain" => [
        'www' => 'http://wx.sonlq.com';
        'web' => 'http://wx.sonlq.com/web';
        'm' => 'http://wx.sonlq.com/m';
    ]
];
```



