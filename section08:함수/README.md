# 함수 다루기

[1. `함수, 메서드, 생성자`](#1.`함수, 메서드, 생성자`)<br>
[2. `Computed Property Name`](#2.`Computed-Property-Name`)<br>


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













