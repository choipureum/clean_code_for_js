# 분기 다루기: 매우 중요**
[1. 값식문](#1-값식문)<br>
[2. 삼항연산자 다루기](#2-삼항-연산자-다루기)<br>
[3. Truthy & Falsy](#3-truthy--falsy)<br>
[4. 단축평가(short-circuit-evaluation)](#4-%EB%8B%A8%EC%B6%95%ED%8F%89%EA%B0%80short-circuit-evaluation)<br>
[5. else if를 피해라](#5-else-if%EB%A5%BC-%ED%94%BC%ED%95%B4%EB%9D%BC)
[6. else를 피해라](#6-else%EB%A5%BC-%ED%94%BC%ED%95%B4%EB%9D%BC)<br>
[7. Early Return](#7-early-return)<br>
[8. 부정 조건문 지양하기](#8-%EB%B6%80%EC%A0%95-%EC%A1%B0%EA%B1%B4%EB%AC%B8-%EC%A7%80%EC%96%91%ED%95%98%EA%B8%B0)<br>
[9. Default Case 고려하기](#9-default-case-%EA%B3%A0%EB%A0%A4%ED%95%98%EA%B8%B0)<br>
[10. 명시적인 연산자 사용 지양하기](#10-%EB%AA%85%EC%8B%9C%EC%A0%81%EC%9D%B8-%EC%97%B0%EC%82%B0%EC%9E%90-%EC%82%AC%EC%9A%A9-%EC%A7%80%EC%96%91%ED%95%98%EA%B8%B0)<br>
[11. Nullish coalescing operator (널 병합 연산자)](#11-nullish-coalescing-operator-%EB%84%90-%EB%B3%91%ED%95%A9-%EC%97%B0%EC%82%B0%EC%9E%90)<br>
[12. 드모르간의 법칙](#12-%EB%93%9C%EB%AA%A8%EB%A5%B4%EA%B0%84%EC%9D%98-%EB%B2%95%EC%B9%99)<br>

---

## 1. **값식문**
**NOTE**
- 인자는 값 혹은 식 이어야한다. => jsx 코드가 js로 트랜스 파일링 되면서 인자 값에 값과 식만 들어가기 때문.

```jsx
// JSX:
ReactDOM.render( // <- ()는 함수를 뜻한다.
    <div id="msg">Hello World!</div>
    , mountNode
);
// HTML태그와 mountNode를 파라미터로 받음 -> 바벨을 만나면서 밑의 코드로 트랜스 파일링됨.
ReactDOM.render(React.createElement(
    'div', 
    {id: 'msg'}, 
    'Hello World!'
    ), 
    mountNode
);
```

```jsx
// JSX:
ReactDOM.render( // condition에서 값을 반환하기 때문에 신택스 오류를 뱉지않음
// but if문과 같은 식을 넣게 되면 문법 오류를 뱉는다.
    <div id={condition?'msg':''}>Hello World!</div>
    , mountNode
);
```

```js
// 즉시 실행 함수(IIFE)를 사용함으로써 바로 값을 리턴 하기때문에 식을 넣어도 값처럼 사용가능
<p>
{(()=>{
    switch(this.state.color){
        case 'red':
            return '#FF0000';
        ...
    }
})}
</p>
//분기문 없이 값과 식으로도 충분히 표현가능하다.
```
```js
//좋지 않은 코드
function ReactComponent(){
    return(
        <tbody>
            {(()=>{
                const rows = [];
                
                for(let i=0;i<objectRows.length;i++){
                    rows.push(<ObjectRow key={i} data={objectRows[i]} />);
                }
                return rows;
            })()}
        </tbody>
    );
}

//값과 식으로 리팩토링 (배열 고차 함수 이용)
function ReactComponent(){
    return(
        <tbody>
            {objectRows.map((obj,i) => (
                <ObjectRow key={i} data={obj} />
            ))}
        </tbody>
    );
}
```
```js
//값과 식을 쓰지 않은 코드
function ReactComponent(){
    return(
        <div>
            {(()=>{
                if(conditionOne) return <span>One</span>
                if(conditionTwo) return <span>Two</span>
                else conditionOne;
                return <span>Three</span>
            })} 
        </div>
    )
}

function ReactComponent(){
    return(
        <div>
            {conditionOne && <span>One</span>} 
            {conditionTwo && <span>Two</span>} 
            {!conditionTwo && <span>Three</span>}
        </div>
    )
} 
```
**해결** 고차함수를 주요하게 활용해라

## 2. 삼항 연산자 다루기

```js
// 1번 삼항연산자
function example(){
    return condition1? value1
    : condition2 ? value2
    : condition3 ? value3
    : value4;
}
// 2번 if else 분기 처리
function example(){
    if(condition1){
        return value1;
    }else if(condition2){
        return value2;
    }
}...
```
```js
// 엣지케이스 컨트롤이 더 낫다.
switch(key){
    case value:
        break;
        ...
}
```
**NOTE**: 위와 같은 상황에서 모두 가독성도 좋지않고 유지보수 별로. 2번은 포매팅만해줘도 쓸만하지만 그래도 `switch`문이 낫다.


```js
const example = condition1
    ? ((a === 0)? 'zero':'positive')
        : 'negative'; 
```
```js
//nullable한 상황이다. 이런경우 이름없음 으로 출력되도록 삼항연산자를 통해 처리해줌
const welcomeMsg = (isLogin) => {
    const name = isLogin ? getName() : '이름없음';
    return `안녕하세요 ${name}`;
}
```
```js
// bad case
//  반환값이 없기때문에 void처럼 된다. 억지로 삼항연산자 사용한 케이스
function alertMsg(isAudult){
    isAudult
        ? alert('입장이 가능합니다'); //undefined
        : alert('입장이 불가능합니다.'); // undefined가 들어가는 꼴
}

//아래와 같이 값대입인 경우는 좋다.
function alertMsg(isAudult){
    isAudult
        ? '입장이 가능합니다'; 
        : '입장이 불가능합니다.';
}
//억지로 사용할 거면 그냥 ifelse로 포매팅 해라
```

**NOTE**: 삼항연산자는 값을 반환하거나 변수를 지정할때 사용하면 좋다.

---

## 3. **Truthy & Falsy**

- Truthy로 평가되는 문항들
```js
// Truthy 하게 처리되는 값들이 있음
if('string'.length){ //6이지만 truthy하기 때문에 true로 분기 처리됨
}
if(3.14){ // 같음
}
if({}){ // true
}
if([]){ // 빈 객체, 빈배열도 트루시하게 반환됨
}
if('0'){ // true
}
```
<Br>
- Falsy 문항들

```js
if(0){}
if(undefined){}
if(null){}
if(-0){}
if(0n){}
```

- 적용
```js
function printName(name){
    if(name === undefined || name === null){ // 코드가 길어짐 Truthy를 사용안하면 안들어가는 분기를 모두 체크해줘야됨
        return '사람이 없네요';
    }
    return  `안녕하세요 ${name}님`;
}

//리팩토링
function printName(name){
    if(name){ // Falsy적용
        return '사람이 없네요';
    }
}
```

## 4. 단축평가(short-circuit-evaluation)

```js
// AND
true && true && 도달 O
true && false && 도달 X // false에서 걸림

// OR
false || false || 도달 O 
true || true || 도달 X // 1항 2항에서 걸림
```

```js
//단축평가는 기본값을 줄때 제일 편리하다 다음을 리팩토링 해보자
function fetchData(){
    if(state.data){
        return state.data;
    }
    else{
        return 'Fetching...'
    }
}

//단축평가를 통한 리팩토링
function fetchData(){
    return state.data || 'Fetching...'
}
```

```js
function favoriteDog(someDog){
    let favoriteDog;
    if(someDog){
        favoriteDog = dog;
    }
    else{
        favoriteDog = '냐옹';
    }
    return favoriteDog+'입니다';
}

function favoriteDog(someDog){
    return (someDog || '냐옹')+ '입니다';
}
```
```js
const getActiveUserName(user, isLogin){
    if(isLogin){
        if(user){
            if(user.name){
                return user.name;
            }else{
                return '이름 없음';
            }
        }
    }
}

const getActiveUserName(user, isLogin){
    if(isLogin && user){
        return user.name || '이름 없음';
    }
}
```

---

## 5. else if를 피해라

- `else if`가 `promise`체인과 비슷하다고 생각하지만 전혀 다르다.
- 아예 사용하지 않거나 `switch`문으로 변경해라 (다를게 없지만 가독성이 올라감)
```js
//안티 패턴
const x = 1;

if(x >= 0){
    'x는 0과 같거나 크다';
}else if(x>0){
    'x는 0보다 크다';
}else{
    'Else';
}

//아래와 같이 표현 or switch case문 사용
if(x >= 0){
    'x는 0과 같거나 크다';
}
if(x>-){
    'x는 0보다 크다';
}
```

## 6. else를 피해라

- 단축평가로 간단하게 표현가능하다.
- 안써도 된다 그냥

```js
//1 안티패턴
const getActiveUserName(user){
    if(user.name){
        return user.name;
    }else{
        return '이름 없음';
    }
}
// 아래가 에센셜 패턴
const getActiveUserName(user){
    if(user.name){
        return user.name;
    }
    return '이름 없음';
}
```
**NOTE**: 스타일 상의 문제가 있을 뿐만 아니라, 반전된 기능을 하는 함수가 될 수 있다. 특히 하나의 함수가 여러 역할을 하게될 시에 문제가 생김

---

## 7. Early Return 

```js
// anti pattern
function loginService(isLogin, user){
    if(!isLogin){
        if(checkToken()){
            if(!user.nickname){
                return registerUser(user);
            }else{
                refreshToken();
                return '로그인 성공';
            }
        }
        else{
            throw new Error('no token');
        }
    }
}

//줄이기
function loginService(isLogin, user){
    // Early return
    /**
     * 함수를 일찍 종료시킨다
     * 로직은 같지만 사람이 보기 좋은 가독성 좋은 코드로 변경
     */
    if(isLogin){
        return;
    }
    if(!checkToken()){
        throw new Error('No Token');
    }
    
    if(!user.nickName){
        return registerUser();
    }
    refreshToken();
    return '로그인 성공';    
}
```
```js
//안티패턴
function 오늘하루(condition, weather, isJob){
    if(condition === 'GOOD'){
        공부();
        게임();
        유튜브보기();

        if(weather === 'GOOD'){
            운동();
            삘레();
        }

        if(isJob === 'GOOD'){
            야간업무();
            조기취침();
        }
    }
}

//리팩토링
function 오늘하루(condition, weather, isJob){
    if(condition !== 'GOOD'){
        return;
    }
    공부();
    게임();
    유튜브보기();

    if(weather === 'GOOD'){
        운동();
        삘레();
    }
    if(isJob === 'GOOD'){
        야간업무();
        조기취침();
    }
}
```

---

## 8. 부정 조건문 지양하기

- 생각을 여러번 해야할 수 있다.
- 언어 자체로 if문이 처음부터 오고 true부터 실행 시킴. -> 부정조건문을 죽여라
- 부정조건문을 사용할때는 보통 `Early Return` or `form validation`, `보안, 검사 로직`에서는 어쩔 수 없이 사용할 수 있다.

```js
const isCondition = true;
const isNotCondition = false;

if(isCondition){
    console.log('참된 경우입니다');
}

if(!isNaN(3)){ //-> isNaN();값이 false면 숫자이다(헷갈린다 부정조건문은)
    console.log('숫자임');
}
// isNumber()로 변경해서 직관적으로 작성해 주는 것도 괜찮다.
if(isNumber(3)){
    console.log('숫자입니다');
}

function isNumber(num){
    return !Number.isNaN(num) && typeof num === 'number';
}
```

---

## 9. Default Case 고려하기

- 엣지 케이스(디폴트 값)를 항상 설정해라.

```js
function sum(x,y){
    x = x || 1;
    y = y || 1;
    return x + y;
}
sum(); // 2
```
```js
function createElement(type, height, width){
    const element = document.createElement(type || 'div');
    element.style.height = height || 100;
    element.style.width = width || 100;

    return element;
}
createElement();
```
**NOTE**: 안전하고 확장성 있는 코드를 작성할 수 있다. 

```js
// 다음과 같은 사항에서도 꼭 필요하다. input 값이 다를 수 있다. 항상 신뢰하지 말것.
function registerDay(userInputDay){
    switch(userInputDay){
        case '월요일':
        case '화요일':
        case '수요일':
        case '목요일':
        case '금요일':
        case '토요일':
        case '일요일':
        default:
            throw new Error();
    }
}
```
```js
// parseInt의 radix는 10이 디폴트가 아니다. 안전하게 사용하는 함수.
function safeParseInt(number, radix){
    return parseInt(number, radix || 10);
}
```

---

## 10. 명시적인 연산자 사용 지양하기

- 연산자 우선순위를 사용해 사람이 보기 편하게 명시적으로 알수 있도록 해라.`()`를 잘 활용할 것

```js
function increment(number){
    --number; // 전위 연산자
    number--;  // 후위 연산자
    // 이런 전위 후위 연산자도 헷갈린다. - 디버깅이 힘들다; -> 예측하기 쉬운 코드를 작성할것

    //다음이 명시적이다.
    number = number - 1;
    number = number + 1;
}
```
**NOTE**: 예측가능하고 디버깅 하기 쉬운 코드를 항상 작성해라.(유지보수 가능한 코드)

---


## 11. Nullish coalescing operator (널 병합 연산자)

- `??` 연산자
- 비교적 최신 신택스

```js
function createElement(type, height, width){
    const element = document.createElement(type || 'div');
    element.style.height = height || 100;
    element.style.width = width || 100;

    return element;
}
const el = createElement('div', 0 , 0); 
// 다음은 height, width 가 100으로 들어간다. 이는 NULL로 처리되기때문에
// 0 이 Falsy로 처리되기 때문에

function createElement(type, height, width){
    const element = document.createElement(type ?? 'div');
    element.style.height = height ?? 100;
    element.style.width = width ?? 100;

    return element;
}
// 이제 null, undefined일 때만 동작하도록 한다.
// 0을 Truthy 반환
```
```js
//null과 undefined를 평가할때만 사용할것
function helloWorld(msg){
    if(!msg){
        return 'hello World';
    }
    return 'hello!'+(msg || 'World');
}
console.log(helloWorld(0)); // hello World
```
**NOTE**: Null병합 연산자와 단축 평가는 혼합해서 사용할 수 없다.(옵셔널 체이닝과 궁합이 오히려 좋다.(`?.`))
```js
function (){
    return isLogin && null ?? isToken(); // error
}

function (){
    return (isLogin && null) ?? isToken(); // good
}
```

---

## 12. 드모르간의 법칙

```js
const isValidUser = true; // <= 서버에서 받아옴
const isValidToken = true; // 서버에서 받아옴

if(isValidToken && isValidUser){
    console.log('로그인 성공');
}

// 위의 코드들은 검증된 코드안에서 재활용 할것이다.
// 로그인 실패를 넣는다면
if(!(isValidToken && isValidUser)){ // 너무 복잡해지는 문제가 있으며 또 변경사항이 생겼을때 대응하기 어렵다.
    console.log('로그인 실패');
}
// 쌍커풀을 벗겨낼 필요가있다.
```
**NOTE**: 위의 경우 드모르간의 법칙을 적용한다.

```js
//AND 부정
if(A && B){
    // 성공
}
if(!A || !B){
    //실패
}

// OR 부정
if(A || B){
    // 성공
}
if(!A && !B){
    //실패
}
```
```js
//true is not true
// false is not false
if(!isValidToken || !isValidUser){
    console.log('로그인 실패!');
}

if(!isValidToken && !isValidUser){ 
    console.log('로그인 실패');
}
```