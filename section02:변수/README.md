# 변수 다루기
[1.var를 지양해라](#1var%EB%A5%BC-%EC%A7%80%EC%96%91%ED%95%B4%EB%9D%BC)<br>
[2.전역 공간 사용 최소화해라](#2-%EC%A0%84%EC%97%AD%EA%B3%B5%EA%B0%84-%EC%82%AC%EC%9A%A9%EC%9D%84-%EC%B5%9C%EC%86%8C%ED%99%94-%ED%95%98%EB%9D%BC)
[3.임시변수 제거하기](#3-%EC%9E%84%EC%8B%9C%EB%B3%80%EC%88%98-%EC%A0%9C%EA%B1%B0%ED%95%98%EA%B8%B0)
[4.호이스팅 피하기](#4-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85-%EC%A3%BC%EC%9D%98%ED%95%98%EA%B8%B0)

## 1.`**var**`를 지양해라

`var`는 함수 스코프<br>
`let`, `const`는 블록 스코프<br>
=> 안전하게 코드를 작성 가능

```js
console.log(name); // undefined 

var name = '이름';
var name = '이름2';
var name = '이름3';
var name = '이름4';
```
**NOTE**: 무한히 재할당이 가능, 에러가 안나고 호이스팅으로 undefined로 출력(let으로만 선언해줘도 재선언 불가)

```js
var global = '전역';

if(global === '전역'){
    var global = '지역';
    console.log(global); // 지역
}
console.log(global); // 지역
```
```js
let global = '전역';

if(global === '전역'){
    let global = '지역';
    console.log(global); // 지역
}
console.log(global); // 전역
```

**NOTE**: `let`보다도 `const`를 쓰는 것이 좋다. but 어떻게 재할당 해줄 것인가
```js
const person = {
    name: 'pureum',
    age: 29,
}
person = {name: '',...}; // 당연히 재할당 에러 발생
person.name = 'lee'; 
person.age = 30;
person // 재할당 가능: const내 변수, 배열일경우 push도 가능 
```
**NOTE**: 본연의 객체, 배열같은 레퍼런스 객체들을 조작할 때는 이상이 없음!

---

## 2. **전역공간** 사용을 최소화 하라
- 전역공간을 더럽히면 안됨 -> 어디서나 접근 가능한 레벨이기 때문(스코프 분리는 매우 위험)
- 전역변수를 애초에 만들지 않고 지역변수만 생성할 것
- window, global 조작 하지 않기

전역공간
<li>window</li>
<li><b>global</b>: 다른 js파일에서 전역변수는 모두 공유/ 코드의 구역이 나뉘지 않음</li>

**NOTE**: 파일을 나누어도 코드스코프는 나누어지지 않는다.

```js
//index.js
var globalVar = 'abcd';

window.setTimeout(()=>{
    console.log('1초');
},1000);

//index_2.js
var setTimeout = "setTimeout";
// 에러가 안나오고 위의 window.setTimout()이 동작하지 않음

for(var i =0;i<10;i++){
    const element = array[i]; // i가 window 레벨에서 관리됨 : 위험
    //블록 스코프 단위로 나누어서 관리해야 전역에 난잡하게 들어가는 문제를 해결할 수 있다.
}
```
**해결**
1. IIFE : 즉시 실행 함수
2. Module: 모듈
3. 클로져 사용
4. const, let 변수 사용으로 블록 스코프 레벨 변수 관리

---

## 3. **임시변수** 제거하기

- 임시변수: Scope안에서 전역변수처럼 활용되는 변수들.


### 상황 1
```js
function getElement(){
    const result = {};
    
    result.title = document.querySelector('.title');
    result.text = document.querySelector('.text');
    result.value = document.querySelector('.value');
    return result;
}
```
**NOTE**: `getElement`라는 함수도 커지게 되면 전역공간처럼 쓰일 수 있다. 즉 내부 임시변수들도 전역변수처럼 될수 있다.

- 해결법
```js
function getElement(){
    return {
        title = document.querySelector('.title'),
        text = document.querySelector('.text'),
        value = document.querySelector('.value'),
    };
}
```
**NOTE** 임시 변수가 없고 사이드 이펙트가 적은 코드로 변경

### 상황 2
```js
function getDateTime(){
    let month = targetDate.getMonth();
    let day = targetDate.Date();
    let hour = targetDate.Hours();

    month = month >= 10? month: '0'+month;
    day = day >= 10? day: '0'+day;
    hour = hour >= 10? hour: '0'+hour;
    
    return{
        month, day, hour,
    }
}
```
**NOTE**: 바로 리턴할 수 있는 형태로 변경해 줄 것

- 해결법

```js
function getDateTime(targetDate){
    let month = targetDate.getMonth();
    let day = targetDate.Date();
    let hour = targetDate.Hours();

    return{
        month : month >= 10? month: '0'+month;
        day : day >= 10? day: '0'+day;
        hour : hour >= 10? hour: '0'+hour;
    }
}

function getDateTime(){
    const currentTime = getDateTime(new Date());
   
    return{
        month: computedKrDate(currentTime.month) + '분 전',
        day: computedKrDate(currentTime.day) + '분 전',
        hour: computedKrDate(currentTime.hour) + '분 전',
    }
}
```

### 상황 3
```js
function getRandomNumber(min,max){
    const randomNumber = Math.floor(Math.random() * (max+1)+min);
    return randomNumber;
}
```
**NOTE**: 임시변수를 조작한다면 역시나 사이드이펙트가 생긴다.
- 해결법

```js

```

### 상황 4
- js입문자가 보통 흔히 저지르는 구현 코드 (값을 예측하기가 매우 힘들다)
```js
//문제 코드
function getSomeValue(param){
    let tempVal = '';
    
    for(let index =0 ;i<array.length;i++){
        temp= array[index];
        temp+=array[index];
    }

    if(temp??){
        tempVal =??
    }else{
        ...
    }
}
```

**문제**
- 명령형으로 가득한 코드들, 코드스멜
- 어디서 어떻게 잘못되었는지 디버깅이 어렵다.
- 추가 코드 작성을 계속 넣고 싶다는 유혹에 빠지게됨 -> **코드 유지보수가 매우 힘듬**<br>

**해결**
- -> 함수를 나누기, 바로 return, 고차함수(map, filter, reduce) 이용
- -> 선언형으로 코딩하기


## 4. **호이스팅** 주의하기

- 호이스팅: 런타임 시기(동작할 때) 선언이 최상단으로 끌어올려짐=> 문제는 선언과 할당이 분리됨, 예측하지 못한 실행 결과가 도출됨.
<br>=> 예측하지 못한 상황을 탈피하고자 함수표현식을 사용하여 호이스팅을 방지한다.

문제상황
```js
var global = 0;

function outer(){
    console.log(global); // undefined -> 선언과 할당이 분리되있음
    var global = 5;
    
    function inner(){
        var global = 10;
        console.log(global); // 10
    }
    inner();
    global =1;
    console.log(global); // 1
}
outer();
```
```js
function duplicatedVar(){
    var a;
    console.log(a); // undefined
    var a =100;
    console.log(a); // 100
}
console.log(duplicatedVar); // undefined -> 현재 스코프 최상단에 선언하기때문
```
```js
var sum;

console.log(typeof sum); //function -> 함수도 호이스팅 된다 

function sum(){
    return 1+2;
}
```
```js
var sum =10;

console.log(sum); //10 선언과 할당을 함께 해주면 제대로 선언됨 

function sum(){
    return 1+2;
}
```
```js
//옳바른 예시
const sum = () => {
    return 1+2;
}
console.log(sum()); //3 
```
**NOTE**: 함수를 만들 땐 함수 표현식으로 만들어라