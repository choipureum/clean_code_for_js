# 경계 다루기
[1. min-max](#1-min-max)<br>
[2. begin-end](#2-begin-end)<br>
[3. first-last](#3-first-last)<br>
[4. prefix-suffix](#4-prefix---suffix)<br>
[5. 매개변수의 순서가 경계다](#5-%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98%EC%9D%98-%EC%88%9C%EC%84%9C%EA%B0%80-%EA%B2%BD%EA%B3%84%EB%8B%A4)<br>

---

## 1. **min-max**

- 최소값과 최대값 확실히 하기(이상-이하/ 초과-미만)
```js
//난수 생성 함수
function genRandomNumber(min, max){
    return Math.floor(Math.random() * (max-min+1))+min;
}

const MIN_NUMBER =  1;
const MAX_NUMBER = 45;

genRandomNumber(MIN_NUMBER, MAX_NUMBER); //1~45사이 랜덤 난수 생성
```
**NOTE** 사이드이펙트 - MIN, MAX의 경계가 애매할 수 있다. 이상,이하 초과,미만 등

```js
/*
1. 최솟값과 최댓값을 다룬다
2. 최소값과 최대값 포함 여부를 결정해야한다(이상-초과/ 이하-미만)등
3. 혹은 네이밍에 최소값과 최대값 포함 여부를 표현한다.
*/
//ex)
const MIN_IN_NUMBER = 1; //1이 최소값
const MIN_NUMBER_LIMIT =1; //등등.
```

---

## 2. **begin-end**

- 보통 수많은 datePicker 라이브러리가 begin-end로 되어있음

```js
function reservationDate(beginDate, endDate){
    //...
}

reservationDate('YYYY-MM-DD', 'YYYY-MM-DD');
```

---

## 3. **first-last**

- 포함된 양 끝을 의미한다.
- ~부터 ~~~까지

```js
const students = ['포코', '존', '현석'];

function getStudents(first, last){
    //...someCode
}
getStudents(students[0], students[2]);
```

---

## 4. **prefix - suffix**

- 코드를 읽는 일관성을 줄 수 있다.

```js
// set keyword(setter)
const language = {
    set current(name){
        this.log.push(name);
    }
    log: []
}

//get keyword(getter)
const obj = {
    log: ['a','b','c'],
    get latest(){
        if(this.log.length === 0){
            return undefined;
        }
        return this.log[this.log.length-1];
    }
}
console.log(obj.latest());
//react에서 use로 prefix로 주는것은 hooks라는 약속 등과 같은 역할
```
```js
//private 표현 이전/ 이후
function FactoryFunction(name){
    //_ prefix를 붙임으로써 private로 표현
    this._name = name;
}

// # private syntax 추가
class FactoryFunction{
    #name = name;
}
```

**NOTE**: react에서 components를 작성할때 Base를 붙인다던가 App을 붙인다던가 약속을 정할수있음

---

## 5. 매개변수의 순서가 경계다.

- 매개변수의 순서만 잘 지켜도 그자체로 경계다.
- 호출하는 함수의 네이밍과 인자의 순서의 연관성을 고려해라

```js
function sumeFunc(someArg, someArg){}

//보통 각 인자에 관해 함수의 네이밍과 인자의 관계를 통해 유추한다.
// 매개변수를 2개가 넘지않도록 고정해주는 것이 보통 좋다.-> 인자가 많아지면 객체로 받거나, 나머지 매개변수를 사용한다(...Args)
genRandomNumber(1, 50);
getDates('2021-10-21', '2021-10-30');
shuffleArray(1, 5);
```
**해결** 
1. 매개변수를 2개가 넘지 않도록 만든다.
2. 두개가 넘을경우 arguments. rest parameter를 고려해라
3. 혹은 랩핑하는 함수를 만들어라(최악의 경우)
4. 객체를 받아라

```js
function someFunc(someArg1,someArg2,someArg3,someArg4){
    
}

function getFunc(someArgs1, someArgs3){
    someFunc(someArgs1, undefined, someArgs3);
}
```