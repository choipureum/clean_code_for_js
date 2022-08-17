# 함수 다루기

[1. `함수, 메서드, 생성자`](#1.`함수, 메서드, 생성자`)<br>
[2. `argument & paramter`](#2.`argument & paramter`)<br>
[3. `복잡한 인자 관리하기`](#3.`복잡한 인자 관리하기`)<br>
[4. `Default Value`](#4.`Default Value`)<br>
[5. `Rest Parameters`](#5. `Rest Parameters`)<br>
[6. `void, return`](#6. `void, return`)<br>
[7. `화살표 함수`](#7. `화살표 함수`)<br>
[8. `Callback Function`](#8. `Callback Function`)<br>
[9. `순수 함수`](#9. `순수 함수`)<br>
[10. `Closure`](#9. `Closure`)<br>


---

## 1.`함수, 메서드, 생성자`

```js
/**
 * @name 함수, 메서드, 생성자
 * @returns {func}
 */
function func(){
    return this;
}

/**
 * @name 객체의 메서드
 * @type {{method(): this}}
 */
const obj = {
    method(){
        return this;
    }
}

/**
 * @name  Class 생성자 함수
 * @description 요즘엔 @class가 나와서 사용 안해도 된다.
 * @constructor
 */
function Func(){
    return this;
}

/**
 * @name { 함수 } : 1급객체
 *   - 변수나, 데이터에 담길 수 있다.
 *   - 매개변수로 전달가능(콜백 함수)
 *   - 함수가 함수를 반환(고차함수)
 */
func();
/**
 * @name { 메서드 } => 객체에 의존성이 있는 함수, OOP 행동을 의미
 */
obj.method();

/**
 * @name 생성자 함수 => 인스턴스를 생성하는 역할 => Class
 * @type {Class}
 */
const instance = new Func();
```

## 2. `argument & parameter`

`parameter` vs `argument`
- `parameter` : 함수에 들어가는 파라미터는 name이 있고 함수의 정의, 구현부분에 정의된다.
- `argument` : 함수에 들어가는 real value 이다.
```js
/**
 * Parameter (Formal Parameter) - 형식을 갖춘 파라미터
 * 이름 존재
 * @param url
 */
function axios(url){
}

/**
 * Argument (Actual Parameter) : 실제 값 : 어떤 대표되는 것이 아닌 진짜 실제 밸류
 * 이름이 없음
 */
axios('http://github.com')
```

---

## 3. 복잡한 인자 관리하기

```js
function toggleDisplay(isToggle){}
function sum(sum1, sum2){}
function genRandomNumber(min,max){}
function ntimer(start, stop, end){}
function genSquare(top, right, bottom, left){}

/**
 * @name 복잡한 인자 관리하기 - worst
 * @param name
 * @param brand
 * @param color
 * @param type
 * @returns {{color, name, type, brand}}
 */
function createCar(name, brand, color, type){
    return{
        name,
        brand,
        color,
        type,
    }
}

function createCar(name, { brand, color, type }){ // 차량이름 { options }
    return{
        name,
        brand,
        color,
        type,
    }
}
```
```js
/**
 * @name worst 경우
 * @param name
 * @param brand
 * @param color
 * @param type
 */
function createCar({name, brand, color, type}){
    if(!name){
        throw new Error('이름 필요');
    }
    
    if(!brand){
        throw new Error('브랜드 필요');
    }
}
// => 구조분해 할당으로 변수가 없는 거에 대한 명시적 에러처리 가능 : 인자관리
```

---

## 4. `Default Value`

- 기본값 설정은 중요. 보통 라이브러리에서 고민하게 되는 문제이다.


```js
/**
 * @name 방어코드가 없으면 undefined 천지가 됨
 * @param options
 * @returns {{margin: (*|number), center: number, navElement: (string)}}
 */
function createCarousle(options){
    options = options || {};
    let margin = options.margin || 0;
    let center = options.center || false;
    let navElement = options.navElement || 'div';
    
    return{
        margin,
        center,
        navElement,
    }
}

/**
 * @name 위의 코드와 100% 같이 동작함 - 구조분해 할당 개선 추가
 * @param margin
 * @param center
 * @param navElement
 * @returns {{margin: number, center: boolean, navElement: string}}
 */
function createCarousel({ // 기본 값을 다 할당 해준다
    margin = 0,
    center = false,
    navElement = 'div'
} = {}){ // options = {}
    return{
        margin,
        center,
        navElement,
    }
};
/**
 * @name 파라미터 예외처리 케이스
 * @param argName
 */
const required = (argName) => {
    throw new Error();
}

function createCarousle({
    items = required('items'), 
    margin = required('margin'), 
    center = false, 
    navElement = 'div'
} = {}){
    return{
        margin,
        center,
        navElement,
    }
}
```

---


## 5. `Rest Parameter`

```js
sumTotal(1,2,3,4,5);
/**
 * @name worst code
 * @returns {any}
 */
function sumTotal(){
    return Array.from(arguments).reduce(
        (acc,curr) => acc + curr,
    );
}

/**
 * @name 개선 코드 - rest Parameter
 * @param args
 * @returns {any}
 */
function sumTotal(initValue, ...args){
    console.log(initValue);
    
    return args.reduce(
        (acc,curr) => acc + curr,
    );
}
// 추가적인 인자도 받을수 있으며 Array로 변환하지 않아도 된다.
```

---

## 6. `void & return`

```js
function handleClick(){
    return setState(false); // return 
}
//개선
function handleClick(){
    setState(false);
    return;
}
// worst
function showAlert(message){
    return alert(message);
}

function testVoid(){
    return [1,2,3].push(4); // return 4
}
```
**NOTE** :  return 하는 객체에만 return 해줄 것.

---

## 7. `화살표 함수`

- 화살표 함수를 무조건 사용할 필요는 없다

```js
const user = {
    name : 'poco',
    getName: () => {
        return this.name;
    }
}

user.getName(); // undefined (렉시컬 스코프로 변경)

const user = {
    name : 'poco',
    getName(){
        return this.name;
    }
}

user.getName() // poco
```

```js
const user = {
    name: 'Poco',
    getName: () => {
        return this.name;
    },
    newFriends: (...rest)=> {
        //call, bind, apply 사용 불가
        const newFriendList = rest;
        return this.name + newFriendList;
    }
};
```
```js
const Person = (name,city) => {
    this.name = name;
    this.city = city;
}

const person = new Person('poco', 'korea'); // ERROR! 
// 화살표 함수는 생성자 함수 생성 불가
```
```js
class Parent{
    parentMethod(){
        console.log('parentMethod')
    }
    parentMethodArrow = () => {
        console.log('parentMethodArrow')
    }
    overrideMethod = () => {
        return  'Parent';
    }
}
class Child extends Parent{
    childMethod(){
        super.parentMethod(); // parentMethod
    }
    choidMethodArrow(){
        super.parentMethodArrow(); // ERROR! 
        // 화살표함수로 만든 class 내부 메소드는 상속이 안됨 초기화됨
    }
    overrideMethod(){
        return 'Child';
    }
}
new Child().childMethod();
new Child().overrideMethod(); // Child가 호출될지 알았지만 Parent가 호출됨 => 화살표함수를 일반함수로 바꾸면 정상적으로 Child가 나옴.
```

**NOTE** : 무분별한 화살표함수는 옳지 않다. 문법적으로 안되거나 예측 불가능한 엣지케이스가 나올 수 있다. 적극적으로 사용하되 분별해서 사용하자.


---

## 8. `Callback Function`

```js
/**
 * @name 대표적 콜백 함수
 */
element.addEventListener('click', (e)=> {
    //여러 event
});
// addEventListener의 프로토타입 정의
Dom.prototype.addEventListener = (eventType, callbackFunc) => {
    if(eventType == 'click'){
        const clickEventObject = {
            target: {},
        }
    }
    callbackFunc(clickEventObject);
}

/**
 * @name 예제 2
 */
function register(){
    const isConfirm = confirm('회원가입에 성공했습니다.');
    
    if(isConfirm){
        redirectUserInfoPage();
    }
}

function login(){
    const isConfirm = confirm('로그인에 성공했습니다');
    
    if(isConfirm){
        redirectIndexPage();
    }
}

/**
 * @name 공통화
 * @param msg
 * @param callbackFunc
 */
function confirmModal(msg, callbackFunc){
    const isConfirm = confirm(msg);
    
    if(isConfirm && callbackFunc){
        callbackFunc();
    }
}
```

---

## 9. `순수 함수`
**NOTE** : 순수함수는 예측 가능하며 사이드이펙트를 일으키지 않는 함수.

```js
let num1 = 10;
let num2 = 20;

/**
 * @name 비순수함수
 * @returns {number}
 */
function impureSum1(){
    return num1 + num2;
}

function impureSum2(newNum){
    return num1 + newNum;
}

impureSum1(); //  30

num1 = 30;
impureSum1(); //  50 -> 외부인자에 따라 변해버림
impureSum2(30); // 60 -> 사이드이펙트 발생
```
```js
/**
 * @name 순수함수
 * @param num1
 * @param num2
 * @returns {*}
 */
function pureSum(num1, num2){
    return num1 + num2; // 외부인자에 따라 변하지 않는 순수함수
}
```

**NOTE** : 순수함수 심화
```js
/**
 * @name 순수함수
 * @param num
 * @returns {*}
 */
function changeValue(num){
    num++;
    return num;
}
changeValue(1); //2
changeValue(3); //4
changeValue(4); //5
//////////////////////////

// 원시타입 vs 참조 타입 차이
// => 객체, 배열 등은 새롭게 만들어 반환해야된다.
const obj = { one: 1}
function changeObj(targetObj){
    targetObj.one = 100;
    return targetObj;
}

changeObj(obj); //{one: 100}
obj; // { one: 100 }

function chageObjNew(targetObj){
    return { ...targetObj, one: 100 };
}
changeObjNew(obj); // { one: 100 };
obj; // { one:1 }
```
---

## 10. `Closure`

```js

function add(num1){
    return function sum(num2){
        return num1 + num2;
    }
}

const addOne = add(1);
const addTwo = add(2);

addOne(3); // 4
addTwo(2); // 4

const addThree = add(1)(3) // 4
```

**NOTE** : 응용1
```js
function add(num1){
    return function(num2){
        return function(calculateFn){
            return calculateFn(num1,num2);
        }
    }
}

function sum(num1,num2){
    return num1 + num2;
}
function multiple(num1, num2){
    return num1 * num2;
}

const addOne = add(5)(2);
const sumAdd = addOne(sum); // 7
const sumMultiple = addOne(multiple); //10
```
**NOTE** : 응용2

```js
function log(value){
    return function(fn){
        fn(value);
    }
}

const logFoo = log('foo');

logFoo((v)=> console.log(v));
logFoo((v)=> console.info(v));
logFoo((v)=> console.error(v));
logFoo((v)=> console.warn(v));


///////

const arr = [1,2,3, 'A', 'B', 'C'];

function isTypeOf(type){
    return function(value){
        return typeof value === type;
    }
}

const isNumber = isTypeOf('number');
const isString = isTypeOf('string');


arr.filter(isNumber); //[1,2,3]
arr.filter(isString); //[A,B,C]
```

```js
function fetcher(endpoint){
    return function(url,options){
        return fetch(endpoint+url, options)
            .then((res) => {
                if(res.ok){
                    return res.json();
                }else{
                    throw new Error(res.error);
                }
            })
            .catch((err) => console.error(err));
    };
}

const naverApi = fetcher('http://naver.com');
const daumApi = fetcher('http://daum.net');

daumApi('/webtoon').then((res)=> res);
naverApi('/webtoon').then((res)=> res);
```



