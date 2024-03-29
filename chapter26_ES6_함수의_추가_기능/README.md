# # chapter 26 : ES6 함수의 추가 기능

## 함수의 구분
ES6 이전까지 JS의 함수는 별다른 구분 없이 사용했음.  
JS 함수는 
- 일반적인 함수로 호출
- new 연산자와 함께 호출하여 인스턴스 생성할 수 있는 생성자 함수로서 호출
- 객체 바인딩되어 메서드로서 호출
- 객체 바인딩되어 메서드로서 호출

용도가 많아 실수 유발 및 성능 면에서도 손해이다.  
이러한 문제를 해결하기 위해 ES6에서는 함수를 사용 먹적에 따라 세 가지 종류로 명확히 구분했다.  
|ES6함수 의 구분 | CONSTRUCTOR | prototype | super | arugments |
|--------|:----------:|:--------:|:----------:|:-------:|
|일반 함수(normal) | O | O | X | O |
|메서드(Method) | X | X | O | O |
|화살표 함수(normal) | X | X | X | X |


일반 함수는 constructor 이지만 ES6의 메서드와 화살표 함수는 non-constructor다. 

## 메서드
ES6 사양에서 메서드는 메서드 축약 표현으로 정의된 함수만을 의미한다.
```js
const obj = {
    x: 1,
    // foo는 메서드다.
    foo() { return this.x; },
    // bar에 바인딩된 함수는 메서드가 아닌 일반 함수다.
    bar: function() { return this.x; }
};

console.log(obj.foo()); // 1
console.log(ojb.foo()); // 1
```
ES6 사양에서 정의한 메서드는 인스턴스를 생성할 수 없는 non-constructor이다. 따라서 생성자 함수로서 호출이 불가하다.  
ES6 메서드는 자신을 바인딩한 객체를 가리키는 내부 슬롯 [[HomeObject]]를 갖는다. super 참조는 내부 슬롯 [[HomeObejct]]를 사용하여 수퍼클래스의 메서드를 참조하므로 내부 슬롯 [[HomeObject]]를 갖는 ES6 메서드는 super 키워드를 사용할 수 있다.

## 화살표 함수
화살표 함수(arrow function)은 function 키워드 대신 화살표(=>, fat arrow)를 사용하여 기존의 함수 정의 방식보다 간략하게 함수를 정의할 수 있다.  
특히 화살표 함수는 콜백 함수 내부에서 this가 전역 객체를 가리키는 문제를 해결하기 위한 대안으로 유용하다.

### 화살표 함수 정의
#### 함수 정의
함수 선언문 x, 함수 표현식만 가능
```js
const multiply = (x, y) => x * y;
multiply(2, 3); // -> 6
```

#### 매개변수 선언
매개변수가 여러 개인 경우 소괄호 ()안에 매개변수를 선언
```js
const arrow = (x, y) => { ... };
// 1개인 경우 소괄호 생략 가능
const arrow = x => { ... };
// 매개변수가 없는 경우 소괄호 () 생략 x
```
#### 함수 몸체 정의
하나의 문으로 구성된다면 함수 몸체를 감싸는 중괄호 {}를 생략할 수 있다.  
함수 몸체를 감싸는 중괄호 {}를 생략한 경우 함수 몸체 내부의 문이 표현식이 아닌 문이라면 에러가 발생한다. 표현식이 아닌 문은 반환할 수 없기 때문  
```js
const arrow = () => const x =1; // SyntaxError: Unexpected toekn 'const'

//위 표현은 다음과 같이 해석됨
const arrow = () => { return const x = 1;};
```
따라서 함수 몸체가 하나의 문으로 구성된다 해도 함수 몸체의 문이 표현식이 아닌 문이라면 중괄호 생략 불가  
- 객체 리터럴 반환의 경우 소괄호 ()로 감싸주어야 함. 감싸 주지 않으면 {}를 함수 몸체를 감싸는 중괄호 {}로 해석함.  
- 화살표 함수도 즉시 실행함수로 사용할 수 있음
- 일급객체이므로 고차함수에 인수 전달할 수 있으며 표현이 간결하고 가독성이 좋다. 따라서 콜백 함수 정의 시 유용

### 화살표 함수와 일반 함수의 차이
1. 화살표 함수는 인스턴스를 생성할 수 없는 non-constructor다  

화살표 함수는 인스턴스를 생성할 수 없으므로 prototype 프러피타가 없고 프로토타입도 생성하지 않는다.

2. 중복된 매개변수 이름을 선언할 수 없다.
3. 화살표 함수는 함수 자체의 this, arguments, super, new.target 바인딩을 갖지 않는다.  

스코프 체인을 통해 상위 스코프를 참조. 만약 화살표 함수 중첩시 스코프 체인상 가장 가까운 상위 함수중에서 화살표 함수가 아닌 함수를 참조한다.

### this
화살표 함수의 this는 일반 함수의 this와 다르게 동작. 이는 "콜백 함수 내부의 this 문제", 즉 콜백 함수 내부의this가 외부 함수의 this와 다르기 때문에 발생하는 문제를 해결하기 위해 의도적으로 설계된 것이다.
 
과거 콜백 함수 내부의 this 문제는 다음과 같이 해결했다.
1. add 메서드를 호출한 prefixer 객체를 가리키는 this를 일단 회피시킨 후에 콜백 함수 내부에서 사용
2. 객체를 가리키는 this를 명확하게 지정
3. bind 메서드를 사용하여 this 바인딩

- ES6 부터는 화살표 함수를 사용하여 콜백 함수 내부의 this 문제를 해결할 수 있다.  
- **화살표 함수는 함수 자체의 this 바인딩을 갖지않는다. 따라서 화살표 함수 내부에서 this를 참조하면 상위 스코프의 this를 그대로 참조하는데, 이를 lexical this라 한다.**  
- ES6 메서드가 아닌 일반적인 의미의 메서드를 화살표 함수로 정의하는 것은 피해야 한다. 내부 this 가 아닌 상위 스코프의 this를 찾게 되고, 이는 전역 등 잘못된 곳을 참조할 수 있다.

### super
화살표 함수는 함수 자체의 super 바인딩을 갖지 않는다. 따라서 똑같이 상위 스코프의 super를 참조한다.  
### arguments
함수 자체의 arguments 바인딩을 갖지 않음. 따라서 this와 마찬가지로 상위 스코프의 arguments를 참조

## Rest 파라미터
### 기본 문법
Rest 파라미터(나머지 매개변수)는 매개변수 이름 앞에 세개의 점 ...을 붙여서 정의한 매개변수를 의미한다.  
Rest 파라미터는 함수에 전달된 인수들의 목록을 배열로 전달받는다. 
```js
function foo(...rest) {
    console.log(rest);
}

foo(1, 2, 3, 4, 5);
```
Rest 파라미터는 이름 그대로 먼저 선언된 매개변수에 할당된 인수를 제외한 나머지 인수들로 구성된 배열이 할당된다. 따라서 Rest 파라미터는 반드시 마지막 파라미터이어야 한다. 또한 한 개만 선언할 수 있다.

### Rest 파라미터와 arguments 객체
- ES5에서는 arguments 객체를 활용하여 인수를 전달받았다. 하지만 유사 배열 객체이기 때문에 call, apply 메서드를 사용해 객체를 배열로 반환해야 하는 번거로움이 있었다.  
- ES6에서는 rest 파라미터의 사용으로 arguments 객체를 배열로 변환하는 번거로움을 피할 수 있다.
- 화살표 함수는 함수 자체의 arguments 객체를 갖지 않으므로 반드시 Rest 파라미터를 사용해야 한다.

## 매개변수 기본값
- ES6에서 도입된 매개변수 기본값을 사용하면 함수 내에서 수행하던 인수 체크 및 초기화를 간소화할 수 있다.
- 인수를 전달하지 않은 경우와 undefined를 전달한 경우에만 유효하다. 
- Rest에는 기본값을 지정할 수 없다.
- 객체의 length 프로퍼티와 arguments 객체에는 아무런 영향을 주지 않는다.
