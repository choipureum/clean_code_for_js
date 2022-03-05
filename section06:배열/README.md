# 배열 다루기

[1. JS의 배열은 **객체**다](#1-js%EC%9D%98-%EB%B0%B0%EC%97%B4%EC%9D%80-%EA%B0%9D%EC%B2%B4%EB%8B%A4)<br>
[2. `Array.length` ](#2-arraylength)<br>
[3. 배열 요소 접근](#3-%EB%B0%B0%EC%97%B4-%EC%9A%94%EC%86%8C-%EC%A0%91%EA%B7%BC)<br>
[4. 유사 배열 객체 ](#4-%EC%9C%A0%EC%82%AC-%EB%B0%B0%EC%97%B4-%EA%B0%9D%EC%B2%B4)<br>
[5. 불변성](#5-%EB%B6%88%EB%B3%80%EC%84%B1)<br>
[6. `for`문 배열 고차 함수로 리팩토링하기](#6-for%EB%AC%B8-%EB%B0%B0%EC%97%B4-%EA%B3%A0%EC%B0%A8-%ED%95%A8%EC%88%98%EB%A1%9C-%EB%A6%AC%ED%8C%A9%ED%86%A0%EB%A7%81%ED%95%98%EA%B8%B0)<br>
[7. 배열 메서드 체이닝 활용](#7-%EB%B0%B0%EC%97%B4-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%B2%B4%EC%9D%B4%EB%8B%9D-%ED%99%9C%EC%9A%A9)<br>
[8. `map` vs `forEach`](#8-map-vs-foreach)<br>
[9. `Continue` & `Break`](#9-continue--break)<br>

---

## 1. JS의 배열은 **객체**다

```js
const arr = [1,2,3];

arr[3] = 'test';
arr['property'] = 'string value';
arr['func'] = function (){return 'hello';}

arr.forEach(()=>{
    console.log(arr[i]); // 1,2,3
});

const obj = {
    0: 1,
    1: 2,
    2: 3, //arr
    
    3: 'test',
    property: 'string value',
    func: () => 'hello',
}
```
**NOTE**: js 배열은 객체와 같다.

```js
//배열의 타입확인 법의 안티패턴
const arr = [1,2,3];

if(arr.length){
    console.log('배열 확인');
}
if(arr instanceof Array){
    console.log('배열 확인');
}

//빌트인 메서드를 활용해라! - 최근에 나온 문법이라 polyfill이 필요할 수 있다.
Array.isArray(arr);// 배열인가? - boolean
console.log(Array.isArray('test')); // false

```

---

## 2. `Array.length`

```js
const arr = [1,2,3];

//정말 많이 사용하는 함수 arr.length
arr.length = 3;

//안티패턴인 경우
console.log(arr.length); //3
arr.length = 10;
console.log(arr) //[1,2,3,,,,,,,,,] => 객체로 처리되기ㅣ 때문에 빈칸이 들어갈 수 있다.

//어레이 렝스 이용
// 배열 초기화로 이용할 수 있다
Array.prototype.clear = function(){
    this.length = 0;
}
function clearArray(array){
    array.length = 0;
    return array;
}
```
**NOTE** : 배열의 길이는 빈칸이 들어갈수 있기때문에 정말로 값이 들어있는 배열의 길이를 보장해 주지못함. 의식적으로 사용하는 노력을 해야한다.

---

## 3. 배열 요소 접근
- 구조분해 할당(디스트럭터링)을 최대한 활용해준다.
```js
//cypress 예시
const arr = [1,2,3];

//안티패턴
function operateTime(input, operators, is){
    input[0].split('').forEach((num) => {
        cy.get('.digit').contains(num).click();
    });

    input[1].split('').forEach((num) => {
        cy.get('.digit').contains(num).click();
    });
}

// 조금 더 명시적으로 사용하기 = 특히 split은 배열의 요소를 직관적으로 알기힘듬
function operateTime(input, operators, is){
    const [firstInput, secondInput] = inputs;
    firstInput.split('').forEach((num) => {
        cy.get('.digit').contains(num).click();
    });

    secondInput.split('').forEach((num) => {
        cy.get('.digit').contains(num).click();
    });
}

operateTime([1,2],1 ,2); //이런식으로 진행가능
```
```js
//안티 패턴
function clickGroupButton(){
    const confirmButton = document.getElementsByTagName('button')[0];
    const cancelButton = document.getElementsByTagName('button')[1];
    const resetButton = document.getElementsByTagName('button')[2];
}

//좀 더 명시적이고 역할을 확실히 부여한다
function clickGroupButton(){
    //0 1 2
    const [confirmButton,cancelButton,resetButton] = document.getElementsByTagName('button');
}
```
```js
//안티 패턴
function formatDate(targetDate){
    const date = targetDate.toISOString().split()[0];

    const [year,month,day] = date.split('-');

    return `${year}년 ${month}월 ${day}일`;
}

//리팩토링
function formatDate(targetDate){
    const [date] = targetDate.toISOString().split();
    const [year,month,day] = date.split('-');

    return `${year}년 ${month}월 ${day}일`;
}
```
**NOTE**: 배열의 순서도 디스트럭터링 할 수 있다.
---

## 4. 유사 배열 객체 

```js
const arrayLikeObject = {
    0: 'HELLO',
    1: 'WORLD',
    length: 2,
};

Array.isArray(arrayLikeObject) // false; /당연히 배열은 아니다
const arr = Array,from(arrayLikeObject); // 배열로 형변환한다.
Array.isArray(arr); // true : 배열이 됬음
console.log(arr); // ['HELLO', 'WORLD'];
console.log(arr.length) //2

//혼란을 야기할 수 있음
function generatePriceList(){
    return arguments.map((arg)=>arg+'원'); // 유사배열 객체이기때문에 map메서드를 사용할 수 없다.
    // 사용하려면 
    return Array.from(araguments).map((arg)=>arg); //로 변환후 사용해야한다.
}
generatePriceList(100,200,300,400); //=> arguments는 대표적인 js 유새배열 객체
//가변인자로써 매개변수를 미리 선언하지 않아도 받아낼수잇다.
arguments:{
    '0':100,
    '1':200,
    '2':300,
    '3':400,
}
```

---


## 5. 불변성(immutable)

```js
//1번 깊은 복사
const originArray = ['123', '456', '789'];
// 주소값 자체를 복사함
const newArr = originArray;

originArray.push(10); //
originArray.push(11);
originArray.push(12);
originArray.push(0);

console.log(newArr); // [0,123,456,789,10,11,12] -> 깊은 복사

//2번 얕은복사
// 깊은복사를 하지말고 항상 새로운 배열을 반환하는 습관을 들여라
const newArr = [...originArray]; 
//이외에도 slice,map,filter등의 여러 메소드들은 새로운 배열을 반환한다.
```

---


## 6. `for`문 배열 고차 함수로 리팩토링하기(**중요)

```js
//안티 패턴
const price = [1000,2000,3000,5000,4000];

function getWonPrice(priceList){
    let temp = [];
    
    for(let i=0;i<priceList.length;i++){ //i 가 명시적이지 않다
        temp.push(priceList[i]+`원`);
    }
    return temp
}

const result = getWonPrice(price);
console.log(result); // [1000원, 2000원 ...]


const price = [1000,2000,3000,5000,4000];

const suffixWon = (price) => price+'원';
function getWonPrice(priceList){
    return priceList.map(suffixWon);
}

const result = getWonPrice(price);
console.log(result); // [1000원, 2000원 ...]
```

```js
//조건 추가 1000원 추가 리스트만 출력하라
// 그리고 가격순 정렬
const price = [1000,2000,3000,5000,4000];
const isOverOneThousand = (price) => Number(price) > 1000; // Number로 형변환 가능

const suffixWon = (price) => price+'원';
function getWonPrice(priceList){
    const isOverList = priceList.filter(isOverOneThousand); //함수명만 적어주면 자동으로 파라미터 처리
    return priceList.map(suffixWon);
}
```
**NOTE**: 정렬도 추가한다면 isOverList밑에 또 추가 되며 코드스멜이 나는 코드가 될 확률이 높다. 배열 메서드 체이닝을 활용해 깔끔히 정리해 볼수 있을것같다.

---

## 7. 배열 메서드 체이닝 활용
```js
//로직이 점점 늘어날 염려가 있다. 한번더 생각해보기
// 선언적으로 사용하는게 최선인가?
const price = [1000,2000,3000,5000,4000];
const isOverOneThousand = (price) => Number(price) > 1000; // Number로 형변환 가능

const suffixWon = (price) => price+'원';
function getWonPrice(priceList){
    const isOverList = priceList.filter(isOverOneThousand); //함수명만 적어주면 자동으로 파라미터 처리
    const sortList = isOverList.sort(ascendingList); //정렬
    return sortList.map(suffixWon);
}

// 메서드 체이닝 활용
function getWonPrice(priceList){
    return priceList
            .filter(isOverOneThousand)
            .sort(ascendingList)
            .map(suffixWon);
            //.reduce()
            //.flatmap()...등등
}
```

## 8. `map` vs `forEach`
- `map`과`forEach`의 차이점 분명히 하고 사용하기
- 공통점: 배열을 다루는 메소드
- 차이점: `forEach`와 `map`의 근본적인 차이는 `return` 에 있다.
```js
const prices = [1000,2000,3000];

const newPricesForEach = prices.forEach((price)=>price+'원'); //undefined
const newPricesMap = prices.map((price)=>price+'원'); //[1000원, 2000원, 3000원]
```
**NOTE** foreach는 리턴을 하지않고 단순히 루프를 돌며 전부 실행시켜주는 void적인 함수 / map은 배열에 조작을 가하고 새로운 배열을 반환한다.

---


## 9. `Continue` & `Break`

- `Continue`는 `Break`

```js
//foreach에서 루프 흐름제어를 사용하고 싶다면?
const orders = ['first', 'second', 'third'];

for(let i = 0; i<order.length;i++){
    const order = orders[i];
    
    if(order === 'second'){
        continue;
        break;
    }
}

orders.forEach(function(order){
    if(order === 'second'){
        break; //foreach문에서는 break, continue가 안됨 //에러
    }
})

//리팩토링

try{
    orders.forEach(function(order){
        if(order === 'second'){
            break; //foreach문에서는 break, continue가 안됨
        }
    })
}catch(e){

}
```
**NOTE** `forEach`, `map`, `filter`등 배열 고차함수에서는 `break`, `continue`가 안됨. 
1. try, catch활용
2. for문, for of등 활용
3. **`every()`, `some()`, `find()`, `findIndex()`등을 활용해 특정 상황 조기종료를 유도하라**