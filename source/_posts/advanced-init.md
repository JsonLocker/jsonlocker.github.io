---
title: advanced_init
date: 2019-10-11 15:49:19
categories: yii2 
tags:
    - yii2 
    - mailer 
    - advanced 
---

##  1. 安装yii2 高级模板

好久没用yii2高级模板了,今天安装下发现和以往不同的地方是注册会要求邮箱验证,后面才发现已经默认集成了
mailer组件.这里稍微做个备注.

#### 1 . 用composer 安装模板

```
composer create-project --prefer-dist yiisoft/yii2-app-advanced yii-application
```

#### 2. 进行配置
```
php init --env=Development --overwrite=All

```
#### 3. 新建一个数据库如demo ,然后进入`/common/config/main-local.php` 配置数据库参数

```php
return [
    'components' => [
        'db' => [
            'class' => 'yii\db\Connection',
            'dsn' => 'mysql:host=127.0.0.1;dbname=lessons',
            'username' => 'root',
            'password' => 'password',
            'charset' => 'utf8',
        ],
    ],
...
]

```

#### 4. 生成基本数据库

`php migrate`

#### 5. 进入 `/user/local/nginx/conf` 配置基本信息

```
server {
	charset utf-8;
        client_max_body_size 128M;

        listen 80; ## listen for ipv4
        #listen [::]:80 default_server ipv6only=on; ## listen for ipv6

        server_name frontend.test;
        root        /path/to/yii-application/frontend/web/;
        index       index.php;

        access_log  /path/to/yii-application/log/frontend-access.log;
        error_log   /path/to/yii-application/log/frontend-error.log;

        location / {
            # Redirect everything that isn't a real file to index.php
            try_files $uri $uri/ /index.php$is_args$args;
        }

        # uncomment to avoid processing of calls to non-existing static files by Yii
        #location ~ \.(js|css|png|jpg|gif|swf|ico|pdf|mov|fla|zip|rar)$ {
        #    try_files $uri =404;
        #}
        #error_page 404 /404.html;

        # deny accessing php files for the /assets directory
        location ~ ^/assets/.*\.php$ {
            deny all;
        }

        location ~ \.php$ {
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_pass 127.0.0.1:9000;
            #fastcgi_pass unix:/var/run/php5-fpm.sock;
            try_files $uri =404;
        }
    
        location ~* /\. {
            deny all;
        }
}
     
server {
        charset utf-8;
        client_max_body_size 128M;
    
        listen 80; ## listen for ipv4
        #listen [::]:80 default_server ipv6only=on; ## listen for ipv6
    
        server_name backend.test;
        root        /path/to/yii-application/backend/web/;
        index       index.php;
    
        access_log  /path/to/yii-application/log/backend-access.log;
        error_log   /path/to/yii-application/log/backend-error.log;
    
        location / {
            # Redirect everything that isn't a real file to index.php
            try_files $uri $uri/ /index.php$is_args$args;
        }
    
        # uncomment to avoid processing of calls to non-existing static files by Yii
        #location ~ \.(js|css|png|jpg|gif|swf|ico|pdf|mov|fla|zip|rar)$ {
        #    try_files $uri =404;
        #}
        #error_page 404 /404.html;

        # deny accessing php files for the /assets directory
        location ~ ^/assets/.*\.php$ {
            deny all;
        }

        location ~ \.php$ {
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_pass 127.0.0.1:9000;
            #fastcgi_pass unix:/var/run/php5-fpm.sock;
            try_files $uri =404;
        }
    
        location ~* /\. {
            deny all;
        }
}

```

#### 6. 配置域名指向

```
127.0.0.1   frontend.test
127.0.0.1   backend.test

```

##  二、 配置邮件参数

#### 1. 配置common参数

这里主要是配置mailer，因为默认mailer不能发送邮件，造成前台注册无法验证

```php
# vim common/config/main-local.php

return [
    'components' => [
        'db' => [
            'class' => 'yii\db\Connection',
            'dsn' => 'mysql:host=127.0.0.1;dbname=lessons',
            'username' => 'root',
            'password' => 'password',
            'charset' => 'utf8',
        ],
        
        'mailer' => [
            'class' => 'yii\swiftmailer\Mailer',
            'viewPath' => '@common/mail',
            //set this property to false to send mails to real email addresses
            'useFileTransport' => false,
            'transport' => [
                'class' => 'Swift_SmtpTransport',
                'host' => 'smtp.qq.com',
                'username' => 'slq17@qq.com',
		//非邮箱密码，是邮箱的授权码,
                'password' => 'wsdfszbhsqizbigb', 
                'port' => '587',
                'encryption' => 'tls',
            ],
            'messageConfig' => [
                'charset' => 'UTF-8',
		'from' => [Yii::$app->params['adminEmail'] => '系统邮件']
            ],
        ],
    ],
];

```

#### 2. 设置发送邮箱

记得把frontend和backend里面的email清空了,只用common的config里的配置

```php
# vim common/config/param.php
return [
    'adminEmail' => 'slq17@qq.com',
    'user.passwordResetTokenExpire' => 3600,
];

```

#### 3. 配置注册文档

后续发送邮件可致敬<del>照抄</del>这里的代码啦

```php
# vim frontend/models/SignupForm.php


    /**
     * Signs user up.
     *
     * @return bool whether the creating new account was successful and email was sent
     */
    public function signup()
    {
        if (!$this->validate()) {
            return null;
        }

        $user = new User();
        $user->username = $this->username;
        $user->email = $this->email;
        $user->setPassword($this->password);
        $user->generateAuthKey();
        $user->generateEmailVerificationToken();
        $res = $this->sendEmail($user);
        return $user->save() && $this->sendEmail($user);

    }



    /**
     * Sends confirmation email to user
     * @param User $user user model to with email should be send
     * @return bool whether the email was sent
     */
    protected function sendEmail($user)
    {
        return Yii::$app
            ->mailer
            ->compose(
                ['html' => 'emailVerify-html', 'text' => 'emailVerify-text'],
                ['user' => $user]
            )
	    // 把这句注释掉，上面配置好了，以后也省得写
            //->setFrom([Yii::$app->params['adminEmail'] => Yii::$app->name . ' robot'])
            ->setTo($this->email)
            ->setSubject('Account registration at ' . Yii::$app->name)
            ->send();
    }

```
到此advanced模板包括邮件配置完成.
