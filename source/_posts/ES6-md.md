---
title: ES6.md
date: 2019-09-09 08:28:46
categories: 
tags: 
	- javascript  
	- ES6
---


### 1.块作用域 let

定义的变量只在本作用域内有效

```javascript
'use strict';

if(true){
    var fruit = 'apple';  // 可用 
    let fruit = 'apple';  // 错误,只作用于本定于块内
}


console.log(fruit);
```

### 2.恒量 const

无法重新定义值,是限制分配值的动作，不是限制值

```javascript
'use strict';

const fruit = 'apple';
console.log(fruit);

const fruit = 'orange';
console.log(fruit); //报错,提示重复赋值

const fruits = [];
fruits.push('🍎');
console.log(fruits);  //给数值赋值不报错
```

### 3.解构对象

函数返回的是个对象,用解构方法可用直接进行赋值，如下:

```javascript
'use strict';

function breakfast(){
    return {fruit:'fruit', drink:'drink', desert:'dessert'};
}

// 原提取方法 
var tem = breakfast();
    desert = tem[2], drink=tmp[1], fruit=tmp[0];

//解构获取
let [desert, drink, fruit] = breakfast();
// let {fruit:fruit, drink:drink, fruit:fruit} = breakfast();

console.log(desert, drink, fruit);
```


#### 4. 模板字符串

在 `${variable}` 中变量可以是表达式,也可以直接回车换行多行显示

```javascript
'use strict';

let desert = 'cake',
    drink = 'orange';
    
// old
let breakfast = "Today' breakfast is " + desert + "&" + drink;

// new
let breakfast = `Today's breakfast is ${desert} and ${drink}`; 

console.log(breakfast);
```

### 5. Tagged Template - 带标签模板

```javascript
'use strict';

let desert = 'cake',
    drink = 'orange';
let breakfast = kitchen`Today's breakfast is ${desert} and ${drink}`;

fuction kitchen(strings, ...values){
    console.log(strings);
    console.log(values);

    let result = '';
    for(let i=0; i<values; i++){
        result += string[i];
        result += values[i];
    }
    result += strings[strings.length-1];

    return result;
}

console.log(breakfast);
```

### 6. 判断是否含有其他字符串

```javascript
use 'strict';

let breakfast = 'Today is fine';

// startsWith 以某个字符串开头
console.log(breakfast.startsWith('today'));  

// endsWith 以某个字符串结尾
console.log(breakfast.endsWith('today'));

// includes 判断是否包含某个字符串
console.log(breakfast.includes('fine'));

```

### 7. default parameter values - 默认参数

```javascript
use 'strict';

function breakfast(desert = 'orange', drink = 'water'){
    return `${desert} & ${drink}`;
}

console.log(breakfast());
```

### 8.Spread - 展开操作符

```javascript
use 'strict';

let fruits = ['apple', 'banana'],
    food = ['cake', ...fruits];   // 把fruits放入food数组内

console.log(fruits);     // 返回数组 ['apple', 'banana']
console.log(...fruits);  // 输出单独数组内的每一个值  apple banana
console.log(food);     // 返回数组 ['cake', 'apple', 'banana']

```

### 9. Rest - 剩余操作符

```javascript
use 'strict';

// ...foods表示后续存入的参数都存放入foods这个参数
function breakfast(desert, drink, ...foods){
    console.log(desert, drink, foods);
    console.log(desert, drink, ...foods);
}

breakfast("a", "b", "c", "d");   // return "a", "b", ["c", "d"]
breakfast("a", "b", "c", "d");   // return "a", "b", "c", "d"
```

### 10. name属性 - 函数都名字

```javascript
use 'strict';

let funName = function (argument){
    //body ...
}

console.log(funName.name);    // return funcName

let B = function myName(argument){
    //body ...
}
console.log(B.name);    // return myName

```

### 11.Arrow Functions - 箭头函数　

```javascript

//new 箭头前为参数,后为返回内容
let breakfast = (desert, drink)=> {
    return desert + drink;
};

//old
var  breakfast = function breakfast(desert, drink){
    return desert + drink;
}
```

### 12. 对象表达式

```javascript
'use strict';

let desert = '🍰', fruit = '🍎';

let food = {

    desert,      //等同 desert:desert 
    fruit,
    breakfast() {}   // 等同原来breakfast:function(){}
};

console.log(food);  
```

### 13. 对象属性名

```javascript
'use strict';

let food = {};
let drink = 'hot drink';

food.desert = '🍰';
food.drink = '🍶';   //变量有空格会报错
food[drink] = '🍶';  // 正常

console.log(food);
```

### 14. Object.is() - 判断2个值是否相等

```javascript
'use strict';

console.log(NaN == NaN);            // false
console.log(Object.is(NaN, NaN));   //true
```

### 15.Object.assign() - 把一个对象的值复制到另外一个对象

```javascript
'use strict';

let breakfast = {};

Object.assign(
    breakfast,
    {drink:'🍺'}
);

console.log(breakfast);
```

### 16. Object.setPrototypeOf() - 设置对象的prototype

表达式为: `Object.setPrototypeOf(obj, targetObj)`

```javascript
'use strict';

let breakfast = {
    getDrink(){
        return 'apple';
    };
}

let dinner = {
    getDrink(){
        return 'beer';
    };
}

let sunday = Object.create(breakfast );   //基于breakfast创建sunday对象
console.log(sunday.getDrink());           // return apple

// 判断sunday对象是不是基于breakfast
console.log(Object.getPrototypeOf(sunday, breakfast));   //return true

Object.setPrototypeOf(sunday, dinner);
console.log(sunday.getDrink());           // return beer 
console.log(Object.getPrototypeOf(sunday, breakfast));   //return false

```

### 17. __proto__
得到或者设置对象的prototype

```javascript
'use strict'

let breakfast = {
    getDrink(){
        return 'water';
    }
};

let dinner = {
    getDrink(){
        return 'beer';
    }
};

let sunday = {
    __proto__: breakfast
};

console.log(sunday.getDrink());  //return water

sunday.__proto__ = dinner;       // 设置proto
console.log(sunday.getDrink());  //return beer

console.log(Object.getPrototypeOf(sunday) === dinner);
```

### 18. super 

可重定义覆盖原proto的方法

```javascript
'use strict';

let breakfast = {
    getDrink(){
        return 'water';
    }
};

let dinner = {
    getDrink(){
        return 'beer';
    }
};

let sunday = {
    __proto__: breakfast,
    getDrink(){
        return super.getDrink() + 'cake';
    }
};

console.log(sunday.getDrink()); //return water cake
```

###  19. Itrators - 迭代器
```javascript
'use strict';

function chef(foods) {
    let i = 0;
    let done = (i>=foods.length);
    let value = !done ? foods[i++] : undifined;

    return {
        next(){
            return {
                value: value,
                done: done
            };
        }
    };
}

json = chef(['apple', 'banana']);
console.log(json.next());   //object{value:'apple', done:false}
console.log(json.next());   // object{value:'banana', done:false}
console.log(json.next());   // object{value: undefined, done:true}
```

### 20. Generators - 生成迭代器

```javascript
'use strict';

//生成器
let chef = function* (foods){
    for(var i = 0; i < foods.length; i++){
        yeild foods[i];
    }
}

//迭代器
let json =  chef(['🍎', '🍌']);

console.log(json.next());
console.log(json.next());
console.log(json.next());
```

### 21. classes - 类

```javascript
'use strict';

class Chef{
    constructor(food){
        this.food=food;
        this.dish = [];
    }

    get menu() = {
        return this.dish;
    }

    cook(){
        console.log(this.food);
    }

    set menu(dish){
        this.dish.push(this.dish);
    }
}

let json = new Chef('🍌');

json.cook();
```

### 22. get 和 set

```javascript
'use strict';

class Chef{
    constructor(food){
        this.food=food;
        this.dish = [];
    }

    get menu() = {
        return this.dish;
    }

    set menu(dish){
        this.dish.push(this.dish);
    }

    cook(){
        console.log(this.food);
    }

}

let json = new Chef();

console.log(json.menu = '🍜');
console.log(json.menu = '🍕');
console.log(json.menu);
```

### 23. static - 静态方法
无需实例化类的方法
```javascript
'use strict';

class Chef{
    static cook (food){
        console.log(food);
    }
}

Chef.cook('🍅');

```

### 24. extends - 继承

```javascript
'use strict';

class Person {
    constructor(name, birth){
        return this.name = name;
        return this.birth = birth;
    }
    intro(){
        return `${name}.'s year is ${birth}`;
    }
}

class Chef extens Person{
    constructor(name, birth){
        super(name, birth);
    }
}

let Json = new Chef("Jsonlocker", "2019-02-01");
console.log(Json.intro);

```

### 25. Set - 集合

不包含重复项目的集合
```javascript
'use strict';

let foods = new Set('🌲🍉🍓');
console.log(foods);    // return  Set{'🌲','🍉','🍓'}
foods.add('🍎')                  // 添加子项;
console.log(foods.size);         // 查看数量
console.log(foods.has('🍎'));    // 判断包含
foods.delete('🍎');              // 删除
foods.forEach(food => {          // 循环处理
    console.log(food);
});
foods.clear();                   // 清空集合
```
### 26. Map

```javascript
'use strict';

let foods = new Map();
let fruit = {}, cook = function(){} , desert = 'banana';
foods.set(fruit, '🍋');         // 设置项目
foods.set(cook, '🔪');
foods.set(desert, '🥧');

console.log(foods.size);        // 判断大小
console.log(foods.get(fruit));  // 获得子项
foods.delete(desert);           // 删除子项
console.log(foods.has(desert));  // 判断存在

foods.forEach((value, key) =>{
    console.log(`${key} => ${value}`);
});

foods.clear();                  // 清空
```


