# 타입 다루기
[1. 타입 검사](#1-%ED%83%80%EC%9E%85%EA%B2%80%EC%82%AC)<br>
[2. undefined vs null](#2-undefined-vs-null)<br>
[3. eqeq 줄이기](#3-eqeq-%EC%A4%84%EC%9D%B4%EA%B8%B0---%EB%8A%90%EC%8A%A8%ED%95%9C-%EB%8F%99%EB%93%B1%EC%97%B0%EC%82%B0%EC%9E%90%EB%A5%BC-%EC%A7%80%EC%96%91%ED%95%98%EB%9D%BC)<br>
[4. 형변환 주의하기](#4-%ED%98%95%EB%B3%80%ED%99%98-%EC%A3%BC%EC%9D%98%ED%95%98%EA%B8%B0%EB%AA%85%EC%8B%9C%EC%A0%81-%ED%98%95%EB%B3%80%ED%99%98%EC%9D%84-%EC%8D%A8%EB%9D%BC)<br>
[4. isNaN](#5-isnan)<br>

---

## 1. **타입검사**

### `typeof`

`typeof`는 타입을 검사해 **문자열**로 반환한다
```js
typeof '문자열' // 'string'
typeof true   // 'boolean'
typeof undefined //undefined
typeof 123    //number
typeof Symbol() //symbol
```
**NOTE** 모든 타입을 검사해주진 않음
- Primitive : 원시형, 불변 -> `typeof` 가능
- Reference : 참조형, 가변 -> `typeof`로 알기 힘듬, 동적으로 변하는 언어는 타입도 동적이다.

```js
//1. function
function func(){}
typeof func ;// function

//2. class
class class{} 
typeof class; // function

//3. wrapper
const str = new String('string');
typeof str //object

//4. null
typeof null // object -> 언어적 오류(수정할 수 없다고 판단함 js에서)
```

### `instanceof`
- 프로토 타입 체인을 검사 : `Boolean`으로 반환

```js
function Person(name,age){
    this.name = name;
    this.age = age;
}
const p = {
    name: 'test',
    age: 99,
}
const poco = new Person('poco', 99);

poco instanceof Person // true
p instanceof Person // false


const arr = [];
const func = function(){}
const date= new Date();

arr instanceof Array; //true
func instanceof Function; //true
date instanceof Date; //true

//하지만
arr instanceof Object; //true
func instanceof Object; //true
date instanceof Object; //true

Obejct.prototype.toString.call(new String('')); //[object String] // 검사가능
Obejct.prototype.toString.call(func); // [object function]
Obejct.prototype.toString.call(date); //[object Date]
```
**NOTE** 검사 방법에 대한 구글링을 열심히 해보고 적용할 것

**해결** 
 - 동적 타이핑 언어 -> 타입검사에 대한 어려움(외워라...)

---

 ## 2. **undefined** vs **null**

```js
!null = true;
!!null = false;
null+123 = 123;

let verb;
verb = undefined;
let varb = null;
typeof varb; //object
undefined+10 //NaN

!undefined = true;
undefined == null //true
undefined === null // false
!undefined === !null //true
```
**해결**: 팀적으로 하나를 정해서 사용하는 것이 좋다.
- undefined, null => 값이 없거나 정의되지 않은 명시적인 표현

---

## 3. eqeq(==) 줄이기 - 느슨한 동등연산자를 지양하라
`=` : 할당 연산자<br>
`==` : 동등 연산자<br>
`===` : 엄격한 동등 연산자 => **강추**<br>

```js
// 동등 연산자를 사용할 경우 타입캐스팅(형변환)이 일어남
'1' == 1 //true
1 == true // true

'1' === 1 //false
1 === true //false
```
**NOTE** 강제 형변환을 해서 엄격한 타입검사로 동등연산자를 사용해라.(정석적으로)

---

## 4. 형변환 주의하기(명시적 형변환을 써라)

```js
// 암묵적 형변환
!!'문자열' //true
!!''; //false

//명시적 형변환(추천)
parseInt('9.999', 10); // 뒤에 10을 빼먹는 경우가 많은데 10진수라는 것을 명시적으로 지정해주는 것이 좋다.(10진수가 디폴트는 아니다. 오류를 줄일 수 있음) //9 
```

---

## 5. isNaN

```js
Number.isInteger // 정수 판별 함수
isNaN() // 숫자에 대해서 판별할 수 있는지 판단한다. 
// is Not A Number : 숫자가 아니다
typeof 123; //isNaN 
isNaN(123); //false => 숫자가 맞다(NaN이 아니다)
Number.isNaN(123+'테스트') // true -> isNaN
isNaN(123+'xptmxm') // false -> 숫자다?
```
**NOTE** : `isNaN`: 느슨한 검사 <br>
`Number.isNaN`: 엄격한 검사