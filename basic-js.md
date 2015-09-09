## 웹 개발자가 알아야 할 자바스크립트

### 1. 자료형 과 타입체크(data structure & typeof)

#### 기본자료형

- Boolean
- Null
- Undefined
- Number
- String
- Symbol (ECMAScript 6)
- Object
    + Array
    + Function
    + Date
    + RegExp

#### 타입체크

```js
var data = [];

console.log(typeof data); // object

console.log(Object.prototype.toString.apply(data)); // [object Array]

console.log(_.isArray(data)); // true

```

참고

1. https://developer.mozilla.org/ko/docs/Web/JavaScript/Data_structures
2. https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof

### 2. 일급객체 (first-class object)

- 언어가 기본적으로 제공하는 객체를 `일급객체`라 한다.
- 일급객체는 언어상 제약(변수, 매개변수 사용)이 없다.
- Javascript에서 함수는 일급객체이다.
- 변수에 함수를 할당 할 수 있다.
- 함수에 함수를 매개변수로 전달 할 수 있다.
- 함수에 변수로 전달하는 함수를 `일급함수`라 한다.
- 함수를 변수로 전달받는 함수를 `고차함수`라 한다.

```js

function fn01(callback){
    callback && callback();
};

var fn02 = function() {
    console.log('hello');
};

var fn03 = fn01;

fn01(fn02);

fn02( );

fn03(fn02);

```

### 3. 유효범위(scope)

- 객체의 `유효범위(Scope)`는 `함수`이다.
- `this`는 `함수를 호출한 객체`이다.

```js

var data = 0;

function fn(){
    var no = 1;
    data = no;
    console.log('data:' + data);
}

console.log(typeof no); // undefined

console.log('data:' + data); // data:0

fn(); // data:1

console.log('data:' + data); // data:1

```

```js

var o = {
    data : 0,
    fn : function(){
        console.log('data:' + this.data);
    }
};

o.fn(); // data:0

o.data = 1;

o.fn(); // data:1

var x = {
    data : 2
};

x.fn = o.fn;

o.fn(); // data:1
x.fn(); // data:2

x.data = 3;

o.fn(); // data:1
x.fn(); // data:3

```

```js

function out(){
    console.log('data:' + this.data);
}

var a = {
    data:0
};

var b = {
    data:1
};

out();

a.out = out;
a.out();
out.apply(a);

b.out = out;
b.out();
out.apply(b);

```

### 4. 익명함수(Anonymous functions)

- 이름이 없는 함수를 `익명함수`라 한다.
- 함수 변수선언의 우변은 익명함수이다.
- 매개변수 전달에서 직접 선언된 함수는 익명함수이다.
- `즉시실행함수`는 익명함수의 선언과 함께 실행한다.

```js

function run(fn){
    fn && fn();
}

// 함수 변수선언의 우변은 익명함수이다.
var fn01 = function() {
    // TODO
};

// 매개변수 전달에서 직접 선언된 함수는 익명함수이다.
run(function(){
    console.log('callback...');
});

// 즉시실행함수는 익명함수의 선언과 함께 실행한다.
(function(){

})();

```

### 5. 캡슐화(Encapsulation)

- 소스코드를 지역화하여 접근을 제어한다.
    + 오염방지
    + 간섭방지
    + 정보은닉
    + 독립성
    + 이식성

```js

var ki = {};

(function(root){
    var data = 'hello';

    ki.hello = function(name){
        console.log(data + ' ' + name);
    };

})(ki);

console.log(typeof data); // undefined

ki.hello('홍길동'); // hello 홍길동

```

### 6. 호이스팅(Hoisting)

- 스코프 안에서 선언된 객체는 모두 최상단으로 끌어올려 지는 것을 `호이스팅`이라 한다.

```js

(function(){
    console.log(a, b, c); // undefined function b() undefined

    var a = [];

    function b (){

    }

    var c = function(){

    };

    console.log(a, b); // [] function b() function c() 
})();

```

### 7. 반복문(for ...)

- 반복문 처리를 위한 `underscore` 제공함수 `each`를 활용하자.

```js

var list = [1,2,3,4,5];
var map = {a:1,b:2,c:3,d:4,e:5};

var i, l;
for(i=0, l = list.length; i<l; i++){
    console.log(i, list[i]);
    if(i == 0){
        continue;
    }

    if(i == 2){
        break;
    }
}

var k;
for(k in map){
    console.log(k, map[k]);
    if(k == 'a'){
        continue;
    }

    if(k == 'e'){
        break;
    }
}

/* underscore(_) 의 반복문 처리 */

_.each(list, function(v, k, ctx){
    console.log(k, v);
});

_.each(map, function(v, k, ctx){
    console.log(k, v);
});

/* jquery($) 의 반복문 처리 */

$.each(list, function(k, v, ctx){
    console.log(k, v);
    return k == 2; // break
});

$.each(map, function(k, v, ctx){
    console.log(k, v);
    return k == 'e'; // break
});

```

### 8. 약속 패턴(Promise pattern)

- 비동기 호출에 특화된 패턴

#### callback

```js

function async(param, callback) {
    setTimeout(function() {
        callback && callback(param);
    }, 500);
}

(function() {
    async('홍길동', function(data01) {
        async(data01 + ' 고객님', function(data02) {
            console.log(data02 + ' 반갑습니다.');
        });
    });
})();

(function() {
    async('홍길동', fn01);

    function fn01(data) {
        async(data + ' 고객님', fn02);
    }

    function fn02(data) {
        console.log(data + ' 반갑습니다.');
    }
})();

(function() {
    function fn01(param, fn) {
        async(param, function(data){
            fn & fn(data + ' 고객님');
        });
    }

    function fn02(param, fn) {
        async(param, function(data){
            fn & fn(data + ' 반갑습니다.');
        });
    }

    function run(param) {
        fn01(param, cb01);

        function cb01(data){
            fn02(data, cb02);
        }

        function cb02(data){
            console.log(data);
        }
    }

    run('홍길동');
});


```

#### promise

- 제공함수
    + `Promise.all(iterable)`
    + `Promise.reject(reason)`
    + `Promise.resolve(value)`
    + `then(onFulfilled, onRejected)`

__참고__

- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise

__promise 생성__

```js

function async(param){
    var promise = new Promise(function (resolve){
        setTimeout(function(){
            resolve(param);
        }, 500);
    });

    return promise;
}

function async(param){
    var defered = Promise.defer();

    setTimeout(function(){
        defered.resolve(param);
    }, 500);

    return defered.promise;
}

```

__promise(ES6)__

```js

function async(param){
    return new Promise(function (resolve){
        setTimeout(function(){
            resolve(param);
        }, 500);
    });
}

(function(){
    async('홍길동').then(function(data){
        console.log(data);
    });
})();

(function() {
    async('홍길동').then(function(data) {
        async(data + ' 고객님').then(function(data) {
            console.log(data + ' 반갑습니다.');
        });
    });
})();

(function() {
    async('홍길동').then(fn01).then(fn02);

    function fn01(data) {
        return async(data + ' 고객님');
    }

    function fn02(data) {
        console.log(data + ' 반갑습니다.');
    }
})();

(function() {
    function fn01(param) {
        return async(param).then(function(data) {
            return data + ' 고객님';
        });
    }

    function fn02(param) {
        return async(param).then(function(data) {
            return data + ' 반갑습니다.';
        });
    }

    function run(param) {
        fn01(param).then(fn02).then(function(data){
            console.log(data);
        });
    }

    run('홍길동');
})();
```

Q

http://documentup.com/kriskowal/q/

__Q promise__
```html

<script src="q.js"></script>

```

```js

function async(param){
    var defered = Q.defer();

    setTimeout(function(){
        defered.resolve(param);
    }, 500);

    return defered.promise;
}

(function() {
    async('홍길동').then(function(data) {
        async(data + ' 고객님').then(function(data) {
            console.log(data + ' 반갑습니다.');
        });
    });
})();

(function() {
    async('홍길동').then(fn01).then(fn02);

    function fn01(data) {
        return async(data + ' 고객님');
    }

    function fn02(data) {
        console.log(data + ' 반갑습니다.');
    }
})();

(function() {
    function fn01(param) {
        return async(param).then(function(data) {
            return data + ' 고객님';
        });
    }

    function fn02(param) {
        return async(param).then(function(data) {
            return data + ' 반갑습니다.';
        });
    }

    function run(param) {
        fn01(param).then(fn02).then(function(data){
            console.log(data);
        });
    }

    run('홍길동');
})();

```

__promise all__

```js

function async(param){
    var defered = Promise.defer();

    setTimeout(function(){
        defered.resolve(param);
    }, 500);

    return defered.promise;
}

function fn01(param){
    return async(param).then(function(data) {
        return '홍길동' + data;
    });
}

function fn02(param){
    return async(param).then(function(data) {
        return '홍당무' + data;
    });
}

function asyncAll(){
    var data = [
        fn01('님 반갑습니다.'),
        fn02('는 함께먹어요.')
    ];
    return Promise.all(data);
}

asyncAll().then(function(data){
    console.log(data);
});

```

