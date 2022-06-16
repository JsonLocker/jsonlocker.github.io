---
title: adapter 
date: 2021-05-28 18:50:04
categories: patterns 
tags:
    - php 
    - patterns
    - adapter
---

## 适配器模式

适配器模式，即根据客户端需要，将某个类的接口转换成特定样式的接口，以解决类之间的兼容问题。

如果我们的代码依赖一些外部的API，或者依赖一些可能会经常更改的类，那么应该考虑用适配器模式。

下面我们以集成支付宝支付功能为例。

### 1. 问题

假设支付宝支付类功能

```php
/**
 * 支付宝支付类
 */
class Alipay
{
    public function sendPayment()
    {
        echo '使用支付宝支付。';
    }
}

// 客户端代码
$alipay = new Alipay();
$alipay->sendPayment();

```
我们直接实例化Alipay类完成支付功能，这样的客户端代码可能很多。

一段时间后，如果支付宝的Alipay类升级，方法名由sendPayment()变成goPayment()会怎样？

所有用了sendPayment()的客户端代码都要改变。

如果Alipay类频繁升级，或者客户端在很多地方使用，这会是极大的工作量。

### 2. 解决


现在我们用适配器模式来解决。

我们在客户端和Alipay类之间加一个中间类，也就是适配器类，转换原始的Alipay为客户端需要的形式。

为让客户端能调用到统一的类方法，我们先定义一个适配器接口：

```php

/**
 * 适配器接口，所有的支付适配器都需实现这个接口。
 * 不管第三方支付实现方式如何，对于客户端来说，都
 * 用pay()方法完成支付
 */
interface PayAdapter
{
    public function pay();
}

```

因为Alipay类我们无法控制，而且它有可能经常更新，所以我们不对它做任何修改。

我们新建一个AlipayAdapter适配器类，在pay()中转换Alipay的支付功能，如下：

```php

/**
 * 支付宝适配器
 */
class AlipayAdapter implements PayAdapter
{
    public function pay()
    {
        // 实例化Alipay类，并用Alipay的方法实现支付
        $alipay = new Alipay();
        $alipay->sendPayment();
    }
}

```

客户端使用方式：


```php

// 客户端代码
$alipay = new AlipayAdapter();
// 用pay()方法实现支付
$alipay->pay();

```

这样，当Alipay的支付方法改变，只需要修改AlipayAdapter类就可以了。

### 3. 适配新类

有了适配器后，扩展也变得更容易了。

继续以上的例子，在支付宝的基础上，我们再增加微信支付，它与支付宝的支付方式不同，必须通过扫码才能支付。

这种情况也应该使用适配器，而不是直接使用微信的支付功能。

代码如下：

```php

/**
 * 微信支付类
 */
class WechatPay
{
    public function scan()
    {
        echo '扫描二维码后，';
    }

    public function doPay()
    {
        echo '使用微信支付';
    }
}

/**
 * 微信支付适配器
 */
class WechatPayAdapter implements PayAdapter
{
    public function pay()
    {
        // 实例化WechatPay类，并用WechatPay的方法实现支付。
        // 注意，微信支付的方式和支付宝的支付方式不一样，但是
        // 适配之后，他们都能用pay()来实现支付功能。
        $wechatPay = new WechatPay();
        $wechatPay->scan();
        $wechatPay->doPay();
    }
}

```

客户端使用：

```php

// 客户端代码
$wechat = new WechatPayAdapter();
// 也是用pay()方法实现支付
$wechat->pay();

```

这就是适配器的扩展特性。

我们创建了一个用于处理第三方类（支付宝、微信支付）的方法，

如果它们的API有变化，我们仅需修改客户端依赖的适配器类就可以，不用修改、暴露第三方类本身。


注意：适配器模式中，适配器类的名称和创建方式一定是不会频繁改动的。

对于客户端来说，引用适配器类的方式应该是统一而不变的，这才算是正确使用适配器。

### 4. 总结

大的应用都会不断地加入新库和新API。

为避免它们的变更引发问题，应该用适配器模式包装起来，提供应用统一的引用方式。

它会让我们的代码更具结构化，便于管理和扩展。
