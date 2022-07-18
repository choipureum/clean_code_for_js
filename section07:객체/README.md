# 객체 다루기

[1. `Shorthand Properties`](#1.`Shorthand Properties`)<br>
[2. `Computed Property Name`](#2.`Computed Property Name`)<br>
[3. `Lookup Table`](#3.`Lookup Table`(순람표))<br>
[4. `Object Destructuring`](#4.`Object Destructuring`)<br>
[5. `Object Freeze`](#4.`Object Freeze`)<br>




---

## 1.`Shorthand Properties`

1. `css`에서의 shorthand properties
```css
.before{
    background-color: #000;
    background-image: url('images/bg.gif');
}
.after{
    background: #000 url('images/bg.gif');
}
```
2. `js`에서의 shorthand properties

```js
const person = {
    firstName: 'poco', // firstName,lastName,
    lastName: 'jang',
    getFullName(){ //concise메소드
        return this.firstName + ' '+ this.lastName;
    }
}
```

---


## 2.`Computed Property Name`

1. React
```js
const [state, setState] = useState({
    id: '',
    password: '',
});

const handleChange = (e) => {
    setState({
        [e.target.name] : e.target.value; //동적인 key 대응 가능
    })
}
return(
    <React.Fragment>
        <input value={state.id} onChange={handleChange} name='name'/>
        <input value={state.password} onChange={handleChange} name='password'/>
    </React.Fragment>
)
```

2. Vue - Vuex 
```js
const store = new Vuex.store({
    state:{},
    mutations:{
        [SOME_MUTATION](state){}, // [함수명]() {함수몸체}
    }
});

//이거는 아래와 같다.

const obj = {
    [functionName_0](){
        return 'function-0';
    },    
    [functionName_1](){
        return 'function-1';
    },    
    [functionName_2](){
        return 'function-2';
    }
}
for(let i=0;i<3;i++){
    console.log(obj[`functionName${i}`]());
}
```

3. React - Reduce
```js
const noop = createAction('INCREMENT');

const reducer = handleActions(
    {
        [noop]: (state,action)=>({
            counter: state.couter + action.payload,
        }),
    },
    { counter: 0 },
);

```

---

## 3.`Lookup Table`(순람표)

- `if`나 `switch-case`로 너무 늘어질때 사용해라
```js
/**
 * @name 이전
 */
function getUserType(type){
    switch(type){
        case 'ADMIN':
            return '관리자';
        case 'STUDENT':
            return '학생';
        case 'INSTRUCTOR':
            return '강사';
        default:
            return '해당없음';
    }
}

/**
 * @name Lookup
 */
function getUserType(type){
    const USER_TYPE = { //상수는 스네이크케이스
        ADMIN: '관리자',
        INSTRUCTOR: '강사',
        STUDENT: '수강생',
        UNDEFINED: '해당 없음;'
    }
    return USER_TYPE[type] ?? USER_TYPE[USER_TYPE.UNDEFINED];
}
// 리팩토링 바로 리턴
function getUserType(type){
    return(
        {
            ADMIN: '관리자',
            INSTRUCTOR: '강사',
            STUDENT: '수강생',
        }[type] ?? '해당없음'
    );
}

```

---

## 4.`Object Destructuring`(구조분해 할당)

```js
/**
 * @name 개선 이전
 * @param name
 * @param age
 * @param location
 * @constructor
 */
function Person(name, age, location){
    this.name = name;
    this.age = age;
    this.location = location;
}

const poco = new Person('poco',30, 'korea');


/**
 * @name 개선 이후
 * @param name
 * @param age
 * @param location
 * @constructor
 */
function Person({name, age, location}){
    this.name = name;
    this.age = age;
    this.location = location;
}

const poco = new Person({ 
    name: 'poco', 
    age: 30, 
    location:'korea'
});
```
**NOTE** : 
1. 순서를 안지켜도된다<br>
2. 누가봐도 명확한 코드<br>

```js
//리팩토링
function Person(name, {age, location}){
    this.name = name;
    this.age = age;
    this.location = location;
}
const options = {
    age: 30,
    location:'korea'
}

const poco = new Person('poco', options);
```

```js
/**
 * @name 개선 전
 * @type {string[]}
 */
const orders = ['first', 'secode', 'third'];

const [first,  , third] = orders;

/**
 * @name 개선후
 */
const { 0: first, 2: third } = orders;
console.log(first);
console.log(third);
```

---

## 5. `Object Freeze`(객체동결)

- 객체를 안전하게 사용할 수 있다.
- `typescript`의 `readonly`기능
```js
const STATUS = Object.freeze({
    PENDING: 'PENDING',
    SUCCESS : 'SUCCESS',
    FAIL: 'FAIL',
    OPTIONS:{
        GREEN : 'GREEN',
        RED: 'RED',
    }
});

STATUS.NEW_PROP = 'P2'; // 추가 안된다.
console.log(STATUS); //PENDING, SUCCESS, FAIL , undefined

`isFROZEN` : 객체 동결 여부
Object.isFrozen(STATUS.PENDING); //true
Object.isFrozen(STATUS.SUCCESS); //true
Object.isFrozen(STATUS.FAIL); //true

Object.isFrozen(STATUS.OPTIONS); //false : 깊은 프리즈는 안된다
STATUS.OPTIONS.GREEN = 'G';
STATUS.OPTIONS.YELLOW = 'Y';
delete STATUS.OPTIONS.RED;
STATUS.OPTIONS; // {GREEN : 'G', YELLOW: 'Y'}
```

**NOTE** : `deepFreeze`는 함수로 구현해서 사용해라
```js
function deepFreeze(targetObj){
    //1. 객체 순회
    //2. 값이 객체인지 확인
    //3. 객체면 재귀
    //4. 객체 아니면 Object.Freeze
    Object.keys(targetObj).forEach(key=>{
        if(/*객체가 맞으면*/){
            deepFreeze(targetObj[key]);
        }
    })
    return Object.freeze(targetObj);
}
```






