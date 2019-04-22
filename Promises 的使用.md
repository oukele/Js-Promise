##### Promises 的使用

~~~ html
一个 Promise 就是一个对象，它代表了一个异步操作的最终完成或者失败
~~~

`本质上，Promise 是一个绑定了回调的对象，而不是将回调传进函数内部`

###### 基本概念

~~~ html
1、Promise 是一个构造函数，可 new 出一个 Promise 的实例

2、Promise上有两个函数 resolve( 成功之后的回调函数 ) 和 reject ( 失败之后的回调函数 )

3、Promise构造函数的prototype属性上，有一个then() 方法 ，只要是Promise构造函数创建的实例，都可以访问到 .then() 方法

4、Promise表示一个一步操作，每当我们 new 一个 Promise 的实例，这个实例就代表具体的异步操作

5、Promise 创建的实例 是一个异步操作，这个异步操作的结果，只有两种结果
	状态1：异步执行成功，需要在内部调用成功的回调函数 resolve 把 结果返回给调用者
	状态2：异步执行失败，需要在内部调用失败的回调函数reject把结果返回给调用者
	
	由于 Promise 的实例是一个异步操作，所以内部拿到操作结果后，无法使用return把操作结果返回给调用者，这时候 只能使用回调函数的形式，把成功或者失败的结果，返回调用者、

6、我们可以在 new 出来的Promise 实例上 调用.then() 方法，预先为这个Promise 异步操作，指定成功（resolve）和失败（reject）回调函数
~~~



###### Promise的执行时机

+ 每当 new 一个 Promise 实例的时候，除了会得到一个promise实例之外，还会立即调用我们为Promise构造函数传递的那个function，执行function的异步代码
+ 在使用Promise时我们可以用函数进行包裹，使其按需执行



##### 没有使用Promise之前

~~~ javascript
// 依次读取一些文件的内容

const fs = require('fs');
// 形参依次为 文件路径，成功回调，失败回调
function getFilePath(path, succesCB, errorCB){
    fs.readFile(path, 'utf-8', (err,data) => {
     	if(err) return errorCB(err);
        succesCB(data);
    })
}

// 依次读取
getFilePath('./1.txt',function(data){
    console.log(data);
    
    getFilePath('./2.txt',function(data){
        console.log(data);
        
        getFilePath('./3.txt',function(data){
           console.log(data);
            .....
        });
        
    });
   
})
~~~

##### 使用Promise后

~~~ javascript
const fs = require('fs');

function getFilePath(path){
    return new Promise(function(resolve, reject){
        
        fs.readFile(path, 'utf-8', (err,data) => {
            if(err)return reject(err);
            
            resolve(data);
        });
        
    })
}



getFilePath('./1.txt')
    .then(function(data){
    	console.log(data);
    	return getFilePath('./2.txt')
	},[error])
    .then(function(data){
    	console.log(data);
    	return getFilePath('./3.txt')
	},[error])
    .then(function(data){
    	console.log(data);
    	return getFilePath('./4.txt')
	},[error]);


~~~



#### 简单的例子

举个例子 ---> 做饭 、吃饭、洗碗

~~~ JavaScript
// 做饭
function cook() {
    console.log('开始做饭..');
    var p = new Promise(function(resolve, reject) {
        setTimeout(function() {
            console.log('做饭完毕！！！！');
            resolve('鸡蛋炒饭');
        }, 1000);
    });
    return p;
}
// 吃饭
function eat(data) {
    console.log('开始吃饭....' + data);
    var p = new Promise(function(resolve, reject) {
        setTimeout(function() {
            console.log(data + ' 吃完了!!!!!!!!!!');
            resolve('一双筷子和一个碗。');
        }, 1000);
    });
    return p;
}
// 洗碗
function wash(data) {
    console.log('开始洗碗 ' + data);
    var p = new Promise(function(resolve, reject) {
        setTimeout(function() {
            console.log(data + ' 洗完了。');
            resolve('干净的一个碗和一双筷子');
        }, 1000);
    });
    return p;
}
~~~

##### 使用方式一

~~~javascript
cook().then(function(data) {
    return eat(data);
}).then(function(data) {
    return wash(data);
}).then(function(data) {
    console.log(data);
});
~~~

结果

~~~ html
开始做饭..
做饭完毕！！！！
开始吃饭....鸡蛋炒饭
鸡蛋炒饭 吃完了!!!!!!!!!!
开始洗碗 一双筷子和一个碗。
一双筷子和一个碗。 洗完了。
干净的一个碗和一双筷子
~~~

##### 使用方式二

~~~ javascript
cook()
    .then(eat)
    .then(wash)
    .then(function(data) {
   		console.log(data);
	});

~~~

结果

~~~ html
开始做饭..
做饭完毕！！！！
开始吃饭....鸡蛋炒饭
鸡蛋炒饭 吃完了!!!!!!!!!!
开始洗碗 一双筷子和一个碗。
一双筷子和一个碗。 洗完了。
干净的一个碗和一双筷子
~~~



#### reject 的使用

~~~ javascript
function cook1(){
    console.log('开始做饭.....');
    var p = new Promise(function(resolve,reject){
        setTimeout(function(){
            console.log('做饭失败。');
            reject('饭烧焦了');
        },10000)
    });
    return p;
}

cook1().then(eat,function(error){
    console.log(error +'无法吃！');
});
~~~

结果

~~~ html
开始做饭.....
做饭失败。
饭烧焦了无法吃！
~~~



#### catch的使用

~~~ javascript
function cook2(){
    console.log('开始做饭。');
    var p = new Promise(function(resolve,reject){
        setTimeout(function(){
            console.log('做饭失败。');
            return reject('饭烧焦了');
        })
    });
    return p;
}

cook2().then(eat).catch(function(data){
    console.log(data);
})
~~~

结果

~~~ html
开始做饭。
做饭失败。
饭烧焦了
~~~

2、

~~~ javascript
cook().then(function(data){
    throw Error('不小打翻了饭');
    eat(data);
}).catch(function(data){
    console.log(data);
});
~~~

结果

~~~html
开始做饭..
做饭完毕！！！！
Error: 不小打翻了饭 
	at Error (native)
    at http://127.0.0.1:8848/student/page/promise_demo.html:95:11
~~~



#### all 方法的使用 (all 方法提供了并行执行异步操作的能力，并且在所有异步操作执行完后才执行回调。)

~~~javascript
function cut(){
    console.log('开始切菜。');
    var p = new Promise(function(resolve,reject){
        setTimeout(function(){
            console.log('切菜完毕。');
            resolve('切好的菜');
        },1000);
    });
    return p;
}

// 烧水
function boil(){
    console.log('开始烧水。');
    var p = new Promise(function(resolve,reject){
        setTimeout(function(){
            console.log('烧水完毕。');
            resolve('烧好的水');
        },1000);
    });
    return p;
}

Promise.all([cut(),boil()]).then(function(result){
    console.log('准备工作完毕!!');
    console.log( result );
});
~~~

结果

~~~html
开始切菜。
开始烧水。
切菜完毕。
烧水完毕。
准备工作完毕!!
["切好的菜", "烧好的水"]
~~~



#### race 方法的使用 （只要有一个异步操作执行完毕，就立刻执行 then 回调。注意：其它没有执行完毕的异步操作仍然会继续执行，而不是停止。）

~~~ javascript
Promise.race([cut(),boil()]).then(function(result){
    console.log('准备工作完毕！');
    console.log(result);
})
~~~

结果

~~~html
开始切菜。
开始烧水。
切菜完毕。
准备工作完毕！
切好的菜
烧水完毕。
~~~

