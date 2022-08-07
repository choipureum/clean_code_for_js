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

## 9. `화살표 함수`

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


