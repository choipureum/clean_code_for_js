# 클린코드

## 1-1. 소개
### 1. 클린코드에 대해 고민하기
- 타인이 정의한 답을 의심하기
- 배움에 열린 태도 가지기
- 직접 생각하고 고민하기
- 클린 코드가 무엇인지 생각하기
- 흔히 알려진 코드 스타일에 대한 견해를 탐구

### 2. js 특성 이해하기
- **몽키패치**(Monkey patch) : 런타임 중에 동적으로 프로그램 내용이 변경되는 행동을 의미한다.
```js
typeof NaN // number
0.5+0.1==0.6 // true
0.1+0.2==0.3 // false
Math.max() // - Infinity
Math.min() // Infinity
[]+{} // "[object Object]"
{}+[] // 0
true+true+true ===3 //true
true-true = 0;
```

### 3. javascript everywhere
- 자바스크립트는 어디에서나 사용된다. 클린코드로 작성하면 더 안전하고 다양한 곳에서 사용가능
- electron : js로 데스크탑 애플리케이션 구축 프레임워크(vscode, facebook 메신저, 노션)

### 4. 사례를 통해 파악하기
- 케이스를 통해 코드에 대해 판단하기(에러케이스, 배드케이스)
```js
function hello(){
    return 'hello';
}
```
### 5. 좋은 코드 작성을 위한 의식적인 수련

**TDZ** : 일시적인 사각지대(스코프의 시작지점부터 초기화 시작 지점까지의 구간)